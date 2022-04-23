## 1. Compiling

### 1.1. Compiler program

Last time, we compiled program with `make hello`, which turning our source code into machine code so that we could run the complied program with `./hello`.

**Make** is actually just a program called `clang`, a **compiler**, with **options**. If we enter `clang hello.c`, then `clang` would compile it then make a compiled program named `a.out` which is the default output filename of `clang`. So we can run a more specific command: `clang hello -o hello.out`.

### 1.2. Command-line argument

What is `-o hello.out` in the command we've just run is called **command-line argument**, which is an input to a program on the command-line as **extra words** after program's name. `clang` is the program name as the first word in command, and then are additional arguments, which are `-o`, `hello.out` and `hello`. That means we're telling the `clang` to use `hello` as the source code, and use `hello` as the **output** filename.

If we want to use CS50's library, via `#include <cs50.h>`, for the `get_string` function, we need to add a flag: `clang hello -o hello.c -l cs50`.

```c
#include <stdio.h>
#include <cs50.h>

int main() {
  string name = get_string ("What's ur name?");
  printf("hello, %s\n", name);
}
```
The `-l` flag links the `cs50` file, which includes the machine code for `get_string` (among other functions), so that our program can then refer to and use as well.

With `make` program, these arguments are generated for us since the staff has configured `make` in the CS50 IDE already as well.

### 1.3. Smaller steps of compiling

Actually there are **four** steps under compiling source code into machine code.

- Preprocessing
- Compiling
- Assembling
- Linking

#### 1.3.1. Preprocessing

**Preprocessing** generally involves lines that start with a `#`, like `#include`. For example, `#include <cs50.h>` will tell `clang` to look for that header file, since it contains that we want to include in our program. Then, `clang` will essentially **replace the contents** of those header files into our program.

For example:

```c
#include <stdio.h>
#include <cs50.h>

int main(void) {
    string name = get_string("What's your name? ");
    printf("hello, %s\n", name);
}
```

...will be preprocessed into:

```c
...
string get_string(string prompt);
int printf(string format, ...);
...
  
int main(void) {
    string name = get_string("What's your name? ");
    printf("hello, %s\n", name);
}
```

This includes the **prototypes** of all the functions from those libraries we included, so we can then use them in our code.

#### 1.3.2. Compiling

**Compiling** takes our source code, in C, and converts it to another type of source code called **assembly code**, which looks like this:

```assembly
...
main:                         # @main
    .cfi_startproc
# BB#0:
    pushq    %rbp
.Ltmp0:
    .cfi_def_cfa_offset 16
.Ltmp1:
    .cfi_offset %rbp, -16
    movq    %rsp, %rbp
.Ltmp2:
    .cfi_def_cfa_register %rbp
    subq    $16, %rsp
    xorl    %eax, %eax
    movl    %eax, %edi
    movabsq    $.L.str, %rsi
    movb    $0, %al
    callq    get_string
    movabsq    $.L.str.1, %rdi
    movq    %rax, -8(%rbp)
    movq    -8(%rbp), %rsi
    movb    $0, %al
    callq    printf
    ...
```

These instructions are **lower-level** and is closer to the **binary instructions** that a computer's processor can directly understand. They generally operate on **bytes** themselves, as opposed to **abstractions** like variable names.

#### 1.3.3. Assembling

The next step after compiling is **assembling**, which takes the `assembly code` and **translate** it to instructions in binary. The instructions in binary are called **machine code**, which a computer's CPU can run directly,

#### 1.3.4. Linking

The last step is **linking**, where previously compiled versions of libraries that we included earlier, like `cs50.c`, are actually combined with the binary of our program. So we end up with one binary file, `a.out` or  `hello`, that is the combined machine code for  `hello.c`, `cs50.c` and `stdio.c`.(In the CS50 IDE, precompiled machine code for `cs50.c` and `stdio.c` has already been installed, and `clang` has been configured to find and use them.)



These four steps have been **abstracted** away, or simplified, by `make`, so all well we have to implement is the code for our programs.

## 2. Debugging

### 2.1. Bugs

**Bugs** are mistakes or problems in programs that cause them to behave differently than intended.

For example, let's take a look at `buggy0.c`:

```c
#include <stdio.h>

int main (void) {
  // Print 10 hashes
  for (int i = 0; i <= 10; i++) {
    printf("#\n");
  }
}
```

What we want is to print just 10 hashes, but it print 11 hashes, which behave differently than we intended.

### 2.2. Printf

Since this program is compiling without any errors, so we now have a logical error. **Printf** is a useful also simple tool for debugging, which we known it's a function in the library of `stdio`.

We can simply add another `printf` temporarily:

```c
#include <stdio.h>

int main (void) {
  // Print 10 hashes
  for (int i = 0; i <= 10; i++) {
    printf("i is now %i\n", i);
    printf("#\n");
  }
}
```

Then we can see what value of `i` is, it started at 0 and continued until it was 10, which sum of counts is 11 instead of 10 as we intended.

So the problem is caused by the conditional expression of `for` loop, we should use `i < 10` instead of `i <= 10`.

### 2.3. Debuggers

In the CS50 IDE, we have another tool, **`debug50`**, to help us debug programs. This is a tool written by staff that's built on a standard tool called **gdb**.

Both of these **debuggers** are programs that will run our own programs step-by-step and let us look at variables and other information while our program is running.

We'll run the command `debug50 ./buggy0`, and it will tell us to recompile our program since we changed it. Then it'll tell us to add a **breakpoint**, or indicator for a line of code where debugger should pause our program.

### 2.4. Duck debugger

We can also use `ddb` , short for "duck debugger", a real technique where we explain what we're trying to do to a rubber duck, and oftentimes we'll **realize** our own mistake in logic or implementation as we're explaining it.

## 3. Memory

### 3.1. Spaces of different types

In C, we have different types of variables we can use for **storing data**, and each of them take up a fixed amount of space. Different computer systems actually **vary** in the amount of space actually used for each type, but we'll work with the amounts here, as used in the CS50 IDE.

- bool	   1	 byte
- char	   1	 byte
- double  8	 bytes
- float 	 4 	bytes
- int		 4 	bytes
- long	  8	 bytes
- string 	? 	bytes
- ...

### 3.2. RAM v.s. Hard Drive

**RAM** is a chip insider our computer, short for **random-access memory**, that stores data for **short-term** use, like a program's code while it's running, or a file while it's open.

We might save a program or file to our **hard drive** (or SSD, solid state drive) for **long-term storage**, but use RAM because it's much **faster**. However, RAM is **volatile**, or requires power to keep data stored.

### 3.3. Bytes in RAM

We can think of **bytes** stored in RAM as though they were in a **grid**:

![ram](https://cs50.harvard.edu/x/2021/notes/2/ram.png)

In reality, there are millions(1e6) or billions(1e9) of bytes per chip. Each bytes will have a location on the chip, like the first byte, second byte, and so on.

In C, when we create a variable of type `char`, which will be sized one byte, it will physically be stored in one of those boxes in RAM. An integer as the type of `int` with 4 bytes, will take up four of those boxes.

## 4. Arrays

### 4.1. What is Arrays

It turns out, in memory, we can store variable one after another, back-to-back, and access them more easily with loops. In C, a list of values stored one after another contiguously is called an **array**.

### 4.2. Access arrays

We can assign and use variables in an array with `array[index] = value`. With the brackets, we're indexing into, or going to, the "0th" position in the array. Arrays are **zero-indexed**, which meaning that the first value has index 0, and then the second value has index 1, and so on.

### 4.3. Create Arrays

When we create or declare an array, we need an integer value to represent the length of array. E.g., `int array[3];`.

Conveniently, we could use a **constant**, or variable with a fixed value to replace this integer value. I.e.,

```c
const int LENGTH = 3;

int main(void) {
    int array[LENGTH];
}
```

The keyword `const` is to tell the compiler that the value of this variable should **never be changed** by our program. And by convention, we'll place our declaration **outside** of the `main` function and **capitalize** its name, which isn't necessary for the compiler but shows other humans that this variable is a constant and makes it easy to see from the start.

## 5. Characters

### 5.1. Char as an integer

If we print out a single character like this way:

```c
char c = '#';
printf("%c", c);
```

We'll get `#` printed in the terminal.

But if we change it to print `c` as an integer:

```c
char c = '#';
printf("%i", (int) c);
```

We get `35` printed. It turns out that `35` is indeed the **ASCII** code for a `#` symbol. And in fact, we don't need to cast a `char` to an `integer` explicitly when we print a char as an integer, the compiler can do that for us in this case.

```c
char c = '#';
printf("%i", c);	// still printed 35
```

### 5.2. Char in memory

A `char` is a single byte as we mentioned in 3.1, so we can picture it as being stored in one box in the grid of memory above.

## 6. Strings

### 6.1. String as an array of chars

String is a text, or some characters. We already know that characters are stored in memory taking one byte storing some value. E.g., `HI!` is representing like this in memory:

![grid with 72, 73, and 33 labeled c1, c2, and c3](https://cs50.harvard.edu/x/2021/notes/2/characters.png)

**Strings** are actually just arrays of characters, and defined not in C but by the CS50 library. If we had an array called `s`, each character can be accessed with `s[0]`.

### 6.2. Difference between String and arrays of chars

The same text `HI!`, there is a difference between them. It turns out that a string ends with a **special character**, `\0`, or a byte with all bits set to 0. This character is called the **NULL character**, or NUL. Therefore, we actually need one more byte than an array of chars to store same text.

![grid with H labeled s[0], I labeled s[1], ! labeled s[2], \0 labeled s[3]](https://cs50.harvard.edu/x/2021/notes/2/string.png)

If we print out each char of string as integer, we'll get that `72 73 33 0` as we expected.

And in fact, we could try to access `s[4]` which should not be accessed by us and even may not existence, but we still can see some unexpected symbol printed.

With C, our code has the ability to access or change memory that it otherwise shouldn't, which is both powerful and dangerous.

### 6.3. Arrays of Strings

We can also declare an array of strings, which we can understand it as the array of text, also as two-dimensional arrays of characters.

```c
string words[2];
words[0] = "HI!";
words[1] = "BYE!";
```

And in memory, the array of strings might be stored and accessed with:

![grid with H labeled words[0][0], I labeled words[0][1], and so on, until words[1][4] with a \0, each of which takes up one box, and empty boxes following](https://cs50.harvard.edu/x/2021/notes/2/array_of_strings.png)

`words[0]` refers to the first element, or value, of the `words` array, which is a string, and so `words[0][0]` refers to the first element in that string, which is a character.

So an array of strings can be understood as an array of arrays of characters, which also called two-dimensional arrays of characters.

There're some useful functions of strings, such as `strlen()` in `string.h`, and `toupper()` in `ctype.h`.

## 7. Command-line arguments

### 7.1. argc and argv

Programs of our own can also take in command-line arguments, or words added after our program's name in the command itself.

In `argv.c`, we change what our `main` function looks like:

```c
#include <cs50.h>
#include <stdio.h>

int main(int argc, string argv[]) { // instead of void
    if (argc == 2) {
        printf("hello, %s\n", argv[1]);
    } else {
        printf("hello, world\n");
    }
}
```

`argc` and `argv` are two variables that our `main` function will now get automatically when our program is run from command line. `argv` is the short form for **argument count**, and `argv`, which is **argument list** (or argument list), is an array of strings.

The first argument is `argv[0]`, which is the name of our program (the first word typed, like `./hello`). In this example, we check if we have two arguments, and print out the second one if so.

E.g., if we run `./argv Lee`, we'll get `hello, Lee` printed, since we typed in `Lee` as the second word in our command.

### 7.2. Return value

It turns out that our `main` function also returns an **integer value**. By default, our `main` function returns **0** to indicate nothing went wrong, but we can write a program to return a different value:

```c
#include <stdio.h>
#include <cs50.h>

int main (int argc, string argv[]) {
    if (argc != 2) {
        printf("Missing command-line argument!\n");
        return 1;
    }
    printf ("Hello, %s\n", argv[1]);
    return 0;
}
```

The return value of `main` in our program is called an **exit code**, usually used to indicate error codes. (We'll write `return 0` explicitly at the end of our program here, even though we don't technically need to.)

As we write more complex programs, error codes like this can help us determine what went wrong, even if it's not visible or meaningful to the user.

## 8. Applications

Now that we know how to work with strings in our programs, as well code written by others in libraries, we can analyze paragraphs of text for their level of readability, based on factors like how long and complicated the words and sentences are.

### 8.1. Cryptography

**Cryptography** is the art of scrambling, or hiding information. If we wanted to send a message to someone, we might want to **encrypt**, or somehow scramble that massage so that it would be hard for others to read. 

The original message, or input to out algorithm, in called **plain-text**, and the encrypted message, or output, is called **cipher-text**. And the algorithm that does the scrambling is called a **cipher**. A cipher generally requires another input in addition to the plain-text, which called **Key**, like a number, is some other input that is kept secret.

### 8.2. Example of Cryptography

If we wanted to send a message like `I LOVE YOU`, we can first convert it to ASCII: `73 76 79 86 69 89 79 85`. Then, we can **encrypt** it with a **key** of just `1` and a simple **algorithm**, where we just add the key to each value: `74 77 80 87 70 90 80 86`. Then, the **cipher-text** after we convert the values back to ASCII would be `J MPWF ZPV`. To decrypt this, someone would have to know the **key** is `1`, and to subtract it from each character,