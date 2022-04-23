## 1. Python basics

### 1.1. What is Python

**Python** is a programming language, which is a newer language than **C**, it has additional features as well as simplicity, leading to its popularity.

### 1.2. Hello World

Source code in Python looks a lot simpler than C. In fact, to print "Hello World" just like what we've done in C, all we need to write is:

```python
print("Hello World")
```

- Notice that, unlike in C, we don't need to specify a **newline** in the `print` function or use a **semicolon** to end our line.
- Furthermore, to write and run this program, we'll just save this source code file as `hello.py`, and run the command `python hello.py` without any compilation.

### 1.3. Input & Output

We can get **input** from user, such as `string`:

```python
answer = get_string("What's your name? ")
print("hello, " + answer)
```

- We also need to **import** the Python version of the CS50 library with `import cs50`, where `get_string` function is in it, similar to `#include <cs50.h>` in C.
- But in this case, we need to import this way: `from cs50 import get_string`, we'll explain it later.

Notice that, we create a variable called `answer` without specifying any **type**, and we can combine, or **concatenate**, two strings with the `+` operator before we pass it into `print`.

We can also use the syntax for **format strings** in **output**, `f"...{variable}..."` to plug in variables.

E.g., we could have written `print(f"hello, {answer}")` to plug in the value of `answer` into our string by surrounding it with **curly braces**.

### 1.4. Implicitly setting the type

We can create variables with just `counter = 0`. By assigning the value of `0` , we're implicitly setting the type of `counter` to an integer, so we don't need to specify the type.

To increment a integer variable, we can use `counter = counter +  1` or `counter += 1` like C, but there's no `counter++` in Python.

### 1.5. Conditions and Boolean expressions

Conditions look like:

```python
if x < y:
    print("x is less than y")
elif x > y:
    print("x is greater than y")
else:
    print("x is equal to y")
```

- Unlike in C, where curly braces are used to indicate blocks of code, the **exact indentation** of each line is what **determines the level of nesting** in Python.
- And instead of `else if` , just `elif`.

Boolean expressions are slightly different, too:

```python
while True:
    print("True instead of true")
```

- Both `True` and `False` are **capitalized** in Python

### 1.6. Loops

We can write a loop with a variable:

```python
i = 0
while i < 3:
    print(f"now i is {i}")
    i += 1
```

We can also use a **for** loop, where we can do something for each value in a list:

```python
for i in [0, 1, 2]:
    print("hi")
```

- List in Python, `[0, 1, 2]`, are like arrays in C.

But if the number of loop becomes larger, this way gonna be inelegant. Instead, we can use a specific function called `range(n)`, which will give us a list from `0` up to `n` but exclude `n`, with the values of `0, 1, ..., n - 1`.

```python
for i in range(3):
    print("elegant")
```

`range()` function takes **other options** as well, so we can have lists that start at different values and have different increments between values. E.g., `range(10, 101, 2)` means give us a sequence of number start from `10` up to `101` but exclude `101`, incrementing by `2` at a time.

### 1.7. Pythonic

Since there are often multiple ways to write the same code in Python, the most commonly used and accepted ways are called **Pythonic**.

### 1.8. Built-in data types and Loosely typed

In Python, there're many **built-in data types**:

- `bool`, Boolean values, either `True` or `False`
- `float`, real numbers
- `int`, integers
- `str`, strings

While C is a **strongly typed** language, where we need to specify types, Python is a **loosely typed** language, where the type is implied by the values we assigned.

Other types in Python include:

- `range`, sequence of numbers
- `list`, sequence of mutable values, or values we can change
  - And list, even though they're arrays in C, can grow and shrink automatically in Python
- `tuple`, collection of ordered values like x- and y-coordinates, or longitude and latitude
- `dict`, dictionaries, collection of key-value pairs, like a hash table
- `set`, collection of unique values, or values without duplicates

### 1.9. CS50 library

The CS50 library for Python include:

- `get_float`
- `get_int`
- `get_string`

As we mentioned, we can import one at a time:

```python
from cs50 import get_float
from cs50 import get_int
from cs50 import get_string
```

or all together:

```python
from cs50 import get_float, get_int, get_string
```

or import the whole library:

```python
import cs50
```

If our program needed to import two different libraries, each with a `get_int` function, for example, we would need to use this method to **namespace** functions, keeping their names in different spaces to prevent them from colliding.

## 2. Details

### 2.1. Use others' libraries

Since Python includes many features as well as libraries of code written by others, we can solve problem at a **higher level of abstraction**, instead of implementing all the details ourselves.

There's some differences between C and Python in using libraries.

E.g., we can blur an image with:

```python
from PIL import Image, ImageFilter

# Open an image called "bridge.bmp"
before = Image.open("bridge.bmp")
# Call a bulr filter function
after = before.filter(ImageFilter.BoxBlur(1))
# Save it to a file called "out.bmp"
after.save("out.bmp")
```

- In Python, we include other libraries with `import` just as we mentioned last chapter, and here we'll `import` the `Image` and `ImageFilter` from `PIL` library. (Other people have written this library, among others, and made it available for all of us download and use.)

`Image` is a structure that not only has data, **but functions that we can access** with the `.` operator, such as with `Image.open()`.

Finally, we can run this with `python blur.py` after saving to a file called `blur.py`.

### 2.2. Interpreter

We can implement a dictionary like we did in week 5 with:

```python
# Create a new set called words
words = set()

# Define a function called load
def load(dictionary):
    # open dictionary with read mode as variable "file"
    file = open(dictionary, "r")
    # iterate over the lines in file
    for line in file:
        # remove the newline character at the end of line with .rstrip()
        words.add(line.rstrip())
	file.close()
    return True

def check(word):
    # check if the word in the words
    if word.lower() in words:
        return True
    else:
        return False

def size():
	# we can use len() function get the length of an array(or a set) directly
    return len(words)

def unload():
    # There is no longer memory management like malloc() and free()
    # so it gonna be always True
    return True
```

It turns out, even though implementing a program in Python is simpler for us, the **running time** in Python is **slower** in C since the language has to do more work for us with general-propose solutions, like for memory management.

In addition, Python is also the name of a program called an **interpreter**, which reads in our source code and **translates** it to code that our CPU (Central Processing Unit) can understand, line by line, like machine code compiled by a compiler in C.

E.g., consider that there is a text written in Spanish, and you can't understand Spanish, if we translate it into English, we probably could read it as fast as usual; but if we only have a Spanish-English dictionary, we have to look up every word of this text in dictionary, which gonna be very slow.

So, depending on our goals, we'll also have to consider the **trade-off** of *human time* of writing a program that's more efficient, versus the *running time* of the program.

### 2.3. Input

We can get input from the user with the `input` function without using CS50 library:

```python
answer = input("What's your name? ")
print(f"Hello, {answer}")
```

Also we can ask the user for two integers using `input` function and add them:

```python
x = input("Integer x: ")
# x = 1
y = input("Integer x: ")
# y = 3
print(x + y)
# output is 13 (it's a string)
```

- Notice that comments starts with `#` instead of 

The `input` function give us back strings for our values, to fix this we need to **cast**, or convert, each value from `input` into an `int` before we store it:

```python
# Prompt user for x
x = int(input("x: "))

# Prompt user for y
y = int(input("y: "))

# Perform addition
print(x + y)
```

- But if user didn't type in a number, we'll need to do even more error-checking or our program will crash. So we will generally want to use a commonly used library to solve problems like this.

### 2.4. Divisions and String comparisons

We can divide values with:

```python
# Prompt user for x
x = int(input("x: "))

# Prompt user for y
y = int(input("y: "))

# Perform division
print(x / y)
```

We won't get **truncation** just like in C, but a floating-point, decimal value back, even if we divided two integers.

To compare strings, we can say:

```python
from cs50 import get_string

s = get_string("Do you agree? ")

if s == "Y" or s == "y":
    print("Agreed.")
elif s == "N" or s == "n":
    print("Not agreed.")
```

- Python doesn't have `char`, so we check `Y` and other letters as `strings`.

- We can also compare strings directly with `==` since there're no longer pointers in Python.

- In our boolean expressions, we use `or` and `and` instead of symbols like `&&` and `||`.

We can also say `if s.lower() in ["y", "yes"]:` to check if our string is in a list, after converting it to lowercase first.

### 2.5. Functions and the Main function

Recall that we implemented and improved the `meow()` function in C, we can improve versions of `meow` too.

```python
print("meow")
print("meow")
print("meow")
```

We can define a function that we can reuse:

```python
for i in range(3):
    meow()
    
def meow():
    print("meow")
```

- But this causes an error when we try to run it: `NameError: name 'meow' is not defined.`
- It turns out that we need to define a function before we use it, so we can either move our definition of `meow()` function to the top, or define a `main()` function first.

```python
def main():
    for in in range(3):
        meow()
        
def meow():
    print("meow")
    
main()
```

- Now, by the time we actually call our `main` function, the `meow` function will already have been defined.

Our functions could take inputs, too:

```python
def main():
    meow(3)
    
def meow(times):
    for i in range(times):
        print("meow")
        
main()
```

### 2.6. Variables scope

We can define a function to get a positive integer:

```python
from cs50 import get_int

def main():
    i = get_positive_int()
    print(i)
    
def get_positive_int():
    while True:
        n = get_int("Positive integer: ")
        if n > 0:
            break
	return n

main()
```

Since there's no `do-while` loop in Python as there is in C, we have a `while ` loop that will go on infinitely, and use `break` to end the loop as soon as `n > 0`. Finally, our function will `return n`, at our original indentation level, outside of the `while` loop.

Notice that, the variables in Python are **scoped to functions** by default, meaning that `n` can be initialized within a loop, but still be accessible later in the function.

```python
for i in range(3):
    print(i)
# the variable i is still accessible
print(i)
```

### 2.7. Named arguments

We can print out a row of question marks on the screen:

```python
for i in range(4):
    # named argument "end = "
    print("?", end="")
# add a newline
print()
```

It turns out that `print()` function will print a newline character automatically by default, if we don't want the automatic new line, we can pass a **named argument**, also known as keyword argument, to the `print` function, which specifies the value for a specify parameter.

So far, we've only seen **positional arguments**, where parameters are set based on their position in the function call just like in C.

Here, we say `end=""` to specify that nothing should be printed at the end of our string. `end` is also an **optional argument**, one we don't need pass in, with a default value of `\n`, which is why `print` usually adds a new line for us.

We can also "multiply" a string and print that directly with `print("?" * 4)`.

### 2.8. Overflow, Imprecision

In Python, trying to cause an integer overflow actually won’t work:

```python
i = 1
while True:
    print(i)
    i *= 2
```

We see larger and larger numbers being printed, since Python automatically uses more and more memory to store numbers for us, unlike C where integers are fixed to a certain number of bytes.

Floating-point imprecision, too, **still exists**, but can be prevented by libraries that can represent decimal numbers with as many bits as are needed.

### 2.9. Lists, strings

We can make a list:

```python
scores = [72, 73, 33]

print("Average: " + str(sum(scores) / len(scores)))
```

We can use `sum`, a function built into Python, to add up the values in our list, and divide it by the number of scores, using the `len` function to get the length of the list. Then, we cast the float to a string before we can concatenate and print it.

We can even add the entire expression into a formatted string for the same effect:

```python
print(f"Average: {sum(scores) / len(scores)}")
```

We can add items to a list with:

```python
from cs50 import get_int

scores = []
for i in range(3):
    scores.append(get_int("Score: "))
...
```

We can iterate over each character in a string:

```python
from cs50 import get_string

s = get_string("Before:  ")
print("After: ", end="")
for c in s:
    print(c.upper(), end="")
print()
```

- Python will iterate over each character in the string for us with just `for c in s`.

- To make a string uppercase, we can also just call `s.upper()`, without having to iterate over each character ourselves.

### 2.10. Command-line arguments, exit codes

We can take **command-line arguments** with:

```python
from sys import argv

if len(argv) == 2:
    print(f"hello, {argv[1]}")
else:
    print("Hello, World")
```

- We import `argv` from `sys`, or system module, built into Python.
- Since `argv` is a list, we can get the second item with `argv[1]`.
- Like in C, `argv[0]` would be the name of our program, like `argv.py`.

We can return **exit codes** when our program exits, too:

```python
import sys

if len(argv) != 2:
    print("Missing command-line argument.")
	# same as return 1 in C
    sys.exit(1)
print(f"hello, {world}")
sys.exit(0)
```

We import the entire `sys` module now, since we’re using multiple components of it. Now we can use `sys.argv` and `sys.exit()` to exit our program with a specific code.

### 2.11. Algorithms

We can implement linear search by just checking each element in a list:

```python
import sys

number = [4, 6, 8, 9, 0, 10, 12]

if 0 in number:
    print("Found.")
    sys.exit(0)

print("Not found.")
sys.exit(1)
```

- With `if 0 in numbers:`, we’re asking Python to check the list for us.

A list of strings, too, can be searched with:

```python
names = ["Bill", "Charlie", "Fred", "George", "Ginny", "Percy", "Ron"]

if "Ron" in names:
    print("Found")
else:
    print("Not found")
```

If we have a dictionary, a set of key-value pairs, we can also check for a particular key, and look at the value stored for it:

```python
from cs50 import get_string

people = {
    "Brian": "+1-617-495-1000",
    "David": "+1-949-468-2750"
}

name = get_string("Name: ")
if name in people:
    print(f"Number: {people[name]}")
```

- We first declare a dictionary, `people`, where the keys are strings of each name we want to store, and the value we want to associate with each key is a string of a corresponding phone number.
- Then, we use `if name in people:` to search the keys of our dictionary for a `name`. If the key exists, then we can get the value with the bracket notation, `people[name]`, much like indexing into an array with C, except here we use a string instead of an integer.
- Dictionaries, as well as sets, are typically implemented in Python with a data structure like a hash table, so we can have close to constant time lookup. Again, we have the tradeoff of having less control over exactly what happens under the hood, like being able to choose a hash function, with the benefit of having to do less work ourselves.

Swapping two variables can also be done simply by assigning both values at the same time:

```python
x = 1
y = 2

print(f"x is {x}, y is {y}")
x, y = y, x
print(f"x is {x}, y is {y}")
```

- In Python, we don’t have access to pointers, which protects us from making mistakes with memory.

## 3. Files

### 3.1. CSV file

Let's open a CSV file (comma-separated values):

```python
import csv

from cs50 import get_string

file = open("phonebook.csv", "a")

name = get_string("Name: ")
number = get_string("Number: ")

writer = csv.writer(file)
writer.writerow([name, number])

file.close()
```

It turns out that Python also has a `csv` library that helps us work with CSV files, so after we open the file for **a**ppending, we can call `csv.writer` to create a `writer` from the file, which gives additional functionality, like `writer.writerow` to write a list as row.

### 3.2. with and as keyword

We can use the `with` keyword, which will close the file for us after we're finished.

```python
# after get name and number from user's input
with open("phonebook.csv", "a") as file:
    writer = csv.writer(file)
    writer.writerow((name, number))
```

We can open another CSV file, tallying the number of times a value appears:

```python
import csv

houses = {
    "Gryffindor": 0,
    "Hufflepuff": 0,
    "Ravenclaw": 0,
    "Slytherin": 0
}

with open("Sorting Hat (Responses) - Form Responses 1.csv", "r") as file:
    reader = csv.reader(file)
    next(reader)
    for row in reader:
        house = row[1]
        houses[house] += 1

for house in houses:
    print(f"{house}: {houses[house]}")
```

We use the `reader` function from the `csv` library, skip the header row with `next(reader)`, and then iterate over each of the rest of the rows.

The second item in each row, `row[1]`, is the string of a house, so we can use that to access the value stored in `houses` for that key, and add one to it.

Finally, we’ll print out the count for each house.

## 3. More libraries

### 3.1. pyttsx3 library

On our own Mac or PC, we can open a terminal after installing Python, and use another library to convert text to speech:

```python
import pyttsx3

engine = pyttsx3.init()
engine.say("hello, world")
engine.runAndWait()
```

- By reading the documentation, we can figure out how to initialize the library, and say a string.
- We can even pass in a format string with `engine.say(f"hello, {name}")` to say some input.

### 3.2. face_recognition

We can use another library, `face_recognition`, to find faces in images:

```python
# Find faces in picture
# https://github.com/ageitgey/face_recognition/blob/master/examples/find_faces_in_picture.py

from PIL import Image
import face_recognition

# Load the jpg file into a numpy array
image = face_recognition.load_image_file("office.jpg")

# Find all the faces in the image using the default HOG-based model.
# This method is fairly accurate, but not as accurate as the CNN model and not GPU accelerated.
# See also: find_faces_in_picture_cnn.py
face_locations = face_recognition.face_locations(image)

for face_location in face_locations:

    # Print the location of each face in this image
    top, right, bottom, left = face_location

    # You can access the actual face itself like this:
    face_image = image[top:bottom, left:right]
    pil_image = Image.fromarray(face_image)
    pil_image.show()
```

With [recognize.py](https://cdn.cs50.net/2020/fall/lectures/6/src6/6/faces/recognize.py), we can write a program that finds a match for a particular face.

### 3.3. qrcode

We can create a QR code, or two-dimensional barcode, with another library:

```python
import os
import qrcode

img = qrcode.make("https://youtu.be/oHg5SJYRHA0")
img.save("qr.png", "PNG")
os.system("open qr.png")
```

### 3.4. speech_recognition

We can recognize audio input from a microphone:

```python
import speech_recognition

# Obtain audio from the microphone
recognizer = speech_recognition.Recognizer()
with speech_recognition.Microphone() as source:
    print("Say something:")
    audio = recognizer.listen(source)

# Recognize speech using Google Speech Recognition
print("You said:")
print(recognizer.recognize_google(audio))
```

We’re following the documentation of the library to listen to our microphone and convert it to text.

```python
...
words = recognizer.recognize_google(audio)

# Respond to speech
if "hello" in words:
    print("Hello to you too!")
elif "how are you" in words:
    print("I am well, thanks!")
elif "goodbye" in words:
    print("Goodbye to you too!")
else:
    print("Huh?")
```

- Finally, we use another, more sophisticated program to generate deepfakes, or realistic-appearing but computer-generated videos of various personalities.
- By taking advantage of all these libraries that are freely available online, we can easily add advanced functionality to our own applications.
