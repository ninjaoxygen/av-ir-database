# Introduction #
Crestron uses IR files that end with the extension ".ir". They're reasonably easy to figure out if you know what you're looking at.

# Disclaimer #
This format was reverse-engineered via trial and error. At no time was any binary provided by Crestron involved with this process. No executable files were decompiled. Every piece of information here was determined by hand, manually, and all information obtained was deduced empirically.

# Documentation Method #
This document uses the documentation method outlined in [DocumentationGuide](DocumentationGuide.md).

# Format #
Crestron uses a field-based file format. Each piece of information inside of a Crestron IR file has a field ID, a length, and the corresponding data.

| **Field Structure:** | %c Length | %c Field Type | %s Data |
|:---------------------|:----------|:--------------|:--------|

The length includes all three chunks, and is thus len(Data)+2

## Field Types ##
| **Metadata** |
|:-------------|
| 0xf0         | The file format specifier. Always of length 4, always contains 'IR'. |
| 0xf1         | The comment field. |
| 0xf2         | The last modified date of this file. |
| 0xf3         | Manufacturer name. |
| 0xf4         | Device model. |
| 0xf5         | Remote model. |
| 0xf6         | Minimum repeat count, and a byte of unknown significance. |
| 0xf7         | Device family. |
| 0xf8         | Two bytes of unknown significance. |
| 0xf9         | Options/Metadata encoded into sub-fields. |
| **Control**  |
| 0x00         | A special typeless field indicating an immediate end of file. No type byte follows. |
| 0xff         | End-of-list indicator, which indicates the beginning of IR data chunks. Always of length 2 with no data. |
| **Data**     |
| 0x01-0x7e    | A list item. Depending on the phase of processing, is either a function name or an IR chunk. |

All list item fields use their type as an index. Thus, Field type 0x05 is #5 in the list.

For basic processing of IR data, we can safely ignore all fields except: 00-7e, f0, f3, f4, f5, and ff.

## IR Chunk Layout ##
Each IR data chunk contains several pieces of information encoded serially with no notation.
| **IR Data Chunk:** | %c Carrier Frequency | %c Header Length | %c Repeat Length | %c Repeat Count/Timing Count | %s Wave Timings | %s Wave Data |
|:-------------------|:---------------------|:-----------------|:-----------------|:-----------------------------|:----------------|:-------------|

| **Wave Timings:** | %c%c Wave Timing Length | (repeat...) |
|:------------------|:------------------------|:------------|

| **Wave Data:** | %c Waveform High/Low Timing Indexes | (repeat...) |
|:---------------|:------------------------------------|:------------|

| **Data** | **As** | **Description** |
|:---------|:-------|:----------------|
| Carrier Frequency | 1 byte | Some math needs to be applied to determine the frequency in kHz versus the byte value: Carrier<sub>kHz</sub> = (4,000,000 / Carrier<sub>Byte</sub>) |
| Header Length | 1 byte | Expressed as a single byte. The number of wave transition pairs that are fired only once in a function actuation. |
| Repeat Length | 1 byte | The number of wave transition pairs that are sent following the header, and repeated until the function stops actuating or the number of minimum repeat cycles is fired. |
| Repeat Count | 4 bits | Specifies the minimum number of times the repeat segment of the waveform must be fired, even if the function stops actuating. |
| Timing Count | 4 bits | Specifies the number of wave periods in the following section of IR data. |
| Wave Timings | byte pairs | Two-byte integers that specify unique wave state lengths, with the most significant byte stored first. |
| Wave Data | bytes  | Single bytes that contain pairs of _wave high_ and _wave low_ period length timing indexes. |

## Wave Timing Index ##
  * Indexed from zero, and can only reach a maximum index of 15. (32 byte maximum)
  * The values they contain are timings that depend on the carrier frequency. Determining their absolute timing values requires a little math.

## Wave Data Bytes ##
The Wave Data is a string of bytes for which each contains a nybble for "high" and a nybble for "low". Each nybble refers to the Wave Timing Index. The first nybble denotes a "high" period of the wave, and the second nybble denotes a "low" period of the wave.

The number of Wave Data bytes will equal Repeat Length + Header Length.

# Missing #
This document requires further work before it is complete:
  1. Math for absolute wave period timings
  1. Code examples
  1. Relevant hex dump of example IR file