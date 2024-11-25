文章来源：https://juejin.cn/post/7388278660149641266

# 一文搞懂MySQL分布式锁实现
## 前言

如果抛开性能不谈，仅仅只是需要分布式锁的功能，其实使用MySQL来实现分布式锁是一个不错的选择。

本文将基于MySQL的排它锁机制，实现一个简单的具备重入功能的分布式锁，并且将提供两种思路以供参考。

源码地址：distributed-lock

## 正文
### 一. 思路分析

加排它锁有如下两种思路以供参考。

1.SELECT FOR UPDATE。

```sql
SELECT 
    id,lock_key
FROM
    distributed_locks_impl_1
WHERE
    lock_key="common_key"
FOR UPDATE;
```

执行上述语句后，lock_key等于common_key的记录就会被添加行锁，其它线程再对相同记录执行上述语句就会被阻塞，直到行锁被释放。

2.**INSERT**。执行INSERT语句来插入数据，在插入数据的过程中是会添加行锁的，也就是只会有一个线程能插入记录成功，后续其它线程如果再插入相同的记录，会失败。

通过上述两种思路，是可以实现同一时刻只有一个线程获取到锁的效果的，现在再来思考一下如何重入。

其实要实现重入很简单，可以在代码层面进行处理，最简单的思路就是获取到锁的线程，将锁通过ThreadLocal进行保存，那么后续在同一个线程中，可以通过ThreadLocal来判断是否获取过锁，获取过就允许再次获取，以此来实现锁重入的效果。

## 二. 实现准备
既然是基于MySQL来实现分布式锁，那么肯定需要记录锁的表，我们提前准备好如下两张表，分别对应两种不同的实现方式。

1.SELECT FOR UPDATE

```sql
# 创建表
CREATE TABLE `distributed_locks_impl_1` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键Id',
  `lock_key` varchar(255) DEFAULT NULL COMMENT '锁的键',
  PRIMARY KEY (`id`),
  UNIQUE KEY `lock_key` (`lock_key`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

# 提前插入锁记录
INSERT INTO `distributed_locks_impl_1` VALUES (1, 'common_key');
INSERT INTO `distributed_locks_impl_1` VALUES (2, 'extend_key');
```

2.INSERT

```sql
CREATE TABLE `distributed_locks_impl_2` (
  `lock_key` varchar(255) NOT NULL COMMENT '主键',
  `create_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '记录创建时间',
  PRIMARY KEY (`lock_key`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

## 三. 实现与分析
完整的实现需要如下依赖。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.31</version>
</dependency>

<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>transmittable-thread-local</artifactId>
    <version>2.11.4</version>
</dependency>

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>

<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <scope>test</scope>
</dependency>
```

首先给出通过 SELECT FOR UPDATE 实现分布式锁的完整实现。

```java
@Slf4j
public class DistributedLockUtilImpl1 {

    private static final String USER_NAME = "root";
    private static final String PASS_WORD = "root";
    private static final String JDBC_URL = "jdbc:mysql://localhost:3306/test";
    private static final String DRIVER_CLASS_NAME = "com.mysql.cj.jdbc.Driver";

    private static volatile DataSource dataSource = null;

    private static final String SQL_SELECT_FOR_UPDATE = "SELECT id,lock_key FROM distributed_locks_impl_1 WHERE lock_key=\"%s\" FOR UPDATE;";

    private static final Map<String, LockManager> LOCK_MANAGER_MAP = new ConcurrentHashMap<>();

    private static final int SECONDS_CONTINUE_WAIT = 0;
    private static final int SECONDS_BRIEF_WAIT = 1;

    static {
        LOCK_MANAGER_MAP.put(LockKey.COMMON_KEY.getKey(), new LockManager());
        LOCK_MANAGER_MAP.put(LockKey.EXTEND_KEY.getKey(), new LockManager());
    }

    /**
     * 加指定{@link LockKey}的锁。<br/>
     * 如果获取不到锁，会持续等待。
     *
     * @param lockKey 见{@link LockKey}。
     */
    public static void lock(LockKey lockKey) {
        lock(lockKey, SECONDS_CONTINUE_WAIT);
    }

    /**
     * 加指定{@link LockKey}的锁。<br/>
     *
     * @param lockKey 见{@link LockKey}。
     * @param waitSeconds 等待时间，单位s。如果设置为0，则表示持续等待，
     *                    不允许设置为小于0。
     * @return true表示加锁成功；
     *          false表示加锁失败。
     */
    public static boolean lock(LockKey lockKey, int waitSeconds) {
        if (waitSeconds < 0) {
            return false;
        }

        LockManager lockManager = LOCK_MANAGER_MAP.get(lockKey.getKey());

        if (null == lockManager) {
            return false;
        }

        TransmittableThreadLocal<Connection> ttl = lockManager.getTtl();

        if (null != ttl.get()) {
            // 锁重入场景
            lockManager.reentrant();
            return true;
        }

        Connection connection = null;
        try {
            connection = getConnection();
            doLock(connection, lockKey, waitSeconds);
            ttl.set(connection);
            return true;
        } catch (Exception e1) {
            log.error("lock failed", e1);
            try {
                if (null != connection) {
                    connection.rollback();
                    connection.close();
                }
            } catch (Exception e2) {
                log.error("release connection failed", e2);
            } finally {
                ttl.remove();
            }
            return false;
        }
    }

    public static boolean tryLock(LockKey lockKey) {
        return lock(lockKey, SECONDS_BRIEF_WAIT);
    }

    /**
     * 释放锁。
     *
     * @param lockKey 见{@link LockKey}。
     * @return true表示释放成功；
     *          false表示释放失败或者未持有锁时释放。
     */
    public static boolean unLock(LockKey lockKey) {
        LockManager lockManager = LOCK_MANAGER_MAP.get(lockKey.getKey());

        if (null == lockManager) {
            return false;
        }

        TransmittableThreadLocal<Connection> ttl = lockManager.getTtl();
        Connection connection = ttl.get();
        if (connection == null) {
            return false;
        }

        AtomicInteger lockCount = lockManager.getReentrantCount();
        if (lockCount.get() > 0) {
            // 释放重入锁场景
            lockManager.unReentrant();
            return true;
        }

        try {
            connection.commit();
            return true;
        } catch (Exception e) {
            log.error("release lock failed", e);
            return false;
        } finally {
            try {
                connection.close();
            } catch (Exception e) {
                log.error("release connection failed", e);
            }
            ttl.remove();
        }
    }

    private static void doLock(Connection connection, LockKey lockKey, int queryTimeoutSeconds) throws SQLException {
        connection.setAutoCommit(false);
        try (Statement statement = connection.createStatement()) {
            statement.setQueryTimeout(queryTimeoutSeconds);
            statement.executeQuery(String.format(SQL_SELECT_FOR_UPDATE, lockKey.getKey()));
        }
    }

    private static Connection getConnection() throws SQLException {
        if (null == dataSource) {
            synchronized (DistributedLockUtilImpl1.class) {
                if (null == dataSource) {
                    dataSource = new HikariDataSource();
                    ((HikariDataSource) dataSource).setUsername(USER_NAME);
                    ((HikariDataSource) dataSource).setPassword(PASS_WORD);
                    ((HikariDataSource) dataSource).setJdbcUrl(JDBC_URL);
                    ((HikariDataSource) dataSource).setDriverClassName(DRIVER_CLASS_NAME);
                    return dataSource.getConnection();
                }
            }
        }
        return dataSource.getConnection();
    }

    public static class LockManager {
        private final TransmittableThreadLocal<Connection> ttl;
        private final AtomicInteger reentrantCount;

        public LockManager() {
            ttl = new TransmittableThreadLocal<>();
            reentrantCount = new AtomicInteger(0);
        }

        public TransmittableThreadLocal<Connection> getTtl() {
            return ttl;
        }

        public AtomicInteger getReentrantCount() {
            return reentrantCount;
        }

        public void reentrant() {
            reentrantCount.incrementAndGet();
        }

        public void unReentrant() {
            reentrantCount.decrementAndGet();
        }
    }

    public enum LockKey {
        COMMON_KEY("common_key"),
        EXTEND_KEY("extend_key");

        private final String key;

        LockKey(String key) {
            this.key = key;
        }

        public String getKey() {
            return key;
        }
    }

}
```

这里对上述的实现做一下说明。

首先加的锁是通过LockKey来指定，LockKey的枚举数量以及key的值需要和我们事先准备好的锁的表里面的数据一一对应。

其次加锁的动作就是执行一条带 FOR UPDATE 的查询语句，为了实现加锁超时等待的效果，可以在执行语句时对Statement设置超时时间，鉴于此我们就可以实现三种加锁效果即加锁一直等待，加锁超时等待和加锁立即返回。

最后就是锁重入的实现是依赖于我们定义的LockManager，每一个LockManager对应一把锁，每一个获取到锁的线程，会将加锁使用的Connection交由这把锁对应的LockManager管理，这里的管理其实就是把Connection通过LockManager的ThreadLocal设置为线程本地变量，方便后续在解锁时拿到对应的Connection来提交事务以释放锁。当发生锁重入时，LockManager会记录重入次数，重入多少次锁，就需要释放多少次锁。

现在再给出通过 INSERT 实现分布式锁的完整实现。

```java
@Slf4j
public class DistributedLockUtilImpl2 {

    private static final String USER_NAME = "root";
    private static final String PASS_WORD = "root";
    private static final String JDBC_URL = "jdbc:mysql://localhost:3306/test";
    private static final String DRIVER_CLASS_NAME = "com.mysql.cj.jdbc.Driver";

    private static volatile DataSource dataSource = null;

    private static final String SQL_INSERT_RECORD = "INSERT INTO distributed_locks_impl_2(lock_key) VALUES (\"%s\");";
    private static final String SQL_DELETE_RECORD = "DELETE FROM distributed_locks_impl_2 WHERE lock_key=\"%s\";";

    private static final Map<String, LockManager> LOCK_MANAGER_MAP = new ConcurrentHashMap<>();

    private static final int SECONDS_CONTINUE_WAIT = 0;
    private static final int SECONDS_BRIEF_WAIT = 1;

    static {
        LOCK_MANAGER_MAP.put(LockKey.COMMON_KEY.getKey(), new LockManager());
        LOCK_MANAGER_MAP.put(LockKey.EXTEND_KEY.getKey(), new LockManager());
    }

    /**
     * 加指定{@link LockKey}的锁。<br/>
     * 如果获取不到锁，会持续等待。
     *
     * @param lockKey 见{@link LockKey}。
     */
    public static void lock(LockKey lockKey) {
        lock(lockKey, SECONDS_CONTINUE_WAIT);
    }

    /**
     * 加指定{@link LockKey}的锁。<br/>
     *
     * @param lockKey 见{@link LockKey}。
     * @param waitSeconds 等待时间，单位s。如果设置为0，则表示持续等待，
     *                    不允许设置为小于0。
     * @return true表示加锁成功；
     *          false表示加锁失败。
     */
    public static boolean lock(LockKey lockKey, int waitSeconds) {
        if (waitSeconds < 0) {
            return false;
        }

        LockManager lockManager = LOCK_MANAGER_MAP.get(lockKey.getKey());

        if (null == lockManager) {
            return false;
        }

        TransmittableThreadLocal<Connection> ttl = lockManager.getTtl();

        if (null != ttl.get()) {
            // 锁重入场景
            lockManager.reentrant();
            return true;
        }

        Connection connection = null;
        try {
            connection = getConnection();
            doLock(connection, lockKey, waitSeconds);
            ttl.set(connection);
            return true;
        } catch (Exception e1) {
            log.error("lock failed", e1);
            try {
                if (null != connection) {
                    connection.rollback();
                    connection.close();
                }
            } catch (Exception e2) {
                log.error("release connection failed", e2);
            } finally {
                ttl.remove();
            }
            return false;
        }
    }

    public static boolean tryLock(LockKey lockKey) {
        return lock(lockKey, SECONDS_BRIEF_WAIT);
    }

    /**
     * 释放锁。
     *
     * @param lockKey 见{@link LockKey}。
     * @return true表示释放成功；
     *          false表示释放失败或者未持有锁时释放。
     */
    public static boolean unLock(LockKey lockKey) {
        LockManager lockManager = LOCK_MANAGER_MAP.get(lockKey.getKey());

        if (null == lockManager) {
            return false;
        }

        TransmittableThreadLocal<Connection> ttl = lockManager.getTtl();
        Connection connection = ttl.get();
        if (connection == null) {
            return false;
        }

        AtomicInteger lockCount = lockManager.getReentrantCount();
        if (lockCount.get() > 0) {
            // 释放重入锁场景
            lockManager.unReentrant();
            return true;
        }

        try {
            doUnlock(connection, lockKey);
            return true;
        } catch (Exception e) {
            log.error("release lock failed", e);
            return false;
        } finally {
            try {
                connection.close();
            } catch (Exception e) {
                log.error("release connection failed", e);
            }
            ttl.remove();
        }
    }

    private static void doLock(Connection connection, LockKey lockKey, long waitSeconds) {
        long startMilliseconds = System.currentTimeMillis();
        boolean lockSuccess = false;
        while (!lockSuccess && (waitSeconds == 0 || System.currentTimeMillis() - startMilliseconds <= waitSeconds * 1000)) {
            try (Statement statement = connection.createStatement()) {
                lockSuccess = statement.executeUpdate(String.format(SQL_INSERT_RECORD, lockKey.getKey())) == 1;
            } catch (Exception ignore) {
                LockSupport.parkNanos(1000 * 1000 * 100);
            }
        }
        if (!lockSuccess) {
            throw new RuntimeException("do lock failed");
        }
    }

    private static void doUnlock(Connection connection, LockKey lockKey) {
        boolean unlockSuccess = false;
        try (Statement statement = connection.createStatement()) {
            unlockSuccess = statement.executeUpdate(String.format(SQL_DELETE_RECORD, lockKey.getKey())) == 1;
        } catch (Exception e) {
            log.error("delete record failed", e);
        }
        if (!unlockSuccess) {
            throw new RuntimeException("do unlock failed");
        }
    }

    private static Connection getConnection() throws SQLException {
        if (null == dataSource) {
            synchronized (DistributedLockUtilImpl2.class) {
                if (null == dataSource) {
                    dataSource = new HikariDataSource();
                    ((HikariDataSource) dataSource).setUsername(USER_NAME);
                    ((HikariDataSource) dataSource).setPassword(PASS_WORD);
                    ((HikariDataSource) dataSource).setJdbcUrl(JDBC_URL);
                    ((HikariDataSource) dataSource).setDriverClassName(DRIVER_CLASS_NAME);
                    return dataSource.getConnection();
                }
            }
        }
        return dataSource.getConnection();
    }

    public static class LockManager {
        private final TransmittableThreadLocal<Connection> ttl;
        private final AtomicInteger reentrantCount;

        public LockManager() {
            ttl = new TransmittableThreadLocal<>();
            reentrantCount = new AtomicInteger(0);
        }

        public TransmittableThreadLocal<Connection> getTtl() {
            return ttl;
        }

        public AtomicInteger getReentrantCount() {
            return reentrantCount;
        }

        public void reentrant() {
            reentrantCount.incrementAndGet();
        }

        public void unReentrant() {
            reentrantCount.decrementAndGet();
        }
    }

    public enum LockKey {
        COMMON_KEY("common_key"),
        EXTEND_KEY("extend_key");

        private final String key;

        LockKey(String key) {
            this.key = key;
        }

        public String getKey() {
            return key;
        }
    }

}
```

上述实现相较于第一种实现，主要区别就在于加锁和解锁方式的不同，上述实现中加锁就是通过插入一条锁记录，而解锁就是删除插入的锁记录。

## 四. 功能测试
首先编写单元测试对 SELECT FOR UPDATE 方式实现的分布式锁进行功能测试。

```java
public class DistributedLockUtilImpl1Test {

    @Test
    public void 简单测试多线程下加锁效果() throws InterruptedException {
        CountDownLatch countDownLatch = new CountDownLatch(2);

        new Thread(() -> {
            DistributedLockUtilImpl1.LockKey lockKey = DistributedLockUtilImpl1.LockKey.COMMON_KEY;

            if (DistributedLockUtilImpl1.tryLock(lockKey)) {
                try {
                    System.out.println(Thread.currentThread().getName() + " -> hold the lock for 10 seconds");
                    LockSupport.parkNanos(1000 * 1000 * 1000 * 10L);
                    System.out.println(Thread.currentThread().getName() + " -> now release the lock");
                } finally {
                    boolean unlockSuccess = DistributedLockUtilImpl1.unLock(lockKey);
                    if (!unlockSuccess) {
                        System.out.println(Thread.currentThread().getName() + " -> release lock failed");
                    }
                }
            } else {
                System.out.println(Thread.currentThread().getName() + " -> failed to hold the lock");
            }

            countDownLatch.countDown();
        }, "thread-1").start();

        LockSupport.parkNanos(1000 * 1000 * 100);

        new Thread(() -> {
            DistributedLockUtilImpl1.LockKey lockKey = DistributedLockUtilImpl1.LockKey.COMMON_KEY;

            DistributedLockUtilImpl1.lock(lockKey);
            try {
                System.out.println(Thread.currentThread().getName() + " -> hold the lock for 10 seconds");
                LockSupport.parkNanos(1000 * 1000 * 1000 * 10L);
                System.out.println(Thread.currentThread().getName() + " -> now release the lock");
            } finally {
                boolean unLockSuccess = DistributedLockUtilImpl1.unLock(lockKey);
                if (!unLockSuccess) {
                    System.out.println(Thread.currentThread().getName() + " -> release lock failed");
                }
            }
            countDownLatch.countDown();
        }, "thread-2").start();

        countDownLatch.await();
    }

}
```

运行测试程序，打印如下。

```txt
thread-1 -> hold the lock for 10 seconds
thread-1 -> now release the lock
thread-2 -> hold the lock for 10 seconds
thread-2 -> now release the lock
```

现在再编写单元测试对 INSERT 方式实现的分布式锁进行功能测试。

```java
public class DistributedLockUtilImpl2Test {

    @Test
    public void 简单测试多线程下加锁效果() throws InterruptedException {
        CountDownLatch countDownLatch = new CountDownLatch(2);

        new Thread(() -> {
            DistributedLockUtilImpl2.LockKey lockKey = DistributedLockUtilImpl2.LockKey.COMMON_KEY;

            if (DistributedLockUtilImpl2.tryLock(lockKey)) {
                try {
                    System.out.println(Thread.currentThread().getName() + " -> hold the lock for 10 seconds");
                    LockSupport.parkNanos(1000 * 1000 * 1000 * 10L);
                    System.out.println(Thread.currentThread().getName() + " -> now release the lock");
                } finally {
                    boolean unlockSuccess = DistributedLockUtilImpl2.unLock(lockKey);
                    if (!unlockSuccess) {
                        System.out.println(Thread.currentThread().getName() + " -> release lock failed");
                    }
                }
            } else {
                System.out.println(Thread.currentThread().getName() + " -> failed to hold the lock");
            }

            countDownLatch.countDown();
        }, "thread-1").start();

        LockSupport.parkNanos(1000 * 1000 * 100);

        new Thread(() -> {
            DistributedLockUtilImpl2.LockKey lockKey = DistributedLockUtilImpl2.LockKey.COMMON_KEY;

            DistributedLockUtilImpl2.lock(lockKey);
            try {
                System.out.println(Thread.currentThread().getName() + " -> hold the lock for 10 seconds");
                LockSupport.parkNanos(1000 * 1000 * 1000 * 10L);
                System.out.println(Thread.currentThread().getName() + " -> now release the lock");
            } finally {
                boolean unLockSuccess = DistributedLockUtilImpl2.unLock(lockKey);
                if (!unLockSuccess) {
                    System.out.println(Thread.currentThread().getName() + " -> release lock failed");
                }
            }
            countDownLatch.countDown();
        }, "thread-2").start();

        countDownLatch.await();
    }
    
}
```

运行测试程序，打印如下。

```txt
thread-1 -> hold the lock for 10 seconds
thread-1 -> now release the lock
thread-2 -> hold the lock for 10 seconds
thread-2 -> now release the lock
```

## 五. 存在的问题
上面两种实现方式，其实均有一些比较明显的问题。

第一个问题就是如果持有锁的线程没有释放锁怎么办。

对于 SELECT FOR UPDATE 方式实现的分布式锁，如果持有锁的实例突然挂掉，导致对应的Connection没有进行事务提交，此时对应的锁记录就会被一直锁住，直到超时，这个问题目前没有好的解决办法（如果有请在评论区告诉我），但是换个思路想，现在的云上应用的pod实例，就算是要杀掉pod重建实例，也是会给应用进程一个优雅退出的时间，在优雅退出时，数据源是会将所有连接进行销毁的，也就不存在持有锁不释放的问题。

对于 INSERT 方式实现的分布式锁，如果持有锁的实例突然挂掉，导致锁记录无法被删除，此时也会造成锁记录一直被锁住，而且就算是有优雅退出，也无法解决这个问题。其实对于 INSERT 方式，解决这种情况有一种简单粗暴的方法，那就是起一个分布式定时任务，每间隔一定时间去判断锁记录是否超时，如果超时，就删除这条记录，以及时将锁释放出来。

第二个问题就是加锁效率都不高。

这个问题是没有办法解决的，如果为了追求加锁效率，可以使用Redis，其次可以选择Zookeeper或者Etcd，但是如果只给你MySQL，而又要实现分布式锁的功能，那效率再低也得忍。
