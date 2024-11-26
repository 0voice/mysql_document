# Tracking Dual Passwords in MySQL
We already have blog posts about Dual Password in MySQL from Brian Sumpter – Using MySQL 8 Dual Passwords, and from Marco Tusa – MySQL Dual Passwords – How To Manage Them Programmatically

Let’s skip the details about dual passwords and focus on tracking password usage.

## How can we be sure that we are using the new password?
We received so many questions related to this feature, and here they are;

How can I keep track of the passwords I am using?

Is there a way to check if my application still works with the old password?

When changing the application password while utilizing the dual password feature, it’s important to determine which password is currently in use and whether the application is still relying on the old one. There aren’t many places to check for confirmation on which password is being used.

After reviewing the code, there is a way to check, but it’s an insufficient way:

```
if (!mpvio->acl_user->credentials[PRIMARY_CRED].m_salt_len ||
        check_scramble(pkt, mpvio->scramble,
                       mpvio->acl_user->credentials[PRIMARY_CRED].m_salt)) {
      second = true;
      if (!mpvio->acl_user->credentials[SECOND_CRED].m_salt_len ||
          check_scramble(pkt, mpvio->scramble,
                         mpvio->acl_user->credentials[SECOND_CRED].m_salt)) {
        return CR_AUTH_USER_CREDENTIALS;
      } else {
        if (second) {
          MPVIO_EXT *mpvio_second = pointer_cast<MPVIO_EXT *>(vio);
          const char *username =
              *info->authenticated_as ? info->authenticated_as : "";
          const char *hostname = mpvio_second->acl_user->host.get_host();
          LogPluginErr(
              INFORMATION_LEVEL,
              ER_MYSQL_NATIVE_PASSWORD_SECOND_PASSWORD_USED_INFORMATION,
              username, hostname ? hostname : "");
```

If an old password is used, it will be logged in performance_schema.error_log if the log verbosity is set to 3.

To identify users who have an additional password:

```
mysql> SELECT User, Host, plugin, JSON_KEYS(User_attributes) FROM mysql.user WHERE User_attributes->>'$.additional_password' <> '';
+----------+-------------+-----------------------+----------------------------+
| User     | Host        | plugin                | JSON_KEYS(User_attributes) |
+----------+-------------+-----------------------+----------------------------+
| appuser1 |           % | caching_sha2_password | ["additional_password"]    |
+----------+-------------+-----------------------+----------------------------+
1 row in set (0.00 sec)
```

Here is what it looks like:

```
mysql> SELECT * FROM performance_schema.error_log WHERE ERROR_CODE IN ('MY-013302','MY-013300')G
*************************** 1. row ***************************
    LOGGED: 2024-10-17 07:23:14.877579
 THREAD_ID: 17
      PRIO: Note
ERROR_CODE: MY-013302
 SUBSYSTEM: Server
      DATA: Plugin caching_sha2_password reported: 'Second password was used for login by user: 'appuser1'@'%'.'
1 row in set (0.00 sec)

$ perror 13302
MySQL error code MY-013302 (ER_CACHING_SHA2_PASSWORD_SECOND_PASSWORD_USED_INFORMATION): Second password was used for login by user: '%s'@'%s'.
$ perror 13300
MySQL error code MY-013300 (ER_MYSQL_NATIVE_PASSWORD_SECOND_PASSWORD_USED_INFORMATION): Second password was used for login by user: '%s'@'%s'.
```

Note: It needs to increase verbosity 3 to see that information.

```
SET GLOBAL log_error_verbosity=3;
```

## Conclusion
Dual password support in MySQL 8.0 is an essential feature, while password rotation simplifies compliance with security policies. However, checking which password was used is still hard to detect, and my colleague Yoann created a feature request for this. So, keep an eye on feature request  #115973 and ensure you test dual passwords thoroughly in your environment before adopting them in production.

**MySQL Performance Tuning** is an essential guide covering the critical aspects of MySQL performance optimization.
