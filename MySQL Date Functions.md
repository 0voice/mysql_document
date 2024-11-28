原文地址：https://www.mysqltutorial.org/mysql-date-functions/



# MySQL Date Functions

This page shows you the most commonly used MySQL Date functions that allow you to manipulate date and time data effectively.

## Section 1. Getting the current Date & Time

This section explains the functions that allow you to retrieve the current date, time, or both.

- [CURDATE()](https://www.mysqltutorial.org/mysql-date-functions/mysql-curdate/) – Return the current date. ( synonyms: CURRENT_DATE() & CURRENT_DATE).
- [CURRENT_TIME](https://www.mysqltutorial.org/mysql-date-functions/mysql-current_time/) – Return the current time ( synonyms: CURRENT_TIME() & CURTIME() ).
- [NOW()](https://www.mysqltutorial.org/mysql-date-functions/mysql-now-function/) – Return the current date and time ( synonyms: CURRENT_TIMESTAMP(), CURRENT_TIMESTAMP, LOCALTIME(), LOCALTIMESTAMP()).
- [SYSDATE()](https://www.mysqltutorial.org/mysql-date-functions/mysql-sysdate/) – Return the time at which it executes.
- [UTC_TIMESTAMP()](https://www.mysqltutorial.org/mysql-date-functions/mysql-utc_timestamp/) – Return the current UTC date and time.
- [UTC_DATE()](https://www.mysqltutorial.org/mysql-date-functions/mysql-utc_date/) – Return the current UTC date.
- [UTC_TIME()](https://www.mysqltutorial.org/mysql-date-functions/mysql-utc_time/) – Return the current UTC time.

## Section 2. Calculating Date and Time

- [ADDTIME()](https://www.mysqltutorial.org/mysql-date-functions/mysql-addtime/) – Add a time interval to a time value or datetime value.
- [DATE_ADD()](https://www.mysqltutorial.org/mysql-date-functions/mysql-date_add/) – Add a time value to a date (synonyms: ADDDATE()).
- [DATE_SUB()](https://www.mysqltutorial.org/mysql-date-functions/mysql-date_sub/) – Subtract a time value (interval) from a date.
- [DATEDIFF()](https://www.mysqltutorial.org/mysql-date-functions/mysql-datediff-function/) – Return the difference in days of two date values.
- [TIMEDIFF()](https://www.mysqltutorial.org/mysql-date-functions/mysql-timediff/) – Return the difference of two time values.
- [TIMESTAMPADD()](https://www.mysqltutorial.org/mysql-date-functions/mysql-timestampadd/) – Add or subtract an interval from a timestamp or date.
- [TIMESTAMPDIFF()](https://www.mysqltutorial.org/mysql-date-functions/mysql-timestampdiff/) – Return the difference between two timestamp values.
- [TIME_TO_SEC()](https://www.mysqltutorial.org/mysql-date-functions/mysql-time_to_sec/) – Return the number of seconds from a time argument.
- [TO_DAYS()](https://www.mysqltutorial.org/mysql-date-functions/mysql-to_days/) – Return a day number (the number of days since year 0) from a given date.

## Section 3. Converting Functions

- [CONVERT_TZ()](https://www.mysqltutorial.org/mysql-date-functions/mysql-convert_tz/) – Convert a datetime value from one time zone to another.
- [FROM_DAYS()](https://www.mysqltutorial.org/mysql-date-functions/mysql-from_days/) – Convert a numeric day count into a date.
- [STR_TO_DATE()](https://www.mysqltutorial.org/mysql-date-functions/mysql-str_to_date/) – Convert a string to date.
- [FROM_UNIXTIME()](https://www.mysqltutorial.org/mysql-date-functions/mysql-from_unixtime/) – Convert UNIX timestamps into a readable date and time format.
- [UNIX_TIMESTAMP()](https://www.mysqltutorial.org/mysql-date-functions/mysql-unix_timestamp/) – Convert a datetime to a UNIX timestamp.

## Section 4. Formatting Date & Time functions

- [DATE_FORMAT()](https://www.mysqltutorial.org/mysql-date-functions/mysql-date_format/) – Return a string representation of a date based on a format.
- [TIME_FORMAT()](https://www.mysqltutorial.org/mysql-date-functions/mysql-time_format-function/) – Return a string representation of a time based on a format.
- [GET_FORMAT()](https://www.mysqltutorial.org/mysql-date-functions/mysql-get_format/) – Return a format string for a date, time, datetime, or timestamp.

## Section 5. Extracting Date & Time Functions

The extraction functions allow you to extract date and time components from a date and time.

- [DATE()](https://www.mysqltutorial.org/mysql-date-functions/mysql-date-function/) – Extract the date component from a date.
- [EXTRACT()](https://www.mysqltutorial.org/mysql-date-functions/mysql-extract/) – Extract a component of a date.
- [YEAR()](https://www.mysqltutorial.org/mysql-year/) – Return the year component of a date.
- [YEARWEEK()](https://www.mysqltutorial.org/mysql-date-functions/mysql-yearweek/) – Return the year and week for a date.
- [QUARTER()](https://www.mysqltutorial.org/mysql-date-functions/mysql-quarter/) – Return the quarter of the year for a date.
- [MONTH()](https://www.mysqltutorial.org/mysql-month/) – Return the month component of a date.
- [WEEK()](https://www.mysqltutorial.org/mysql-date-functions/mysql-week/) – Return the week component of a date.
- [WEEKDAY()](https://www.mysqltutorial.org/mysql-weekday/) – Return the weekday index of a date.
- [WEEKOFYEAR()](https://www.mysqltutorial.org/mysql-date-functions/mysql-week/) – Return the calendar week of the date (1-53) – equivalent to WEEK(date, 3).
- [DAY()](https://www.mysqltutorial.org/mysql-date-functions/mysql-day/) – Return the day of the month for a specific date (1-31). DAYOFMONTH is the synonym for DAY.
- [DAYOFYEAR()](https://www.mysqltutorial.org/mysql-date-functions/mysql-dayofyear/) – Return the day of the year (1-366).
- [DAYOFWEEK()](https://www.mysqltutorial.org/mysql-dayofweek/) – Return the day of the week (1-7).
- [HOUR()](https://www.mysqltutorial.org/mysql-date-functions/mysql-hour/) – Return the hour for a time.
- [MINUTE()](https://www.mysqltutorial.org/mysql-date-functions/mysql-minute-function/) – Return the minute for a time.
- [SECOND()](https://www.mysqltutorial.org/mysql-date-functions/mysql-second/) – Return the second for a time.
- [LAST_DAY()](https://www.mysqltutorial.org/mysql-date-functions/mysql-last_day/) – Return an integer that represents the last day of the month for a specific date.

## Section 6. Getting Month & Day Names

This section shows you how to use functions to get the month and day names.

- [DAYNAME()](https://www.mysqltutorial.org/mysql-date-functions/mysql-dayname/) – Return the name of the day for a specific date.
- [MONTHNAME()](https://www.mysqltutorial.org/mysql-date-functions/mysql-monthname/) – Return the name of the month for a specific date.

## Section 7. Creating Date & Time Functions

- [MAKEDATE()](https://www.mysqltutorial.org/mysql-date-functions/mysql-makedate/) – create a date based on a given year and the number of days.
- [MAKETIME()](https://www.mysqltutorial.org/mysql-date-functions/mysql-maketime/) – create a time based on hour, minute, and second.

## Section 8. Handling Period Functions

This section covers the function that manipulates the periods in the format YYMM or YYMMMM.

- [PERIOD_ADD()](https://www.mysqltutorial.org/mysql-date-functions/mysql-period_add/) – add a number of months to a period in the format YYMM or YYMMMM.
- [PERIOD_DIFF()](https://www.mysqltutorial.org/mysql-date-functions/mysql-period_diff/) – calculate the difference in months of two periods represented in the format YYMM or YYYYMM.
