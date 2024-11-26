原文链接：https://www.percona.com/blog/exploring-mysql-8-priority-based-error-log-filtering/


# Exploring MySQL 8 Priority-Based Error Log Filtering

December 13, 2023

[Mani Paluru](https://www.percona.com/blog/author/mani-paluru)

Error logging is a critical aspect of database administration, providing insights into issues, warnings, and errors that may affect the system’s stability and performance. MySQL 8 introduces Error Log Filtering as a mechanism to fine-tune the error log, allowing administrators to focus on the most critical issues. By assigning priorities to different error types, administrators can control which errors are logged, creating a more concise and actionable error log.

In this blog post, we will explore one of the two error filtering methods, specifically focusing on priority-based error log filtering. Typically, error log configuration involves the inclusion of one log filter component and one or more log sink components. MySQL provides options for error log filtering through log_filter_internal, which facilitates error log filtering based on log event priority and error code in conjunction with the log_error_verbosity and log_error_suppression_list system variables. It’s important to note that log_filter_internal comes built-in and enabled by default, offering a comprehensive solution for error log filtering.

![image](https://github.com/user-attachments/assets/1a7e95b9-9afb-4029-a996-2e1e4885b4a7)

The priority-based error log filtering is divided into three types of filtering 

1. Verbosity filtering 
2.  Suppression-list filtering 
3. Verbosity and suppression-list interaction

### Verbosity filtering

Error log verbosity is controlled by the system variable log_error_verbosity, which is a dynamic variable designed to control the level of detail included in error messages recorded in the error log. By adjusting the verbosity level, one can tailor the amount of information logged, balancing the need for comprehensive diagnostics with the desire for a more concise log output.

**Verbosity levels:**

Log_error_verbosity supports different verbosity levels, ranging from one to three, each offering a different degree of detail in error messages.

- Level 1: Basic error information
- Level 2: Additional context information
- Level 3: Maximum detail, including full query information

| **log_error_verbosity Value** | **Permitted Message Priorities** |
| ----------------------------- | -------------------------------- |
| 1                             | ERROR                            |
| 2                             | ERROR, WARNING                   |
| 3                             | ERROR, WARNING, INFORMATION      |

Log_error_verbosity is set to 2 by default, which is helpful for logging the network failures and reconnections, and in case of a replica, it logs the details about its status. This includes information such as the starting coordinates in the binary log and relay log.

Lowering the verbosity level can significantly reduce the volume of information recorded in the error log. This is particularly beneficial in high-traffic environments where minimizing log noise is crucial for efficient log analysis.

Higher verbosity levels provide more detailed information, aiding administrators in diagnosing and troubleshooting issues more effectively. This is especially valuable during the investigation of complex problems or performance bottlenecks.

Additionally, there exists a message priority labeled SYSTEM, exempt from verbosity filtering. Messages categorized as SYSTEM, pertaining to non-error scenarios, are automatically logged in the error log, irrespective of the log_error_verbosity setting. Such messages encompass startup and shutdown notifications, as well as noteworthy alterations to system settings.

### Suppression-list filtering

Log_error_suppression_list is a dynamic system variable introduced in MySQL 8, aimed at selectively suppressing specific error messages from being written to the error log. This functionality allows administrators to tailor the content of the error log, focusing on critical errors while omitting less significant or expected messages. By maintaining a cleaner and more relevant error log, administrators can streamline the troubleshooting and monitoring processes.

The value of log_error_suppression_list can either be an empty string, indicating no suppression, or a list of one or more comma-separated values representing the error codes to be suppressed. These error codes can be specified in either symbolic or numeric form. When using numeric codes, the MY- prefix may be included or omitted, and leading zeros in the numeric part are not considered significant.

By default, log_error_suppression_list is set to empty. If we want to filter out the error message MY-010035, we can set it like this. To persist the values to disk, use set PERSIST instead of set GLOBAL.

![image](https://github.com/user-attachments/assets/7d4d1d29-5d6e-48dc-a220-e1188d1f12fb)

Error codes that we are trying to ignore must be used by MySQL; if not, we encounter errors like below.

![image](https://github.com/user-attachments/assets/90f45828-f546-4648-ab91-178da834f680)

### Verbosity and suppression-list interaction

This filtering is the combination of log_error_verbosity and log_error_suppression_list. We can filter out the error log by setting both log_error_verbosity and log_error_suppression_list. If the server is started with log_error_verbosity = 2 and log_error_suppression_list is to ‘MY-010035, MY-72’ error log will log the ERROR and WARNING messages but discarding the named error codes of ‘MY-010035, MY-72’.

In case the server started with the log_error_verbosity = 1 and with the log_error_suppression_list, which is not effective because all error codes it could potentially suppress are already excluded based on the log_error_verbosity configuration.

**Conclusion:** Error Log Filtering introduces a strategic approach to error log management, enabling administrators to navigate the complexities of database troubleshooting with precision. By assigning priorities to errors and dynamically adjusting verbosity levels, MySQL empowers users to focus on critical issues and maintain optimal system performance. As you explore MySQL’s capabilities, consider incorporating Priority-Based Error Log Filtering into your toolkit for a more efficient and targeted approach to database management.

**Percona Distribution for MySQL is the most complete, stable, scalable, and secure open source MySQL solution available, delivering enterprise-grade database environments for your most critical business applications… and it’s free to use!**
