# Introduction #
Our documentation on this wiki and elsewhere uses a standard set of notations to convey various ideas.

## Numeric Formats ##
| Notation | Example | Notes |
|:---------|:--------|:------|
| **`d` suffix** | `1d` or `87d` | When we are explicitly naming a decimal value |
| **`0x` prefix** | `0x0d` or `0xaf19` | When we are explicitly naming a hexadecimal value |
| **single quotes** | `'F'` or `'9'` | When referring to the ASCII value of a character |

## printf Formats ##
| Notation | Description |
|:---------|:------------|
| **`%c`** | A single byte |
| **`%s`** | A series of bytes of unspecified length and termination |

## Common Pseudofunctions ##
| Function | Description |
|:---------|:------------|
| **`len(x)`** | Used when referring to the number of bytes used to store or represent "x" |
| **`sizeof(x)`** | Used when referring to the implementations-specific storage size of a particular datatype "x" |

## Protocol Notation ##
When describing a series of bytes, the bytes are commonly laid out using printf format code in a horizontal table, like this:
| **Example Format String:** | %c Single Byte | %c%c Two Related Bytes | %s Series of Bytes of Unspecified Length |
|:---------------------------|:---------------|:-----------------------|:-----------------------------------------|

Uppercase initial letters are used to denote that the items in question have certain significance. That is, an item labeled "Data" is referring to some data in particular, and not the general idea of "data".

Bold text is used as the first column in this horizontal row to denote that it describes what follows it.