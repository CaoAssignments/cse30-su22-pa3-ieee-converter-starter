# PA 3 - IEEE Converter

## Due
All files are due on Fri. Sep 2nd, at 11:59pm.

See "Grading” and “Submission” sections for more details.


## Overview
In this assignment, you will write a program to extract the parts of a single-precision IEEE-754 floating point hexadecimal number.

The educational purpose of this programming assignment is to provide you more practice with bitwise operations, IEEE-754 floating point numbers, working with the stack in assembly, basic dynamic memory allocation, and working with C and assembly in the same program.


## Tutorials and readings

Here are a few helpful links in case you need to read up to get familiar with the languages and tools.

- [Unix Tutorial](http://www.ee.surrey.ac.uk/Teaching/Unix/)
- [Vim Tutorial](https://www.openvim.com/)
- [Getting Started-Git Basics](http://git-scm.com/book/en/Getting-Started-Git-Basics)
- [Git Basics Chapter](http://git-scm.com/book/en/Git-Basics)
- [Learn C](https://www.learn-c.org/)
- [C for Java Programmers](http://www.cs.columbia.edu/~hgs/teaching/ap/slides/CforJavaProgrammers.ppt)
- [Debugging with gdb](http://www.delorie.com/gnu/docs/gdb/gdb_toc.html)

## Getting started
Follow these steps to acquire the starter files and prepare your Git repository.

### Gathering Starter Files
The first step is to gather all the appropriate files for this assignment. First connect to ieng6 via ssh, then, on ieng6, connect to the pi-cluster via ssh.

```
$ ssh cs30s222yx@ieng6.ucsd.edu
$ ssh cs30s222yx@pi-cluster.ucsd.edu
$ # or:
$ ssh cs30s222yx@pi-cluster.ucsd.edu -J cs30s222yx@ieng6.ucsd.edu
```
Create and enter the pa3 working directory.
```
$ mkdir ~/pa3
$ cd ~/pa3
```
Copy the starter files from the public directory.
```
$ cp -r ~/../public/pa3StarterFiles/* ~/pa3/
```

### Files provided
Files provided:
```
pa3.h
pa3Strings.h
test.h
Makefile
```

### Preparing Git Repository
You are required to use Git with this programming assignment. See PA1 for how to set up your repository. **You must make your Github repository private.**

## Sample output
A sample stripped executable provided for you to try and compare your output against is available in the public directory, run them using the following commands (where you will also pass in the command line arguments):
```
$ ~/../public/pa3testfpdec
```

#### Warning
1. It is possible to write your assembly files as a C files, compile the C files into assembly files with the `-S` flag, and submit the resulting assembly files into Gradescope. **Do not do this. This will result in a 0 for your correctness grade.** It very is easy to distinguish a compiler written assembly file from a person written assembly file.
2. The output of your program MUST match exactly as it appears in the `pa3testfpdec` output. You need to pay attention to everything in the output, from the order of the error messages to the small things like extra newlines at the end (or beginning, or middle, or everywhere)!
3. We are not giving you any sample outputs, instead you are provided some example inputs. You are responsible for trying out all functionality of the program; the list of example inputs is not exhaustive or complete. It is important that you fully understand how the program works and you test your final solution thoroughly against the executable.

Example input that has normal output:
```
cs30s222yx@pi-cluster-001:pa3$ ./fpdec -s 0x41260000
cs30s222yx@pi-cluster-001:pa3$ ./fpdec -s 0x40000000
cs30s222yx@pi-cluster-001:pa3$ ./fpdec -h 0xbf800000
cs30s222yx@pi-cluster-001:pa3$ ./fpdec -h 0xc47a0000
```


### Tips

**Diffing**

An easy way to check your output against the sample executable is by using diff:
```
cs30s222yx@pi-cluster-001:pa3$ ./fpdec -s 0x41260000 > myOutfile
cs30s222yx@pi-cluster-001:pa3$ ~/../public/pa3testfpdec -s 0x41260000 > refOutfile
cs30s222yx@pi-cluster-001:pa3$ diff -s myOutfile refOutfile
```

## Detailed overview

The function prototypes for the various C and a Assembly functions are as follows. They should be written in modules with identical names (except for main).

### C routines
```c
int main(int argc, char* argv[]); // (in the file fpdec.c)
```

### Assembly routines
```c
unsigned long parseNum(char* argv[]);
void extractParts(unsigned long ieeeBin, ieeeParts_t* fill);
```

### Process Overview
The following is an explanation of the main tasks of the assignment, and how the individual functions work together to form the whole program.

**fpdec workflow:**
1. Parse all the command line arguments passed into the program. Do all the required error checking.
2. Parse the IEEE-754 number passed in from the command line. Remember to use your `parseNum()` function here.
3. Instantiate your `ieeeParts_t` struct on either the stack or the heap and populate it with your `extractParts()` function.
4. Print out each member of the struct using the provided strings in `pa3Strings.h`.

![](https://i.imgur.com/7x4zsqs.png)

**Note** For this assigment and the two extra credits, you may assume the following:
- The exponent will never be all 1s or all 0s.
- All numbers passed in will be prefixed with `0x`.

## Detailed Implementation
Listed below are all the modules to be written. **Please read the entire writeup before writing any code.**

### parseNum.s
```c
unsigned long parseNum(char* argv[]);
```
This function takes in the command line arguments from main and parses the single-precision IEEE-754 floating point number from a string into an unsigned long. You will want to use `strtoul()` here (see `man -s3 strtoul`).

You are guarenteed that `argv[2]` will always be a properly formatted single-precision IEEE-754 floating point number in hexadecimal.

**Return value:** The IEEE-754 floating point number as an `unsigned long`.

---
### extractParts.s
```c
void extractParts(unsigned long ieeeBin, ieeeParts_t* fill);
```
This function will take in a single-precision IEEE-754 floating point number `ieeeBin` and extract its various parts to fill the passed in struct `fill`.

| Part to extract | Details |
|-----------------|---------|
| Sign bit        | The sign bit should be the least significant bit in the struct member `sign`. All other bits must be unset. |
| Exponent      | The exponent must be <ins>unbiased</ins>.  All other bits must be unset. |
| Mantissa        | The mantissa must be the 23 least significant bits in the struct member `mantissa`. You must also reveal the hidden bit as the next least significant bit for a total of 24 bits. All other bits must be unset. |

**Example:**
If `ieeeBin` is `10.375`, whose binary representation is `01000001001001100000000000000000`, we have the following struct:
| Struct member | Decimal value | Binary Value                       |
|---------------|---------------|------------------------------------|
| `sign`        | `0`           | `00000000`                         |
| `exponent`    | `3`           | `00000011`                         |
| `mantissa`    | `10878976`    | `00000000101001100000000000000000` |


---
### fpdec.c
```c
int main(int argc, char* argv[]);
```

This is the main driver of your program. Its tasks are to parse the command line arguments, build the struct of IEEE-754 floating point parts, and print out the struct's members.

__Parsing command line arguments:__

For this assignment, the functionality of the program will be determined by the number of arguments passed in from the command line, and the flag passed in. If two argument are passed in from the command line, you may assume they are either `-s` or `-h` (more on these flags below) and the single-precision IEEE-754 floating point number in hexadecimal, respectively.

The following table details how to handle these flags:
| Flag Options | How to Handle |
|--------------|---------------|
|`-s`          | Instantiate your struct on the stack |
|`-h`          | Instantiate your struct on the heap  |

**Notes**
- You are guarenteed that the flag passed in is either `-s` or `-h`. If you pass in any other flag to the test executable, it is not guarenteed to work.
- The only valid number of command line arguments that can be passed in is 2.
- You may assume that no poorly formatted single-precision IEEE-754 floating point numbers will be passed in as an argument.
- All strings that you need to print out for this function can be found in `pa3Strings.h`.

**Detailed overview**

Now let’s take a closer look at each step of the program.
1. Parse all the command line arguments passed into the program. Do all the required error checking.
2. Parse the IEEE-754 number passed in from the command line. Remember to use your `parseNum()` function here.
3. Instantiate your `ieeeParts_t` struct on either the stack or heap and populate it with your `extractParts()` function.
4. Print out each member of the struct using the provided strings in `pa3Strings.h`.
5. Return `EXIT_SUCCESS`.

You are free to add your own helper functions as you wish.

__Reasons for error__

There is only one reason for error in this program. If an invalid number of arguments is passed in, use `fprintf()` to print `INVALID_ARGS` and `SHORT_USAGE` to stderr, then return `EXIT_FAILURE`.

## Unit Testing

You are provided with a header file **test.h** and a few rules in **Makefile** to help you implement unit testing in this assignment. You are expected to implement unit test files in C for the following functions:

```c
unsigned long parseNum(char* argv[]);
void extractParts(unsigned long ieeeBin, ieeeParts_t* fill);
```

Refer to unit tests from PA1 on how to write these tester functions. You are responsible for making sure you thoroughly test your functions. Make sure you think about boundary cases, special cases, general cases, extreme limits, error cases, etc. as appropriate for each function. The Makefile includes the rules for compiling. Keep in mind that your unit test will not build until all required files for the unit tests have been written.

These test files will be collected and counted as part of the correctness of your program.

**To compile:**
```
$ make testparseNum
```
**To run:**
```
$ ./testparseNum
```

The same goes for `testextractParts`.


## Extra credit

There are 20 points possible for extra credit on this assignment. The two extra credit sections do not depend on one another, so none, either, or both may be done. Do not begin this extra credit until your non-extra credit assignment is completed and you have tested it thoroughly.


### fpdecEC.s (10 points)

You will need to write the main function in `fpdec.c` in assembly. This program should behave exactly the same as the one in the main assignment. A simple way to do this is to translate your existing main function into assembly. There will be no changes to how the non-extra credit program operates: you will be making a separate `fpdecEC` program. To begin, create a new file called `fpdecEC.s`.

```
$ touch fpdecEC.s
```

To make the extra credit:

```
$ make fpdecEC
```

Because this program should behave exactly the same as the one in the main assignment, you can use the same public exectuable `pa3testfpdec` to test this extra credit portion.

### Conversion to Decimal (10 points)

You will need to add new functionality to your main function to convert the single-precision IEEE-754 floating point hexadecimal number to decimal. There will be no changes to how the non-extra credit program operates: you will be making a separate `fpdecEC2` program. To begin, make a copy of your `fpdec.c` file. Do not make changes to any other files.

```
$ cp ~/pa3/fpdec.c ~/pa3/fpdecEC2.c
```

To make the extra credit:
```
$ make fpdecEC2
```

There is a public executable for you to test with.
```
$ ~/../public/pa3testfpdecEC2 -s 0x41260000
```

#### fpdecEC2.c
```c
int main(int argc, char * argv[]);
```

The workflow for this program is similar to that of the main assignment. For reference, below is the detailed overview for the main assignment with some extra steps in bold:

**Detailed overview**

1. Parse all the command line arguments passed into the program. Do all the required error checking.
2. Parse the IEEE-754 number passed in from the command line. Remember to use your `parseNum()` function here.
3. Instantiate your `ieeeParts_t` struct on either the stack or the heap and populate it with your `extractParts()` function.
4. **Convert the parts within the filled in struct into a `float`**.
5. Print out each member of the struct using the provided strings in `pa3Strings.h`. **Also print out the decimal representation of the IEEE-754 number.**
5. Return `EXIT_SUCCESS`.




**Tip:** One way to perform step 4 is by first extracting the whole number part from the mantissa (the part to the left of the decimal point), then extracting the decimal part from the mantissa (the part to the right of the decimal point). Then all you have to do is add the two results together and account for the signed bit.

**Important Notes:**
- For this extra credit, given a single-precision IEEE-754 floating point number called `ieeeBin`, you may assume that `ieeeBin <= -1` or `1 <= ieeeBin`.
- There is a way to do this extra credit by a simple pointer cast. **This, and anything like this, is forbidden. You will receive a 0 for this extra credit for doing this.** We expect this portion of the assignment to be done by parsing each individual bit from the IEEE number.

## Grading

#### Correctness: 135 points
- There are 3 main components of your submission

| Component | Description | Points |
|-----------|-------------|--------|
| Student unit tests | These will test whether or not your unit tests compile | 1 |
| Autograder unit tests | These will test your `parseNum()` and `extractParts()`. A sanity check will be provided for each function | 100 |
| Integrated tests | These will test your main function. For clarity, we will **not** compile your code with our solution code. A sanity check will be provided | 30 |
| Valgrind | All test cases will have their memory correctness checked by Valgrind | 4 |

#### Memory Allocation: -4 to 0 Points
- We will manually check your code for proper stack/heap allocation and deallocation.
- This score will be combined with the `valgrind` autograder case to be a total of 4 points.

#### Unit Tests: 15 points
- Various unit test files manually graded on test case completeness and quality.

#### Extra Credit: 20 points
- View the Extra Credit section for more information.

#### Compile!
- If what you turn in does not compile with the given `Makefile`, you will receive 0 points for functionality. Do not modify the given `Makefile` except when specified.


## Submission

**Super important warning:** If your code does not compile or crashes immediately when run, we will not be able to grade it or reward you functionality points.

Log in to gradescope.com using your @ucsd.edu email address, select the CSE 30 course, assignment PA 3, and upload the following files.  Your file names must match the below *exactly* otherwise our Makefile will not find your files.

We will grade using our `Makefile`, `pa3Strings.h`, `test.h`, and all other files from you. When submitting, please check that Gradescope shows “compile success” to confirm you code works with the given `Makefile`.

```
pa3.h
fpdec.c
parseNum.s
extractParts.s
testparseNum.c
testextractParts.c
```
For extra credit, also submit `fpdecEC.s` and/or `fpdecEC2.c`.

If there is anything in these procedures which needs clarifying, or any mistake in this write-up (it's long, we probably made some), please feel free to ask any tutor, the instructor, or post on Piazza.
