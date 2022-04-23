## 1. Hexadecimal

### 1.1. What is it

In week2 (`Arrays`), we knew that each **byte** has an **address**, or **identifier**, so that we can refer to where our data is actually stored.

It turns out that, by convention, the addresses for memory use the counting system **hexadecimal**, or **base-16**, where there are 16 digit: `0~9`, and `A~F` represent `10~15`.

```c
0x0F
(0 * 16^1) + (F * 16^0) == 15 (base 10)
```

In this example, `0x0F` is `15` in decimal, where `0x` is the **prefix** for hexadecimal.

### 1.2. Why use it

**One digit** in hexadecimal is equal to **four digits** in binary, which binary is the counting system used by computers. (Each digit in hex with 16 different values, which map to four bits in bin, i.e. 2^4 = 16)

Consequently, we could conveniently represent a **byte** in binary by using two digit in hex.

### 1.3. Application

Due to each **byte** has an **address**, we'll use hexadecimal for **each address or location** for computer's memory.

Also the RGB color system conventionally uses hexadecimal to describe the amount of color, i.e. `0xRRGGBB`.

E.g., `0x00 00 00` in hex represents 0 for each of red, green and blue, for a combined color of black. And `0xFF 00 00` would be 255 for red, which is the highest possible of amount of red. Similarly, `0xFF FF FF` is the highest value of each color, combined to the brightest color white.

## 2. Addresses and Pointers

### 2.1. What are they

We know that different variable may have a different type, e.g. `int`, `double` etc. Besides, the variables are stored somewhere in the computer's memory, just like the picture below.

The address where stored this variable might look `0x12345678`, and the type of address value is called the **pointer**, we can save the address of this variable in a pointer variable p, which we can think of as a value that "points" to a location in memory.

![grid representing bytes, with four boxes together containing 50 with small 0x123 underneath, and eight boxes together containing 0x123 with small p underneath](https://cs50.harvard.edu/x/2021/notes/4/p.png)

To declare a pointer, we can use `*` (also refer to dereference operator).

```c
int n = 50;		// declare a integer variable n
int *p = &n;	// declare a pointer refer to an integer variable and assign &n to it
```

This perhaps be confusing, `*p` is the dereferenced value of `p`, but we assign a value to it.

```c
int array[50] = {element...};
```

This is similar to the initialization of an array, where `[50]` is not get the 50th variable, but to declare the length of this array.

Therefore, `int *p` is mean that the type of `*p` is an int, where `*p` is the dereferenced value which is an int, thus `p` is a pointer to the type int. When we initialize it, we assign value to `p` instead of `*p`.

### 2.2. & and dereference operator *

In C, we can actually see the address with the `&` operator, which means **get the address of this variable**. We already know that it is the pointer which the type of address, to print out through `printf` we use `%p` for the **format code** for an address.

```c
int n = 0;
printf("%p", &n);
// Out: 0x123..
```

Correspondingly, the `*` operator, aka **dereference operator**, is to **go to** the location that this pointer points to.

```c
int n = 6;
printf("%i", *&n);
// Out: 6
// the dereferenced value of the address obtained from the variable is itself.
// this is to say, & and * are mutually inverse.
```

### 2.3. the size of pointers

It turns out that `int` have four bytes in computer's memory. Modern computer systems are “64-bit”, meaning that they use 64 bits to address memory, so a pointer will in reality be **8 bytes**, twice as big as an integer of 4 bytes.

## 3. Application of the pointer

### 3.1. Strings

In fact, the CS50 library defines a type that doesn't exist in C, `string`, as `char *`, with `typedef char *string`. It turns out that `string s` is just a pointer, the first address to `s`.

Due to the string in C is ended by `\0` aka `nul`, we can easily find out where the end of this string.

### 3.2. Strings comparison and copying

Once we know that what `string` or `char *` stored is the address of first character in each string, we'll understand that it's wrong to compare two strings through compare their pointers.

![box containing 0x123 labeled s, boxes side by side containing H labeled 0x123, I labeled 0x124, ! labeled 0x125, \0 labeled 0x126, box containing 0x456 labeled t, boxes side by side containing H labeled 0x456, I labeled 0x457, ! labeled 0x458, \0 labeled 0x459](https://cs50.harvard.edu/x/2021/notes/4/s_t.png)

Similarly, if we want to copy a string to another, we cannot just simply assign one string(pointer) to another one (pointer), cause this would make two pointers to the same location instead of copying.

To actually make a copy of a string, we have to do a little more work, and copy each character in original string (`s`) to somewhere else in memory.

```c
#include <cs50.h>
#include <ctype.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main(void)
{
    char *s = get_string("s: ");

    char *t = malloc(strlen(s) + 1);	// size of s + '\0' (terminating null char)

    for (int i = 0, n = strlen(s); i < n + 1; i++)
    {
        t[i] = s[i];
    }

    t[0] = toupper(t[0]);

    printf("s: %s\n", s);
    printf("t: %s\n", t);
}
```

We create a new variable, `t`, of the type of `char *`, with `char *t`. Now we wanna point it to a new chunk of memory that's large enough to store the copy of the string. With `malloc` (memory allocate, in `stdlib`), we allocate some number of bytes in memory (that aren't already used to store other memory), and we pass in the number of bytes we'd like to mark for use.

### 3.3. Error checking

We can add some error-checking to our program:

```c
#include <cs50.h>
#include <ctype.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main(void)
{
    char *s = get_string("s: ");

    char *t = malloc(strlen(s) + 1);
    if (t == NULL)
    {
        return 1;
    }

    for (int i = 0, n = strlen(s); i < n + 1; i++)
    {
        t[i] = s[i];
    }

    if (strlen(t) > 0)
    {
        t[0] = toupper(t[0]);
    }

    printf("s: %s\n", s);
    printf("t: %s\n", t);

    free(t);
}
```

If our computer is **out of memory**, `malloc` will return `NULL`, the null ptr, or a special value that indicate there isn't an address to point to. So we should check for that case, and exit if `t` is `NULL`.

We could also check that `t` has a length, before trying to do something in that memory.

Finally, we should **free** the memory we allocated earlier, which marks it as usable again by some other program. We call the `free` function and pass in the pointer `t`, since we're done with that chunk of memory, we no longer need that chunk of memory.

We can actually also use the `strcpy` function, from the C’s string library, with `strcpy(t, s);` instead of our loop, to copy the string `s` into `t`.

### 3.4. scanf

​	We can get a string using `scanf`.

```c
#include <stdio.h>

int main(void)
{
    char *s;
    printf("s: ");
    scanf("%s", s);
    printf("s: %s\n", s);
}
```

But we haven't actually allocate any memory for `s`, so we need to call `malloc` to allocate some memory to `s`, or `char s[N]`.

Now, if the user types in a string of length 3 or less, our program will work safely. But if the user types in a longer string, `scanf` might be trying to write past the end of our array into unknown memory, causing our program to crash.

### 3.5. Pointer arithmetic

Pointer arithmetic is **mathematical operations** on addresses with pointers.

```c
#include <stdio.h>

int main(void)
{
    char *s = "HI!";
    printf("%c\n", *s);
    printf("%c\n", *(s+1));
    printf("%c\n", *(s+2));
}
```

`*s` goes to the address stored in `s`, and `*(s+1)` goes to the location in memory that the next of the array instead of the next **bit**, this is to say, add the bits of the type have to this address.

We can even try to go to the address in memory that we shouldn't, like with `*(s + 1000000000)`, and when we run our program, we’ll get a **segmentation fault**, or crash as a result of our program touching memory in a segment it shouldn’t have.

## 4. Memory leaks and Garbage values

### 4.1. Memory leaks and Valgrind

`valgrind` is a command-line tool that we can use to run our program and see if it has any **memory leaks**, or memory we've allocated without freeing, which might eventually cause out computer to run out of memory.

### 4.2. Garbage values

```c
int main(void)
{
    int *x;
    int *y;

    x = malloc(sizeof(int));

    *x = 42;
    *y = 13;

    y = x;

    *y = 13;
}
```

With `*y = 13`, we trying to put the value 13 at the address `y` points to. But since we never assigned `y` a value, it has a **garbage value**, or whatever unknown value that was in memory, from whatever program was running in our computer before.

So when we're trying to go to the garbage value in `y` as an address, we're going to some **unknown** address, which is likely to cause a segmentation fault, or segfault.

## 5. Memory layout

### 5.1. What is memory layout

Within our computer's memory, the different types of data that need to be stored for our program are organized into different sections.

![Grid with sections, from top to bottom: machine code, globals, heap (with arrow pointing downward), stack (with arrow pointing upward)](https://cs50.harvard.edu/x/2021/notes/4/memory_layout.png)

- The **machine code** section is our complied program's binary code. When we run our program, that code is loaded into the "top" of memory.
- Just below, or in the next part of memory, are **global variables** we declared in our program.
- The **heap** section is an empty area from where `malloc` can get free memory for our program to use.
  - As we call `malloc`, we starting allocate memory from the top down.
- The **stack** section is used by functions in our program as they called, and grows upward.
  - E.g. the picture below.

![Stack section with (a, b, tmp) labeled swap, above (x, y) labeled main](https://cs50.harvard.edu/x/2021/notes/4/stack.png)

### 5.2. heap/stack/buffer overflow

If we called `malloc` for too much memory then the **heap** section run out of space, we'll have a **heap overflow**, since we end up going past our heap.

Or, if we call too much functions without returning from them, we'll have a **stack overflow**, where our stack has too much memory allocated as well.

A **buffer overflow** occurs when we go past the end of a buffer, some chunk of memory we’ve allocated like an array, and access memory we shouldn’t be.

## 6. File I/O

### 6.1. fopen, fprintf and FILE type

With the ability to use pointers, we can also open **files**, like a digital phone book.

```c
#include <cs50.h>
#include <stdio.h>
#include <string.h>

int main(void)
{
    FILE *file = fopen("phonebook.csv", "a");
    if (file == NULL)
    {
        return 1;
    }

    char *name = get_string("Name: ");
    char *number = get_string("Number: ");

    fprintf(file, "%s,%s\n", name, number);

    fclose(file);
}
```

`fopen` is a new function that we can use to open a file, which will return a pointer to a new type, `FILE`, that we can read from and write to.

- The first argument of `fopen` in this example is **the name of file**, and the second is the **mode** we want to open file in, i.e. `r` for read, `w` for write, `a` for append or adding to.

- Similarly as above, we have to add a check to exit if we couldn't open the file for some reason.

After we get some strings, we could use `fprintf` to print to a file.

Finally, we close the file with `fclose`.

Now we can create our own CSV files, a file of comma-separated values (like a mini-spreadsheet), programmatically.

### 6.2. Graphic

We can read in binary and map them to pixels and colors, to display images and videos. With a finite number of bits in an image file, though, we can only zoom in so far before we start seeing individual pixels.

- With artificial intelligence and machine learning, however, we can use algorithms that can generate additional details that weren’t there before, by guessing based on other data.

Let’s look at a program that opens a file and tells us if it’s a JPEG file, an image file in a particular format:

```c
#include <stdint.h>
#include <stdio.h>

typedef uint8_t BYTE;

int main(int argc, char *argv[])
{
    // Check usage
    if (argc != 2)
    {
        return 1;
    }

    // Open file
    FILE *file = fopen(argv[1], "r");
    if (!file)
    {
        return 1;
    }

    // Read first three bytes
    BYTE bytes[3];
    fread(bytes, sizeof(BYTE), 3, file);

    // Check first three bytes
    if (bytes[0] == 0xff && bytes[1] == 0xd8 && bytes[2] == 0xff)
    {
        printf("Maybe\n");
    }
    else
    {
        printf("No\n");
    }

    // Close file
    fclose(file);
}
```

- First, we define a `BYTE` as 8 bits, so we can refer to a byte as a type more easily in C.
- Then, we try to open a file (checking that we indeed get a non-NULL file back), and read the first three bytes from the file with `fread`, into a buffer called `bytes`.
- We can compare the first three bytes (in hexadecimal) to the three bytes required to begin a JPEG file. If they’re the same, then our file is likely to be a JPEG file (though, other types of files may still begin with those bytes). But if they’re not the same, we know it’s definitely not a JPEG file.

We can even copy files ourselves, one byte at a time now:

```c
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

typedef uint8_t BYTE;

int main(int argc, char *argv[])
{
    // Ensure proper usage
    if (argc != 3)
    {
        fprintf(stderr, "Usage: copy SOURCE DESTINATION\n");
        return 1;
    }

    // open input file
    FILE *source = fopen(argv[1], "r");
    if (source == NULL)
    {
        printf("Could not open %s.\n", argv[1]);
        return 1;
    }

    // Open output file
    FILE *destination = fopen(argv[2], "w");
    if (destination == NULL)
    {
        fclose(source);
        printf("Could not create %s.\n", argv[2]);
        return 1;
    }

    // Copy source to destination, one BYTE at a time
    BYTE buffer;
    while (fread(&buffer, sizeof(BYTE), 1, source))
    {
        fwrite(&buffer, sizeof(BYTE), 1, destination);
    }

    // Close files
    fclose(source);
    fclose(destination);
    return 0;
}
```

- We use `argv` to get arguments, using them as filenames to open files to read from and one to write to.
- Then, we read one byte from the `source` file into a buffer, and write that byte to the `destination` file. We can use a `while` loop to call `fread`, which will stop once there are no more bytes to read.
