## 1. Searching

It turns out that, with arrays, computers cannot look at all of the elements at once. Computers can only **do one thing at a time,** such as look at one element of the array.

Actually humans are also cannot do this, because there if only are several elements of the array, we could have a glance to see all of this, but how if we have billion or even trillion elements of array? And even there are only several elements, we still look at one element at a time, only we're too fast to sense that.

**Searching** is how we solve problem of finding a particular value. A simple case might have an input of some array of values, and the output might simply be a `bool` value, whether or not a particular value is in the array.

### 1.1. Running time, Big *O*, Big Ω

**Running time** is how long an algorithms takes to run given some size of inout.

And in week 0 we use the notation such as **log~2~n**, **n** and **n/2** to describe running times, the more formal way to describe each of these running times is with **Big *O* notation**, which we can think of as **on the order of**. 

In Big *O* notation, we only care about the **dominant factor**, or largest term.

Also there we have a similar notation called **Big Ω** ,big Omega notation, which is the **lower bound** of number of steps for our algorithms, or the best case. **Big *O*** is the **upper bound** of number of steps, or the worst case.

There are some common running times in **Big *O*** and **Big Ω**:

- ***O*(n^2^)**
- ***O*(n*log n)**
- ***O*(n)**
- ***O*(log n)**
- ***O*(1)**
  - An algorithm that takes a **constant** number of steps, regardless of how big the problem is.

And:

- **Ω(n^2^)**
- **Ω(n*log n)**
- **Ω(n)**
- **Ω(log n)**
- **Ω(1)**

### 1.2. T function vs Big O

When discussing an algorithm, the common usage of the symbol  𝑇  is to represent its time complexity.

𝑇  is a function. The input to this function is the input size, the output is the (worst-case) running time for an input of that size.

Therefore,  𝑇(𝑛)  is a number: the number returned by the function  𝑇  when given the number  𝑛 . This number is the running time of our algorithm on an input of size  𝑛 .

The  𝑂  in " 𝑂(𝑛) " is not a function. This is Big O notation. In particular, the symbol  𝑂(𝑛)  represents the class of all functions that grow (asymptotically) at most as quickly as the linear function  𝑓(𝑛)=𝑛 .

Big O notation gives us a convenient way to talk about upper bounds. For example, we can say "the time complexity of this algorithm is  𝑂(𝑛2) " (formally: " 𝑇∈𝑂(𝑛2) ") to say that the running time of the algorithm is at most quadratic in the input size.

Sometimes, you can see both symbols being used in a single equation. For example, the time complexity of MergeSort is commonly described by the following recurrence: " 𝑇(𝑛)=2𝑇(𝑛/2)+𝑂(𝑛) ".

The meaning of the above statement: For any  𝑛 , the time  𝑇(𝑛)  needed to sort  𝑛  elements can be computed by taking the time  𝑇(𝑛/2)  needed to sort  𝑛/2  elements, multiplying that time by 2, and adding something extra. That something extra must be at most linear in  𝑛 .

### 1.3. Linear search, binary search

If we want to look for some number in an array of numbers, the simplest algorithm would be check each of these numbers from left to right, that is **linear search**.

```pseudocode
For i from 0 to n-1:
	If the i'th of array is number:
		Return true
Return false		// Outside the for loop
```

- Input: 
  - a number we wanna find
  - an array of numbers
- Output:
  - a boolean value of whether our number is found
- Running time:
  - *O*(n)
  - Ω(1)

If we know that the numbers in this array are sorted, then we can start in the middle, and find our value more efficiently, that is **binary search**.

```pseudocode
If no numbers:
	Return false
If the middle of array is number:
	Return true
Else if middle number > number:
	Search left half
Else if middle number < number:
	Search right half
```

The input and output is same as the previous.

- Running time:
  - *O*(log n)
  - Ω(1)

We perform steps at a frequency of one **hertz** (Hz), or cycle per second, and a processor's speed might be measured in gigahertz (GHz), or billion of operations per second. So the more efficient algorithms we choose, the more operations we could perform.

## 2. Structs

If we wanted a type of variable more complex, such as a person in phone book, which contains their name and phone number, the basic types in C will not adequate.

It turns out in C that we define our own data type, or **data structure**, with a **struct** in the following syntax:

```c
typedef struct {
    string name;
    string number;
} person;
// Using the dot operator "." to use the variable inside structs.
person p;
p.name = "Use dot";
```

We can see that one struct contains other data types inside it.

With structs, we can be a little more confident that we won't have human errors in our program, which means we'll improve the **robustness** of our programs.

## 3. Sorting

If out input is an **unsorted** list of numbers, there are many algorithms we could use to produce an output of a **sorted** list, where all the elements are in order.

### 3.1. Tradeoff

With a sorted list, we can use binary search for efficiency, but it might take more time to write a sorting algorithm for that efficiency, so sometimes we'll encounter the **tradeoff** of time it takes a human to write a program compared to the time it takes a computer to run some algorithm.

Other tradeoffs we'll see might be **time** and **complexity**, or **time** and **memory usage**.

### 3.2. Selection sort

**Selection sort** is a simple sorting algorithm, and its pseudocode here:

```pseudocode
For i from 0 to n-1
	Find smallest item between i'th item and last item
	Swap smallest item with i'th item
```

For this algorithm, we were looking at roughly all `n` elements to find the smallest currently, and making `n` passes to sort all the elements.

Its running time is ***O*(n^2^)**.

- First we have to look at all n elements, then n-1, then n-2, and so on until there is only one element we need to look at.
- n + (n - 1) + (n - 2) + ... + 1 = n^2^/2 + n/2 = ***O*(n^2^)**

And its lower bound of running time is **Ω(n^2^)**.

- The best case with a sorting algorithm is that the list of input has been sorted.
- Even though the list was sorted, **Selection sort** still need finding the smallest element n times, that still has the running time of **n^2^**.

### 3.3. Bubble sort

There also is a simple sorting algorithm called **Bubble sort**, where we swap pairs of numbers repeatedly.

Here's its pseudocode:

```pseudocode
Repeat n-1 times
	For i from 0 to n-2	// Since we're comparing the i'th and i+1'th
		If i'th and 1+i'th elements out of order
			Swap them
	If no swaps
		Quit
```

- And we can stop as soon as the list is sorted, since we can just remember whether we made any swaps. If not, the list must be stored already. 

This algorithm has ***O*(n^2^)**.

- We have `n-1` comparisons in the loop, and at most `n-1` loops, so we get **n^2^-2n+1** steps total, where the largest factor is **n^2^**.

The lower bound for running time this algorithm would be **Ω(n)**.

- Once we look at all the elements is sorted we could quit.

## 4. Recursion

**Recursion** is the ability for a function to call itself.

### 4.1. Merge sort

We can take the idea of **recursion** to sorting, with another algorithm called **Merge sort**.

The pseudocode might look like:

```pseudocode
If only one number
	Return
Else
	Sort left half of number
	Sort right half of number
	Merge sorted halves
```

Notice the line 1 in pseudocode, this is **recursion terminal condition**. Once we haven't **recursion terminal condition**, the recursion will endless running.

The magic of this algorithm is the line 6. It will return if there's only one element, then we could merge sorted halves, where the halves both are only one element, and of course is sorted, then we can back to the stage which  invoked this stage, and then merge, and so forth.

Each merging steps required `n` steps, there are `log n` stages containing merging, so total time is `n*log n`.

So the upper bound for running time is ***O*(n * log n)**.

And the lower bound is **Ω(n * log n)**, since we still have to sort each half first and then merge them together.

### 4.2. Tradeoff again

Even though **merge sort** is likely to be faster than **selection sort** or **bubble sort**, we do need **more memory** to temporarily store our merged list at each stage.

We face the **tradeoff** of incurring a higher cost, another array in memory, for the benefit of faster sorting.

## 5. Another notation

Finally, there's another notation, **θ**，Theta, which we use describe running times of algorithms if **the upper bound and lower bound is the same**.

For example, **merge sort** has **θ（n logn)**, and **selection sort** has **θ(n^2^)**.
