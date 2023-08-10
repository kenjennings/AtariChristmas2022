# AtariChristmas2022
**VC3 2022 Vintage Christmas Programming Challenge #VCCC2022**

https://logiker.com/Vintage-Computing-Christmas-Challenge-2022

Generate this shape on screen:

[![VCCC2022](https://raw.githubusercontent.com/kenjennings/AtariChristmas2022/master/VCCC2022illustration.png)](https://github.com/kenjennings/AtariChristmas2022/blob/main/README.md "VCCC2022") 
 
---

Some random acts of hackery below solve the problem conventionally which could work on a wide variety of computers to unconventional methods specific to the Atari.

---

Lowest common denominator in BASIC:

	100 PRINT "    *       *    "
	110 PRINT "    **     **    "
	120 PRINT "    ***   ***    "
	130 PRINT "    **** ****    "
	140 PRINT "*****************"
	150 PRINT " *************** "
	160 PRINT "  *************  "
	170 PRINT "   ***********   "
	180 PRINT "    *********    "
	190 PRINT "   ***********   "
	200 PRINT "  *************  "
	210 PRINT " *************** "
	220 PRINT "*****************"
	230 PRINT "    **** ****    "
	240 PRINT "    ***   ***    "
	250 PRINT "    **     **    "
	260 PRINT "    *       *    "

The size of this varies based on whether or not the BASIC language stores the program source as-is, or tokenizes the program.  Tokenization in Atari BASIC reduces the line numbers to two bytes, eliminates the blank spaces around the PRINT commands, and reduces the five-letter PRINT commands to just one byte each.

However, Atari BASIC is less likely than other BASICs to improve a computational solution, because all numeric constants and variables are handled as six-byte floating point numbers. 

---

Expressing this in assembly has some plusses and minuses.  Assembly allows clever organization of data that can reduce program size, but assembly may actually be larger code since complex actions can require several 6502 instructions where BASIC provides the library that implements "PRINT" which would not be counted as part of program size. 

The Atari offers multiple ways to solve problems ranging from system-legal, standard coding up to use (or misuse) of the hardware in ways that can't be duplicated on other computers.

The exmples below explore and compare different strategies for solving the assignment to display the star on screen.  Though, none compete with the contest's official winning method for a minimally sized program.

On the Atari a system-standard solution will not be optimal.  The Atari implements a common, generalized, I/O library providing the programmer one address to call for all I/O operations.  This allows the routines that manage devices to be updated and changed in the OS without breaking compatibility with older programs.

The Atari I/O library implementation also has similarity to the Unix concept that everything is a file.  In an Atari program the same code could be used to write data to the screen, the printer, a file on a disk drive, or a cassette tape.

On many other systems the requirements for using different devices require different setup and/or different OS routines.  One device may work with just a call to a routine, and another device may require differently formatted data and a different routine.

While the single OS entry point and standard model for I/O is a convenience to the Atari programmer it also requires the programmer apply a consistent level of work to instruct the OS which operation to perform.  Open a device, write to a device, read from a device, close a device all require the program set up a common data structure describing the operation.

This minimum required amount of work to describe I/O means the total code for a simple problem is likely to be larger on the Atari.

Ordinarily, a program's good behavior requires it open a channel for a device before writing to it.  However, the overhead of performing the open device activity (and also close device) can (usually) (safely) be skipped for the standard screen display most of the time.

By default, the OS opens channel 0 for the text editor (E:), and channel 6 for screen graphics (S:).  This remains intact in the transition from DOS to the machine language program.  So, the program can make the reasonable assumption that E: is available and write directly to channel 0.  (Yes, it is entirely possible the prior program closed E: on channel 0, but going back to DOS should restore this.)

In other news, there is an optimization available for displaying text on the default text screen.  The Atari Central I/O provides a shortcut for printing text.  The I/O channel for the E: device provides a pointer to a routine which outputs a single character provided to the routine via the CPU A register.  This is originally intended for use by BASIC, but is perfectly serviceable for any other program that needs to stream single characters to the screen device.

---

On the subject of later logic optimizations here are some idiotic pattern recognition discussion houghts for what can make the logic or data better/smaller. . . .

- HIGH LEVEL CONCEPT:

Problems: 
 - Each line of text is 17 characters
 - packing 1 character into 1 bit still needs 17 bits 
 - this leaves 1 leftover bit after two bytes/1 word

Solution:
 - Only the line that begins with '*' needs the 17th character to also be a '*'
 - All other lines end in blank space ' '
 - Therefore end each line with the same character used to begin the line.

Represent characters as bits:
 - 1 bit == "*" 
 - 0 bit == " "

Each line: 
 - Byte 0, Byte 1
 - Total 34 bytes of data, 2 bytes per line 
 - One imaginary bit per line to be resolved by logic.

Logic:
    For each line on screen
        Walk through the bits in byte_0 and then byte_1 %10000000 to %00000001
          - %1, print "*" 
          - %0, print " " 
        At the end of each line, If byte is negative Then
            finish with an extra "*"
        ELSE
            finish with an extra " "
        print end of line
    loop again for next line.

More dubious "thinking":
    The star is vertically symmetrical 
      - Therefore the unique lines of data needed is about half 
        the number of displayed lines.
      - Thence a set of loops forward and then back can iterate through 
        the series of data.

    Not every line is unique.  There are several duplicates.
      - Therefore a lookup ID could point to each lines data.
      - Thence more lines of data can be removed.

    There are less than 16 unique strings.
      - Enumerating a lookup ID requires only a nybble of data
      - Therefore one byte is needed for two lookup IDs per line. 

---


















Unfortunately, I missed the contest this year.  But, decided to roll up something **unconventional** for the entertainment value.  

The methods presented here are fairly atypical as solutions go.  Yes, I realize the intent of the challenge is to compute the components to display the Tree -- how many blank spaces, and how many asterisks to use for the lines.  This is supposed to be about patterns and relationships between each line and for each triangular section on the tree.  

However, the Atari can do some unusual things with the display.   Directly printing the data for the tree is the least optimized method of display for most computers.  But, on the Atari the hardware allows ways to optimize the direct output approach.

These demos are built with MADS assembler from eclipse+WUDSN by Ken Jennings

---

**VERSION SUMMARY**

| **VERSION** | **FILE SIZE** | **6502 CODE SIZE** |
| ------- | ------- | ------- |
| WorstCase | 1080 bytes | 3 bytes |
| Original  | 452 bytes | 3 bytes |
| 32 Width  | 272 bytes | 3 bytes |
| Pretty    | 340 bytes | 3 bytes |
| Computed  | 181 bytes | 31 bytes |
| Ugly code | 96 bytes | 65 bytes |

---

**2021 Atari Christmas Original Version...**

[![DEMO](https://raw.githubusercontent.com/kenjennings/AtariChristmas2021/master/AtariChristmas2021.png)](https://github.com/kenjennings/AtariChristmas2021/blob/main/README.md "Demo") 

ASSEMBLY RESULTS:
FILE SIZE:         452 Bytes
- EXE FILE OVERHEAD:  30 Bytes
- NON-DISPLAY DATA:   10 Bytes
- DISPLAY DATA:      409 Bytes
- (340 Bytes Screen Memory)
- (69 Bytes Display List)
- EXECUTABLE CODE:     3 Bytes 

This is the first version I threw together.   Here I abuse Atari's structured file format to load all data for display directly into memory and update the necessary shadow registers.  In the end, no actual code executes to create the display. The only code running is a do-nothing loop to prevent returning to DOS immediately.

While we're here... Setup the display to immitate the number of lines on the C64.

---

**2021 Atari Christmas Worst Case Version...**

[![DEMO](https://raw.githubusercontent.com/kenjennings/AtariChristmas2021/master/AtariChristmas2021.png)](https://github.com/kenjennings/AtariChristmas2021/blob/main/README.md "Demo") 

WORST CASE ASSEMBLY RESULTS:
FILE SIZE:         1080 Bytes
- EXE FILE OVERHEAD:   34 Bytes
- NON-DISPLAY DATA:    10 Bytes
- DISPLAY DATA:      1033 Bytes
- (1000 Bytes Screen memory)
- (33 Bytes Display list)
- EXECUTABLE CODE:     3 Bytes 

The Original version contains some optimizations that are automatic, typical, and  trivial based on the way the Atari's ANTIC chip operates.  For example, re-using the same screen memory data for multiple lines on the screen.  This Worst Case Version produces a display identical to the Original version, but avoids these kinds of  Atari-specific environment optimizations.  This result is what would be expected on most non-Atari systems as the only way to directly generate the display without computation.

The program declares contiguous data for the entire display.  Since screen data is contiguous the Display List is more simple, because the Load Memory Scan only needs to be set up once in the display list and then automatically reads the subsequent data as screen memory for the following lines.

Because the screen data is nearly 1K, the display list following the screen would run over the 1K boundary limit for Display Lists.  Unlike the other versions of the demo using much less data for screen display, this version must move the  Display List to a location that will prevent it from crossing over the 1K boundary.  This introduces an extra segment and more XEX file overhead not present in the other versions.

Again, the only code running is a do-nothing loop to prevent returning to DOS immediately.

---

**2021 Atari Christmas Optimized For 32 Character Width...**

[![DEMO](https://raw.githubusercontent.com/kenjennings/AtariChristmas2021/master/AtariChristmas2021opt32.png)](https://github.com/kenjennings/AtariChristmas2021/blob/main/README.md "Demo") 

EASY OPTIMIZE 32 ASSEMBLY RESULTS:
FILE SIZE:         272 Bytes
- EXE FILE OVERHEAD:  30 Bytes
- NON-DISPLAY DATA:   10 Bytes
- DISPLAY DATA:      229 Bytes
- (181 Bytes Screen Memory)
- (48 Bytes Display List)
- EXECUTABLE CODE:     3 Bytes 

Since the point is to display the Christmas tree let's just service only enough data to make that possible.
 1) Make the Display List only produce what is necessary to display the lines of the tree.   There is no need to present any blank/empty text mode lines. 
 2) The tree at its widest point is less than the 32 characters for ANTIC's Narrow Width display, so use this feature to reduce the data needed for each line.
 3) Since each line begins and ends with blank spaces then the blanks can overlap from line to line to produce the correct number of leading/trailing blanks from "shared" data.

This nearly cuts the original Assembly results in half.

---

**2021 Atari Christmas Optimized Width Plus Prettification...**

[![DEMO](https://raw.githubusercontent.com/kenjennings/AtariChristmas2021/master/AtariChristmas2021opt32pretty.png)](https://github.com/kenjennings/AtariChristmas2021/blob/main/README.md "Demo") 

PRETTIFICATION ASSEMBLY RESULTS:
FILE SIZE:         340 Bytes
- EXE FILE OVERHEAD:  38 Bytes
- NON-DISPLAY DATA:   10 Bytes
- DISPLAY DATA:      289 Bytes
- (209 Bytes Screen Memory)
- (48 Bytes Display List)
- (32 Bytes Character Set)
- EXECUTABLE CODE:     3 Bytes 

Those default colors (modeled from the C64) are ugly, right?   So, let's make this look more like a Christmas Tree.  
1) Use ANTIC Mode 4 for color text.
2) Use colors more like a Christmas Tree
3) Use a (partial) redefined character set to provide the new colored asterix for building the tree.

Yes, this does make the demo a little bigger, but it is still smaller than the original demo by over 100 bytes.  (Amazing considering it had to add 32 bytes for the character set.)

---

**2021 Atari Christmas Prettification With Computation...**

[![DEMO](https://raw.githubusercontent.com/kenjennings/AtariChristmas2021/master/AtariChristmas2021opt32pretty.png)](https://github.com/kenjennings/AtariChristmas2021/blob/main/README.md "Demo") 

PRETTIFICATION COMPUTATION ASSEMBLY RESULTS:
FILE SIZE:         181 Bytes
- EXE FILE OVERHEAD:  38 Bytes
- NON-DISPLAY DATA:   32 Bytes
- DISPLAY DATA:       80 Bytes
- (48 Bytes Display list)
- (32 Bytes Character Set)
- EXECUTABLE CODE:    31 Bytes 

In this version, rather than populating the screen memory at load time, instead compute the values and write into screen memory.

No, this is not a clever analysis of the relationship of the number of asterisks in each line and in each section.   This version unpacks 22 bytes of run-length-encoded data to populate the 209 bytes of allocated screen memory which represent 448 apparent bytes of displayed data on screen.  The unpacking code is 28 bytes long.  (Plus the ubiquitous 3 bytes of do-nothing loop to prevent returning to DOS.)

--- 

**2021 Atari Christmas Ugly Code...**

[![DEMO](https://raw.githubusercontent.com/kenjennings/AtariChristmas2021/master/AtariChristmas2021uglycode.png)](https://github.com/kenjennings/AtariChristmas2021/blob/main/README.md "Demo") 

UGLY CODING ASSEMBLY RESULTS:
 FILE SIZE:          96 Bytes
- EXE FILE OVERHEAD:  10 Bytes
- DATA:               21 Bytes
- EXECUTABLE CODE:    65 Bytes 

No graphics mode creation or setup at all.   Just use the default display provided by the screen editor E: device.

The code simply reads from a table listing the number of asterisks needed for each line.  It outputs the correct number of spaces and asterisks to the E: device to center the asterisks and fill the line.

Logic is based on the default 38 character/line width for the screen editor.

--- 
