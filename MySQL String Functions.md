原文地址：https://www.mysqltutorial.org/mysql-string-functions/



# MySQL String Functions

This page shows you the most commonly used MySQL string functions that allow you to manipulate character string data effectively.

| Name                                                         | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [CONCAT](https://www.mysqltutorial.org/mysql-string-functions/mysql-concat/) | Concatenate two or more strings into a single string.        |
| [CONCAT_WS](https://www.mysqltutorial.org/mysql-string-functions/mysql-concat_ws/) | Return a single string by concatenating multiple strings separated by a specified separator. |
| [INSTR](https://www.mysqltutorial.org/mysql-string-functions/mysql-instr/) | Return the position of the first occurrence of a substring in a string. |
| [LENGTH](https://www.mysqltutorial.org/mysql-string-functions/mysql-string-length/) | Get the length of a string in bytes.                         |
| [CHAR_LENGTH](https://www.mysqltutorial.org/mysql-string-functions/mysql-char_length/) | Return the length of a string measured in characters.        |
| [LEFT](https://www.mysqltutorial.org/mysql-string-functions/mysql-left-function/) | Get a specified number of leftmost characters from a string. |
| [LOWER](https://www.mysqltutorial.org/mysql-string-functions/mysql-lower/) | Return a string converted to lowercase.                      |
| [LOCATE](https://www.mysqltutorial.org/mysql-string-functions/mysql-locate-function/) | Return the position of a substring within a given string starting at a specified position. |
| [LTRIM](https://www.mysqltutorial.org/mysql-string-functions/mysql-ltrim-function/) | Remove all leading spaces from a string.                     |
| [REPLACE](https://www.mysqltutorial.org/mysql-string-functions/mysql-replace-function/) | Replace all occurrences of a substring in a string with a new one. |
| [REPEAT](https://www.mysqltutorial.org/mysql-string-functions/mysql-repeat-function/) | Repeat a string a specified number of time.                  |
| [REVERSE](https://www.mysqltutorial.org/mysql-string-functions/mysql-reverse-function/) | Reverse a string.                                            |
| [RIGHT](https://www.mysqltutorial.org/mysql-string-functions/mysql-right-function/) | Get a specified number of rightmost characters from a string. |
| [RTRIM](https://www.mysqltutorial.org/mysql-string-functions/mysql-rtrim-function/) | Remove all trailing spaces from a string.                    |
| [SUBSTRING](https://www.mysqltutorial.org/mysql-string-functions/mysql-substring/) | Extract a substring starting from a position with a specific length. |
| [SUBSTRING_INDEX](https://www.mysqltutorial.org/mysql-string-functions/mysql-substring_index-function/) | Return a substring from a string before a specified number of occurrences of a delimiter. |
| [TRIM](https://www.mysqltutorial.org/mysql-string-functions/mysql-trim-function/) | Remove unwanted characters from a string.                    |
| [FIND_IN_SET](https://www.mysqltutorial.org/mysql-string-functions/mysql-find_in_set/) | Find a string within a comma-separated list of strings.      |
| [FORMAT](https://www.mysqltutorial.org/mysql-string-functions/mysql-format-function/) | Format a number with a specific locale, rounded to the number of decimals. |
| [UPPER](https://www.mysqltutorial.org/mysql-string-functions/mysql-upper/) | Convert a string to uppercase.                               |

## Concatenation Functions

- [CONCAT()](https://www.mysqltutorial.org/mysql-string-functions/mysql-concat/): Combines two or more strings into a single string.
- [CONCAT_WS()](https://www.mysqltutorial.org/mysql-string-functions/mysql-concat_ws/): Combines multiple strings into a single string with a specified separator.

## Substring Functions

- [SUBSTRING()](https://www.mysqltutorial.org/mysql-string-functions/mysql-substring/): Extracts a substring from a given string.
- [SUBSTRING_INDEX()](https://www.mysqltutorial.org/mysql-string-functions/mysql-substring_index-function/): Extracts a substring from a string using a delimiter.
- [LEFT()](https://www.mysqltutorial.org/mysql-string-functions/mysql-left-function/): Returns a specified number of characters from the beginning of a string.
- [RIGHT()](https://www.mysqltutorial.org/mysql-string-functions/mysql-right-function/): Returns a specified number of characters from the end of a string.
- [MID()](https://www.mysqltutorial.org/mysql-string-functions/mysql-substring/): Extracts a substring from the middle of a string. The MID() function is a synonym for SUBSTRING().

## Searching and Locating Functions

- [LOCATE()](https://www.mysqltutorial.org/mysql-string-functions/mysql-locate-function/): Finds the position of a substring within a string.
- [POSITION()](https://www.mysqltutorial.org/mysql-string-functions/mysql-locate-function/): Finds the position of a substring. The `POSITION()` is a synonym for the `LOCATE()` function.
- [INSTR()](https://www.mysqltutorial.org/mysql-string-functions/mysql-instr/): Another function for finding the position of a substring.

## Case Conversion Functions

- [UPPER()](https://www.mysqltutorial.org/mysql-string-functions/mysql-upper/): Converts a string to uppercase.
- [LOWER()](https://www.mysqltutorial.org/mysql-string-functions/mysql-lower/): Converts a string to lowercase.

## Character Manipulation Functions

- [REPLACE()](https://www.mysqltutorial.org/mysql-string-functions/mysql-replace-function/): Replaces all occurrences of a substring in a string.
- [TRIM()](https://www.mysqltutorial.org/mysql-string-functions/mysql-trim-function/): Removes leading and trailing spaces from a string.
- [LTRIM()](https://www.mysqltutorial.org/mysql-string-functions/mysql-ltrim-function/): Removes leading spaces from a string.
- [RTRIM()](https://www.mysqltutorial.org/mysql-string-functions/mysql-rtrim-function/): Removes trailing spaces from a string.
- [REPEAT()](https://www.mysqltutorial.org/mysql-string-functions/mysql-repeat-function/): Repeats a string a specified number of times.
- [REVERSE()](https://www.mysqltutorial.org/mysql-string-functions/mysql-reverse-function/): Reverses the characters in a string.
- [INSERT()](https://www.mysqltutorial.org/mysql-string-functions/mysql-insert-function/): Replaces a substring within a string with a new substring.

## Whitespace Functions

- SPACE(): Returns a string consisting of spaces.
- [ASCII()](https://www.mysqltutorial.org/mysql-string-functions/mysql-ascii/): Returns the ASCII value of the leftmost character of a string.
- CHAR(): Converts an ASCII value to a character.

## Length and Count Functions

- [LENGTH()](https://www.mysqltutorial.org/mysql-string-functions/mysql-string-length/): Returns the length of a string in bytes.
- [CHAR_LENGTH()](https://www.mysqltutorial.org/mysql-string-functions/mysql-char_length/): Returns the length of a string in characters.
- OCTET_LENGTH(): Returns the length of a string in bytes.
- BIT_LENGTH(): Returns the length of a string in bits.
- [CHARACTER_LENGTH()](https://www.mysqltutorial.org/mysql-string-functions/mysql-char_length/): Returns the length of a string in characters.
- BIT_COUNT(): Counts the number of bits in a binary string.
- STRCMP(): Compares two strings and returns their relative order.

## Padding string functions

- [LPAD()](https://www.mysqltutorial.org/mysql-string-functions/mysql-lpad/) – Left-pads a string with a set of characters to a specified length.
- [RPAD()](https://www.mysqltutorial.org/mysql-string-functions/mysql-rpad/) – Right-pads a string with a set of characters to a specified length.
