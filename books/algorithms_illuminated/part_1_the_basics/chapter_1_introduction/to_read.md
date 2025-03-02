Chapter 1

Introduction

The goal of this chapter is to get you excited about the study of algorithms. We begin by discussing algorithms in general and why they’re so important. Then we use the problem of multiplying two integers to illustrate how algorithmic ingenuity can improve on more straightforward or naive solutions. We then discuss the `MergeSort` algorithm in detail, for several reasons: it’s a practical and famous algorithm that you should know; it’s a good warm-up to get you ready for more intricate algorithms; and it’s the canonical introduction to the “divide-and-conquer” algorithm design paradigm. The chapter concludes by describing several guiding principles for how we’ll analyze algorithms throughout the rest of the book.

### 1.1 Why Study Algorithms?

Let me begin by justifying this book’s existence and giving you some reasons why you should be highly motivated to learn about algorithms. So what is an algorithm, anyway? It’s a set of well-defined rules—a recipe, in effect—for solving some computational problem. Maybe you have a bunch of numbers and you want to rearrange them so that they’re in sorted order. Maybe you have a road map and you want to compute the shortest path from some origin to some destination. Maybe you need to complete several tasks before certain deadlines, and you want to know in what order you should finish the tasks so that you complete them all by their respective deadlines.

So why study algorithms?

**Important for all other branches of computer science.** First, understanding the basics of algorithms and the closely related field of data structures is essential for doing serious work in pretty much any branch of computer science. For example, during my years as a professor at Stanford University, every degree the computer science department offered (B.S., M.S., and Ph.D.) required an algorithms course. To name just a few examples:

1. Routing protocols in communication networks piggyback on classical shortest-path algorithms.

2. Public-key cryptography relies on efficient number-theoretic algorithms.

3. Computer graphics requires the computational primitives supplied by geometric algorithms.

4. Database indices rely on balanced search tree data structures.

5. Computational biologists use dynamic programming algorithms to measure genome similarity.

---
---

1.2 Integer Multiplication

6. Clustering algorithms are one of the most ubiquitous tools in unsupervised machine learning.

And the list goes on.

**Driver of technological innovation.** Second, algorithms play a key role in modern technological innovation. To give just one obvious example, search engines use a tapestry of algorithms to efficiently compute the relevance of various Web pages to a given search query. The most famous such algorithm is the PageRank algorithm, which gave birth to Google. Indeed, in a December 2010 report to the United States White House, the President’s council of advisers on science and technology wrote the following:

> “Everyone knows Moore’s Law — a prediction made in 1965 by Intel co-founder Gordon Moore that the density of transistors in integrated circuits would continue to double every 1 to 2 years. . . in many areas, performance gains due to improvements in algorithms have vastly exceeded even the dramatic performance gains due to increased processor speed.”¹

**Lens on other sciences.** Third, although this is beyond the scope of this book, algorithms are increasingly used to provide a novel “lens” on processes outside of computer science and technology. For example, the study of quantum computation has provided a new computational viewpoint on quantum mechanics. Price fluctuations in economic markets can be fruitfully viewed as an algorithmic process. Even evolution can be thought of as a surprisingly effective search algorithm.

**Good for the brain.** Back when I was a student, my favorite classes were always the challenging ones that, after I struggled through them, left me feeling a few IQ points smarter than when I started. I hope this material provides a similar experience for you.

**Fun!** Finally, I hope that by the end of the book you can see why the design and analysis of algorithms is simply fun. It’s an endeavor that requires a rare blend of precision and creativity. It can certainly be frustrating at times, but it’s also highly addictive. And let’s not forget that you’ve been learning about algorithms since you were a little kid.

### 1.2 Integer Multiplication

#### 1.2.1 Problems and Solutions

When you were in third grade or so, you probably learned an algorithm for multiplying two numbers—a well-defined set of rules for transforming an input (two numbers) into an output (their product). It’s important to distinguish between two different things: the description of the problem being solved, and that of the method of solution (that is, the algorithm for the problem). In this book, we’ll repeatedly follow the pattern of first introducing a computational problem (the inputs and desired output), and then describing one or more algorithms that solve the problem.

¹Excerpt from Report to the President and Congress: Designing a Digital Future, December 2010 (page 71).

---
---

1.2.2 The Integer Multiplication Problem

In the integer multiplication problem, the input is two n-digit numbers, which we’ll call x and y. The length n of x and y could be any positive integer, but I encourage you to think of n as large, in the thousands or even more. (Perhaps you’re implementing a cryptographic application that must manipulate very large numbers.) The desired output in the integer multiplication problem is the product x · y.

> **Problem: Integer Multiplication**
>
> **Input:** Two n-digit nonnegative integers, x and y.
>
> **Output:** The product x · y.

1.2.3 The Grade-School Algorithm

Having defined the computational problem precisely, we describe an algorithm that solves it—the same algorithm you learned in third grade. We will assess the performance of this algorithm through the number of “primitive operations” it performs, as a function of the number of digits n in each input number. For now, let’s think of a primitive operation as any of the following: (i) adding two single-digit numbers; (ii) multiplying two single-digit numbers; or (iii) adding a zero to the beginning or end of a number.

To jog your memory, consider the concrete example of multiplying x = 5678 and y = 1234 (so n = 4). See also Figure 1.1. The algorithm first computes the “partial product” of the first number and the last digit of the second number 5678 · 4 = 22712. Computing this partial product boils down to multiplying each of the digits of the first number by 4, and adding in “carries” as necessary. When computing the next partial product (5678 · 3 = 17034), we do the same thing, shifting the result one digit to the left, effectively adding a “0” at the end. And so on for the final two partial products. The final step is to add up all the partial products.

```
      5678
    × 1234
  --------
     22712
    17034
   11356
   5678
  --------
  7006652
```

*Figure 1.1: The grade-school integer multiplication algorithm.*

---

<sup>2</sup>If you want to multiply numbers with different lengths (like 1234 and 56), a simple hack is to just add some zeros to the beginning of the smaller number (for example, treat 56 as 0056). Alternatively, the algorithms we’ll discuss can be modified to accommodate numbers with different lengths.

<sup>3</sup>8 · 4 = 32, carry the 3, 7 · 4 = 28, plus 3 is 31, carry the 3, ...

---
---

## 1.3 Karatsuba Multiplication

Back in third grade, you probably accepted that this algorithm is *correct*, meaning that no matter what numbers *x* and *y* you start with, provided that all intermediate computations are done properly, it eventually terminates with the product *x · y* of the two input numbers. That is, you’re never going to get a wrong answer, and the algorithm can’t loop forever.

### 1.2.4 Analysis of the Number of Operations

Your third-grade teacher might not have discussed the number of primitive operations needed to carry out this procedure to its conclusion. To compute the first partial product, we multiplied 4 times each of the digits 5, 6, 7, 8 of the first number. This is four primitive operations. Each carry might force us to add a single-digit number to a double-digit one, for another two primitive operations. In general, computing a partial product involves *n* multiplications (one per digit) and at most 2*n* additions (at most two per carry), for a total of at most 3*n* primitive operations. There’s nothing special about the first partial product: every partial product requires at most 3*n* operations. Because there are *n* partial products—one per digit of the second number—computing all of them requires at most *n · 3n = 3n²* primitive operations. We still have to add them all up to compute the final answer, but this takes a comparable number of operations (at most another 3*n²*, as you should check). Summarizing:

\[ \text{total number of operations} \leq \frac{\text{constant}}{6} \cdot n^2. \]

Thinking about how the amount of work the algorithm performs *scales* as the numbers grow bigger and bigger, we see that the work required grows quadratically in the number of digits. If you double the length of the input numbers, the work required jumps by a factor of 4. Quadruple their length and it jumps by a factor of 16, and so on.

### 1.2.5 Can We Do Better?

Depending on what type of third-grader you were, you might well have accepted this procedure as the unique or at least optimal way to multiply two numbers. If you want to be a serious algorithm designer, you’ll need to grow out of that kind of obedient timidity. The classic algorithms book by Aho, Hopcroft, and Ullman, after iterating through a number of algorithm design paradigms, has this to say:

> “Perhaps the most important principle for the good algorithm designer is to refuse to be content.”<sup>4</sup>

Or as I like to put it, every algorithm designer should adopt the mantra:

> *Can we do better?*

This question is particularly apropos when you’re faced with a naive or straightforward solution to a computational problem. In the third grade, you might not have asked if one could do better than the straightforward integer multiplication algorithm. Now is the time to ask, and answer, this question.

<sup>4</sup> Alfred V. Aho, John E. Hopcroft, and Jeffrey D. Ullman, *The Design and Analysis of Computer Algorithms*, Addison-Wesley, 1974, page 70.

---
---

1.3 Karatsuba Multiplication

The algorithm design space is surprisingly rich, and there are certainly other interesting methods of multiplying two integers beyond what you learned in the third grade. This section describes a method called *Karatsuba multiplication.*[^5]

#### 1.3.1 A Concrete Example

To get a feel for Karatsuba multiplication, let's re-use our previous example with \( x = 5678 \) and \( y = 1234 \). We're going to execute a sequence of steps, quite different from the grade-school algorithm, culminating in the product \( x \cdot y \). The sequence of steps should strike you as very mysterious, like pulling a rabbit out of a hat; later in the section we'll explain exactly what Karatsuba multiplication is and why it works. The key point to appreciate now is that there's a dazzling array of options for solving computational problems like integer multiplication.

First, to regard the first and second halves of \( x \) as numbers in their own right, we give them the names \( a \) and \( b \) (so \( a = 56 \) and \( b = 78 \)). Similarly, \( c \) and \( d \) denote 12 and 34, respectively (Figure 1.2).

![Figure 1.2: Thinking of four-digit numbers as pairs of two-digit numbers.](image)

Next we'll perform a sequence of operations that involve only the double-digit numbers \( a, b, c, \) and \( d \), and finally collect all the terms together in a magical way that results in the product of \( x \) and \( y \).

**Step 1:** Compute \( a \cdot c = 56 \cdot 12 \), which is 672 (as you're welcome to check).

**Step 2:** Compute \( b \cdot d = 78 \cdot 34 = 2652 \).

The next two steps are still more inscrutable.

**Step 3:** Compute \( (a + b) \cdot (c + d) = 134 \cdot 46 = 6164 \).

**Step 4:** Subtract the results of the first two steps from the result of the third step:  
6164 - 672 - 2652 = 2840.

Finally, we add up the results of steps 1, 2, and 4, but only after adding four trailing zeroes to the answer in step 1 and two trailing zeroes to the answer in step 4.

[^5]: Discovered in 1960 by Anatoly Karatsuba, who at the time was a 23-year-old student.

---
---

1.3 Karatsuba Multiplication

---

**Step 5:** Compute \(10^4 \cdot 672 + 10^2 \cdot 2840 + 2652 = 6720000 + 284000 + 2652 = 7006652\).

This is exactly the same (correct) result computed by the grade-school algorithm in Section 1.2!

You should not have any intuition about what just happened. Rather, I hope that you feel some mixture of bafflement and intrigue, and appreciate the fact that there seem to be fundamentally different algorithms for multiplying integers than the one you learned as a kid. Once you realize how rich the space of algorithms is, you have to wonder: can we do better than the third-grade algorithm? Does the algorithm above already do better?

### 1.3.2 A Recursive Algorithm

Before tackling full-blown Karatsuba multiplication, let’s explore a simpler recursive approach to integer multiplication. A recursive algorithm for integer multiplication presumably involves multiplications of numbers with fewer digits (like 12, 34, 56, and 78 in the computation above).

In general, a number \(x\) with an even number \(n\) of digits can be expressed in terms of two \(n/2\)-digit numbers, its first half \(a\) and second half \(b\):

\[ x = 10^{n/2} \cdot a + b. \]

Similarly, we can write

\[ y = 10^{n/2} \cdot c + d. \]

To compute the product of \(x\) and \(y\), let’s use the two expressions above and multiply out:

\[
x \cdot y = (10^{n/2} \cdot a + b) \cdot (10^{n/2} \cdot c + d) \\
= 10^n \cdot (a \cdot c) + 10^{n/2} \cdot (a \cdot d + b \cdot c) + b \cdot d. \tag{1.1}
\]

Note that all of the multiplications in (1.1) are either between pairs of \(n/2\)-digit numbers or involve a power of 10.

The expression (1.1) suggests a recursive approach to multiplying two numbers. To compute the product \(x \cdot y\), we compute the expression (1.1). The four relevant products (\(a \cdot c\), \(a \cdot d\), \(b \cdot c\), and \(b \cdot d\)) all concern numbers with fewer than \(n\) digits, so we can compute each of them recursively. Once our four recursive calls come back to us with their answers, we can compute the expression (1.1) in the obvious way: tack on \(n\) trailing zeroes to \(a \cdot c\), add \(a \cdot d\) and \(b \cdot c\) (using grade-school addition) and tack on \(n/2\) trailing zeroes to the result, and finally add these two expressions to \(b \cdot d\). We summarize this algorithm, which we’ll call `RecIntMult`, in the following pseudocode.

---

*I'm assuming you’ve heard of recursion as part of your programming background. A recursive procedure is one that invokes itself as a subroutine with a smaller input, until a base case is reached.*

*For simplicity, we are assuming that \(n\) is a power of 2. A simple hack for enforcing this assumption is to add an appropriate number of leading zeroes to \(x\) and \(y\), which at most doubles their lengths. Alternatively, when \(n\) is odd, it’s also fine to break \(x\) and \(y\) into two numbers with almost equal lengths.*

*Recursive algorithms also need one or more base cases, so that they don’t keep calling themselves for the rest of time. Here, the base case is: if \(x\) and \(y\) are 1-digit numbers, multiply them in one primitive operation and return the result.*

*In pseudocode, we use “:=” to denote an equality test, and “:=” to denote a variable assignment.*

---
---

RecIntMult

**Input:** two n-digit positive integers x and y.  
**Output:** the product x · y.  
**Assumption:** n is a power of 2.

```
if n = 1 then
    compute x · y in one step and return the result // base case
else
    a, b := first and second halves of x // recursive case
    c, d := first and second halves of y
    recursively compute ac := a · c, ad := a · d, bc := b · c, and bd := b · d
    compute 10^n · ac + 10^(n/2) · (ad + bc) + bd using grade-school
    addition and return the result
```

Is the RecIntMult algorithm faster or slower than the grade-school algorithm? You shouldn’t necessarily have any intuition about this question, and the answer will have to wait until Chapter 4.

### 1.3.3 Karatsuba Multiplication

Karatsuba multiplication is an optimized version of the RecIntMult algorithm. We again start from the expansion (1.1) of x · y in terms of a, b, c, and d. The RecIntMult algorithm uses four recursive calls, one for each of the products in (1.1) between n/2-digit numbers. But we don’t care about a · d or b · c, except inasmuch as we care about their sum a · d + b · c. With only three quantities that we care about— a · c, a · d + b · c, and b · d—can we get away with only three recursive calls? To see that we can, first use two recursive calls to compute a · c and b · d, as before.

**Step 1:** Recursively compute a · c.

**Step 2:** Recursively compute b · d.

Instead of recursively computing a · d or b · c, we recursively compute the product of a + b and c + d.¹⁰

**Step 3:** Compute a + b and c + d using grade-school addition, and recursively compute (a + b) · (c + d).

The key trick in Karatsuba multiplication goes back to the early 19th-century mathematician Carl Friedrich Gauss, who was thinking about multiplying complex numbers. Subtracting the results of the first two steps from the result of the third step gives exactly what we want, the middle coefficient in (1.1) of a · d + b · c:

\[
(a + b) \cdot (c + d) - a \cdot c - b \cdot d = a \cdot d + b \cdot c.
\]

¹⁰The numbers a + b and c + d might have as many as (n/2) + 1 digits, but the algorithm still works fine.

---
---

## 1.3 Karatsuba Multiplication

**Step 4:** Subtract the results of the first two steps from the result of the third step to obtain  
\( a \cdot d + b \cdot c \).

The final step computes (1.1), as in the `RecIntMult` algorithm.

**Step 5:** Compute (1.1) by adding up the results of steps 1, 2, and 4, after adding \( n \) trailing zeroes to the answer in step 1 and \( n/2 \) trailing zeroes to the answer in step 4.

---

### Karatsuba

**Input:** two \( n \)-digit positive integers \( x \) and \( y \).  
**Output:** the product \( x \cdot y \).  
**Assumption:** \( n \) is a power of 2.

```
if n = 1 then
    compute x · y in one step and return the result  // base case
else                                                // recursive case
    a, b := first and second halves of x
    c, d := first and second halves of y
    compute p := a + b and q := c + d using grade-school addition
    recursively compute ac := a · c, bd := b · d, and pq := p · q
    compute adbc := pq − ac − bd using grade-school addition
    compute 10^n · ac + 10^n/2 · adbc + bd using grade-school
    addition and return the result
```

Thus Karatsuba multiplication makes only three recursive calls! Saving a recursive call should save on the overall running time, but by how much? Is the `Karatsuba` algorithm faster than the grade-school multiplication algorithm? The answer is far from obvious, but it is an easy application of the tools you’ll acquire in Chapter 4 for analyzing the running time of such “divide-and-conquer” algorithms.

---

### On Pseudocode

This book explains algorithms using a mixture of high-level pseudocode and English (as in this section). I’m assuming that you have the skills to translate such high-level descriptions into working code in your favorite programming language. Several other books and resources on the Web offer concrete implementations of various algorithms in specific programming languages.

The first benefit of emphasizing high-level descriptions over language-specific implementations is flexibility: while I assume familiarity with *some* programming language, I don’t care which one. Second, this approach promotes the understanding of algorithms at a deep and conceptual level, unencumbered by low-level details. Seasoned programmers and computer scientists generally think and communicate about algorithms at a similarly high level.

---
---

1.4 MergeSort: The Algorithm

This section and the next provide our first taste of analyzing the running time of a non-trivial algorithm—the famous `MergeSort` algorithm.

### 1.4.1 Motivation

`MergeSort` is a relatively ancient algorithm, and was certainly known to John von Neumann as early as 1945. Why begin a modern course on algorithms with such an old example?

**Oldie but a goodie.** Despite being over 70 years old, `MergeSort` is still one of the methods of choice for sorting. It’s used all the time in practice, and is the default sorting algorithm in a number of programming libraries.

**Canonical divide-and-conquer algorithm.** The “divide-and-conquer” algorithm design paradigm is a general approach to solving problems, with applications in many different domains. The basic idea is to break your problem into smaller subproblems, solve the subproblems recursively, and finally combine the solutions to the subproblems into one for the original problem. `MergeSort` is an ideal introduction to the divide-and-conquer paradigm, the benefits it offers, and the analysis challenges it presents.

**Calibrate your preparation.** Our `MergeSort` discussion will give you a good indication of whether your current skill set is a good match for this book. My assumption is that you have the programming and mathematical backgrounds to (with some work) translate the high-level idea of `MergeSort` into a working program in your favorite programming language and to follow our running time analysis of the algorithm. If this and the next section make sense, you’re in good shape for the rest of the book.

**Motivates guiding principles for algorithm analysis.** Our running time analysis of `MergeSort` exposes a number of more general guiding principles, such as the quest for running time bounds that hold for every input of a given size, and the importance of the rate of growth of an algorithm’s running time (as a function of the input size).

**Warm-up for the master method.** We’ll analyze `MergeSort` using the “recursion tree method,” which is a way of tallying up the operations performed by a recursive algorithm. Chapter 4 builds on these ideas and culminates with the “master method,” a powerful and easy-to-use tool for bounding the running time of many different divide-and-conquer algorithms, including the `RecIntMult` and `Karatsuba` algorithms of Section 1.3.

---
---

1.4 **MergeSort: The Algorithm**

---

### 1.4.2 Sorting

Look around and you’ll notice that computers seem pretty good at sorting things instantaneously. Think of the list of contacts on your phone. Are they listed in the order that you entered them? Of course not—that would be annoying, so they’re instead sorted alphabetically for easy reference. Meanwhile, your favorite spreadsheet program has no trouble sorting a large file according to whatever criterion you want. How do they do it?

Here’s a canonical version of the sorting problem:

---

**Problem: Sorting**

- **Input:** An array of *n* numbers, in arbitrary order.

- **Output:** An array of the same numbers, sorted from smallest to largest.

---

For example, given the input array

```
5 4 1 8 7 2 6 3
```

the desired output array is

```
1 2 3 4 5 6 7 8
```

In the example above, the eight numbers in the input array are distinct. Sorting isn’t really any harder when there are duplicates, and it can even be easier. But to keep the discussion as simple as possible, let’s assume—among friends—that the numbers in the input array are always distinct. I strongly encourage you to think about how sorting algorithms need to be modified (if at all) to handle duplicates.¹¹

If you don’t care about optimizing the running time, it’s not too difficult to come up with a correct sorting algorithm. Perhaps the simplest approach is to first scan through the input array to identify the minimum element and copy it over to the first element of the output array; then do another scan to identify and copy over the second-smallest element; and so on. This algorithm is called **SelectionSort**. You may have heard of **InsertionSort**, which can be viewed as a slicker implementation of the same idea of iteratively growing a prefix of the sorted output array. You might also know **BubbleSort**, in which you identify adjacent pairs of elements that are out of order, and perform repeated swaps until the entire array is sorted. All of these algorithms have quadratic running times, meaning that—analogous to the grade-school multiplication algorithm in Section 1.2.3—the number of operations performed on arrays of length *n* scales with *n*², the square of the input length.

---

¹¹In practice, there is often data (called the *value*) associated with each number (which is called the *key*). For example, you might want to sort employee records (with the name, salary, etc.) using social security numbers as keys. We focus on sorting the keys, with the understanding that each key retains its associated data.

---
---

Can we do better? By using the divide-and-conquer algorithm design paradigm, the `MergeSort` algorithm improves dramatically over these more straightforward sorting algorithms.^12

### 1.4.3 An Example

The easiest way to understand `MergeSort` is through a picture of a concrete example (Figure 1.3). We’ll use the input array from Section 1.4.2.

```
        5 4 1 8 7 2 6 3
               divide
        -----------------
       |                 |
    5 4 1 8           7 2 6 3
       |                 |
       |                 |
  recursive calls   recursive calls
       |                 |
    1 4 5 8           2 3 6 7
       |                 |
        -----------------
               merge
        1 2 3 4 5 6 7 8
```

*Figure 1.3: A bird’s-eye view of `MergeSort` on a concrete example.*

As a recursive divide-and-conquer algorithm, `MergeSort` calls itself on smaller arrays. The simplest way to decompose a sorting problem into smaller sorting problems is to break the input array in half. The first and second halves are each sorted recursively. For example, in Figure 1.3, the first and second halves of the input array are `[5, 4, 1, 8]` and `[7, 2, 6, 3]`. By the magic of recursion (or induction, if you prefer), the first recursive call correctly sorts the first half, returning the array `[1, 4, 5, 8]`. The second recursive call returns the array `[2, 3, 6, 7]`. The final “merge” step combines these two sorted arrays of length 4 into a single sorted array of all 8 numbers. Details of this step are given below; the basic idea is to walk indices down each of the sorted subarrays, populating the output array from left to right in sorted order.

### 1.4.4 Pseudocode

The picture in Figure 1.3 suggests the following pseudocode, with two recursive calls and a merge step, for the general problem. As usual, our description cannot necessarily be translated line by line into working code (though it’s pretty close).

---

^12 While generally dominated by `MergeSort`, `InsertionSort` is still useful in practice in certain cases, especially for small input sizes.

---
---

## 1.4 MergeSort: The Algorithm

```
MergeSort

Input: array A of n distinct integers.
Output: array with the same integers, sorted from smallest to largest.

// ignoring base cases
C := recursively sort first half of A
D := recursively sort second half of A
return Merge(C, D)
```

There are several deliberate omissions from the pseudocode that deserve comment. As a recursive algorithm, there should also be one or more base cases, in which there is no further recursion and the answer is returned directly. So if the input array A contains only 0 or 1 elements, MergeSort returns it (it is already sorted). The pseudocode does not detail what "first half" and "second half" mean when n is odd, but the obvious interpretation (with one "half" having one more element than the other) works fine. Finally, the pseudocode ignores the implementation details of how to actually pass the two subarrays to their respective recursive calls. These details depend somewhat on the programming language. The point of high-level pseudocode is to ignore such details and focus on the concepts that transcend any particular programming language.

### 1.4.5 The Merge Subroutine

How should we implement the Merge step? At this point, the two recursive calls have done their work and we have in our possession two sorted subarrays C and D (of length n/2). The idea is to traverse both the sorted subarrays in order and populate the output array from left to right in sorted order.¹³

```
Merge

Input: sorted arrays C and D (length n/2 each).
Output: sorted array B (length n).
Simplifying assumption: n is even.

1 i := 1
2 j := 1
3 for k := 1 to n do
4     if C[i] < D[j] then
5         B[k] := C[i]        // populate output array
6         i := i + 1          // increment i
7     else
8         B[k] := D[j]        // D[j] < C[i]
9         j := j + 1
```

¹³Unless otherwise noted, we number our array entries beginning with 1 (rather than 0), and use the syntax "A[i]" for the ith entry of an array A. These details vary across programming languages.

---
---

We traverse the output array using the index k, and the sorted subarrays with the indices i and j. All three arrays are traversed from left to right. The for loop in line 3 implements the pass over the output array. In the first iteration, the subroutine identifies the minimum element in either C or D and copies it over to the first position of the output array B. The minimum element overall is either in C (in which case it’s C[1], because C is sorted) or in D (in which case it’s D[1], because D is sorted). Advancing the appropriate index (i or j) effectively removes from further consideration the element just copied, and the process is then repeated to identify the smallest element remaining in C or D (the second-smallest overall). In general, the smallest element not yet copied over to B is either C[i] or D[j]; the subroutine explicitly checks to see which one is smaller and proceeds accordingly. Because every iteration copies over the smallest element still under consideration in C or D, the output array is indeed populated in sorted order.

As usual, our pseudocode is intentionally a bit sloppy, to emphasize the forest over the trees. A full implementation should also keep track of when the traversal of C or D falls off the end of the array, at which point the remaining elements of the other array are copied into the final entries of B (in order). Now is a good time to work through your own implementation of the MergeSort algorithm.

### 1.5 MergeSort: The Analysis

What’s the running time of the MergeSort algorithm, as a function of the length n of the input array? Is it faster than more straightforward methods of sorting, such as InsertionSort, InsertionSort, and BubbleSort? By “running time,” we mean the number of lines of code executed in a concrete implementation of the algorithm. Think of walking line by line through this implementation using a debugger, one “primitive operation” at a time. We’re interested in the number of steps the debugger takes before the program completes.

#### 1.5.1 Running Time of Merge

Analyzing the running time of the MergeSort algorithm is an intimidating task, as it’s a recursive algorithm that calls itself over and over. So let’s warm up with the simpler task of understanding the number of operations performed by a single invocation of the Merge subroutine when called on two sorted arrays of length ℓ/2 each. We can do this directly, by inspecting the code in Section 1.4.5 (where n corresponds to ℓ). First, lines 1 and 2 each perform an initialization, and we’ll count this as two operations. Then, we have a for loop that executes a total of ℓ times. Each iteration of the loop performs a comparison in line 4, an assignment in either line 5 or line 8, and an increment in either line 6 or line 9. The loop index k also needs to get incremented each loop iteration. This means that 4 primitive operations are performed for each of the ℓ iterations of the loop.¹⁴ Totaling up, we conclude that the Merge subroutine performs at most 4ℓ + 2 operations to merge two sorted arrays of length ℓ/2 each. Let me abuse our friendship further with a true but sloppy inequality that will make our lives easier: for every positive integer ℓ ≥ 1, we have 4ℓ + 2 ≤ 4ℓ + 2ℓ = 6ℓ.

¹⁴One could quibble with the choice of 4. Does comparing the loop index k to its upper bound also count as an additional operation each iteration, for a total of 5? Section 1.6 explains why such differences in accounting don’t really matter. So let’s agree, among friends, that it’s 4 primitive operations per iteration.

---
---

1.5 **MergeSort: The Analysis**

That is, 6ℓ is also a valid upper bound on the number of operations performed by the *Merge* subroutine.

**Lemma 1.1 (Running Time of Merge)** *For every pair of sorted input arrays C, D of length ℓ/2, the* Merge *subroutine performs at most 6ℓ operations.*

> **On Lemmas, Theorems, and the Like**
>
> In mathematical writing, the most important technical statements are labeled *theorems*. A *lemma* is a technical statement that assists with the proof of a theorem (much as *Merge* assists with the implementation of *MergeSort*). A *corollary* is a statement that follows immediately from an already-proven result, such as a special case of a theorem. We use the term *proposition* for stand-alone technical statements that are not particularly important in their own right.

### 1.5.2 Running Time of MergeSort

How can we go from the straightforward analysis of the *Merge* subroutine to an analysis of *MergeSort*, a recursive algorithm that spawns further invocations of itself? Especially terrifying is the rapid proliferation of recursive calls, the number of which is blowing up exponentially with the depth of the recursion. The one thing we have going for us is that the input size ℓ is passed as an input subproblem to each recursive call of *MergeSort*. There’s a tension between two competing forces: on the one hand, the explosion of different subproblems that need to be solved; and on the other, the ever-shrinking inputs for which these subproblems are responsible. Reconciling these two forces will drive our analysis of *MergeSort*. In the end, we’ll prove the following concrete and useful upper bound on the number of operations performed by *MergeSort* (across all its recursive calls).

**Theorem 1.2 (Running Time of MergeSort)** *For every input array of length n ≥ 1, the* MergeSort *algorithm performs at most*

6n log₂ n + 6n

*operations, where log₂ denotes the base-2 logarithm.*

> **On Logarithms**
>
> Some people are unnecessarily frightened by the appearance of a logarithm, which is actually a very down-to-earth concept. For a positive integer n, log₂ n just means the following: type n into a calculator, and count the number of times you need to divide it by 2 before the result is 1 or less. For example, it takes five divide-by-twos to bring 32 down to 1, so log₂ 32 = 5. Ten divide-by-twos bring 1024 down to 1, so log₂ 1024 = 10. These examples make it intuitively clear that log₂ n is much less than n (compare 10 vs. 1024), especially as n grows large. A plot confirms this intuition.

---
---

16

(Figure 1.4).

“To be pedantic, log₂ n is not an integer if n is not a power of 2, and what we have described is really log₂ n rounded up to the nearest integer. We can ignore this minor distinction.

![Graph](image.png)

Figure 1.4: The base-2 logarithm function f(n) = log₂ n grows much more slowly than the linear function f(n) = n. Other logarithm bases lead to qualitatively similar pictures.

Theorem 1.2 is a win for the **MergeSort** algorithm and showcases the benefits of the divide-and-conquer algorithm design paradigm. We mentioned that the running times of simpler sorting algorithms, like **SelectionSort**, **InsertionSort**, and **BubbleSort**, depend quadratically on the input size n, meaning that the number of operations required scales as a constant times n². In Theorem 1.2, one of these factors of n is replaced by log₂ n. As suggested by Figure 1.4, this means that **MergeSort** typically runs much faster than the simpler sorting algorithms, especially as n grows large.¹⁵

### 1.5.3 Proof of Theorem 1.2

We now do a full running time analysis of **MergeSort**, thereby substantiating the claim that a recursive divide-and-conquer approach results in a faster sorting algorithm than more straightforward methods. For simplicity, we assume that the input array length n is a power of 2. This assumption can be removed with minor additional work.

The plan for proving the running time bound in Theorem 1.2 is to use a recursion tree; see Figure 1.5.¹⁶ The idea of the recursion tree method is to write out all the work done by a

¹⁵See Section 1.6.3 for further discussion of this point.
¹⁶For some reason, computer scientists seem to think that trees grow downward.

---
---

1.5 **MergeSort: The Analysis**

recursive algorithm in a tree structure, with nodes of the tree corresponding to recursive calls, and the children of a node corresponding to the recursive calls made by that node. This tree structure provides us with a principled way to tally up all the work done by *MergeSort* across all its recursive calls.

```
          entire input
             _______
            |       |
            |       |
            |_______|
               / \
              /   \
             /     \
            /       \
           /         \
          /           \
         /             \
        /               \
       /                 \
      /                   \
     /                     \
    /                       \
   /                         \
  /                           \
 /                             \
/                               \
left half                     right half
```

*level 0*  
*(outermost call)*

*level 1*  
*(first recursive calls)*

*level 2*

```
leaves (single-element arrays)
```

**Figure 1.5:** A recursion tree for *MergeSort*. Nodes correspond to recursive calls: Level 0 corresponds to the outermost call to *MergeSort*, level 1 to its recursive calls, and so on.

The root of the recursion tree corresponds to the outermost call to *MergeSort*, where the input is the original input array. We’ll call this level 0 of the tree. Because each invocation of *MergeSort* spawns two recursive calls, the tree is binary (that is, with two children per node). Level 1 of the tree has two nodes, corresponding to the two recursive calls made by the outermost call, one for the left half of the input array and one for the right half. Each of the level-1 recursive calls itself makes two recursive calls, each operating on a distinct quarter of the original input array. This process continues until eventually the recursion bottoms out with arrays of length 1 (the base cases).

---

**Quiz 1.1**

Roughly how many levels does this recursion tree have, as a function of the length *n* of the input array?

a) A constant number (independent of *n*)

b) \(\log_2 n\)

c) \(\sqrt{n}\)

---
---

18

d) \( n \)

(See Section 1.5.4 for the solution and discussion.)

This recursion tree suggests a particularly convenient way to account for the work done by `MergeSort`, which is level by level. To implement this idea, we need to understand two things: the number of distinct subproblems at a given recursion level \( j \), and the length of the input to each of these subproblems.

---

**Quiz 1.2**

What is the pattern? Fill in the blanks in the following statement: at each level \( j = 0, 1, 2, \ldots \) of the recursion tree, there are [blank] subproblems, each operating on a subarray of length [blank].

a) \( 2^j \) and \( 2^j \), respectively

b) \( n/2^j \) and \( n/2^j \), respectively

c) \( 2^j \) and \( n/2^j \), respectively

d) \( n/2^j \) and \( 2^j \), respectively

(See Section 1.5.4 for the solution and discussion.)

---

Let's now put this pattern to use and tally all the operations that `MergeSort` performs. We proceed level by level, so fix a level \( j \) of the recursion tree. How much work is done by the level-\( j \) recursive calls, not counting the work done by their recursive calls at deeper levels? Inspecting the `MergeSort` code, we see that it does only three things: make two recursive calls and invoke the `Merge` subroutine on the results. Thus, ignoring the work done by its recursive calls, the work done in a level-\( j \) subproblem is just the work performed by `Merge`. This we already understand from Lemma 1.1: at most \( 6\ell \) operations, where \( \ell \) is the length of the input array in this subproblem.

To put everything together, we can express the total work done by level-\( j \) recursive calls (not counting later recursive calls) as

\[
\frac{\# \text{ of level-} j \text{ subproblems} \times \text{ work per level-} j \text{ subproblem}}{=2^j} = \frac{6n/2^j}{=6n}
\]

Using the solution to Quiz 1.2, we know that the first term equals \( 2^j \), and the input length in each such subproblem is \( n/2^j \). Taking \( \ell = n/2^j \), Lemma 1.1 implies that each level-\( j \) subproblem performs at most \( 6n/2^j \) operations. We conclude that at most

\[
2^j \cdot \frac{6n}{2^j} = 6n
\]

operations are performed across all the recursive calls at the \( j \)th recursion level.

Remarkably, our bound on the work done at a given level \( j \) is independent of \( j \)! That is, each level of the recursion tree contributes the same number of operations to the analysis. The reason for this is a perfect equilibrium between two competing forces—the number

---
---

## 1.5 MergeSort: The Analysis

of subproblems doubles every level, while the amount of work performed per subproblem halves every level.

We're interested in the number of operations performed across all levels of the recursion tree. By the solution to Quiz 1.1, the recursion tree has log₂ n + 1 levels (levels 0 through log₂ n, inclusive). Using our bound of 6n operations per level, we can bound the total number of operations by

\[
\text{number of levels} \times \text{work per level} \leq 6n \log_2 n + 6n,
\]

matching the bound claimed in Theorem 1.2. 2Θg^17

### On Primitive Operations

We measure the running time of an algorithm like MergeSort in terms of the number of "primitive operations" performed. Intuitively, a primitive operation performs a simple task (like adding, comparing, or copying) while touching a small number of simple variables (like 32-bit integers).[^18] Warning: in some high-level programming languages, a single line of code can mask a large number of primitive operations. For example, a line of code that touches every element of a long array translates to a number of primitive operations proportional to the array's length.

### 1.5.4 Solutions to Quizzes 1.1–1.2

#### Solution to Quiz 1.1

**Correct answer: (b).** The correct answer is ≈ log₂ n. The reason is that the input size decreases by a factor of two with each level of the recursion. If the input length is level 0 is n, the level-1 recursive calls operate on arrays of length n/2, the level-2 recursive calls on arrays of length n/4, and so on. The recursion bottoms out at the base cases, with input arrays of length at most one, where there are no more recursive calls. How many levels of recursion are required? The number of times you need to divide n by 2 before obtaining a number that is at most 1. For n a power of 2, this is precisely the definition of log₂ n. (More generally, it is log₂ n rounded up to the nearest integer.)

#### Solution to Quiz 1.2

**Correct answer: (c).** The correct answer is that there are 2ʲ distinct subproblems at recursion level j, and each operates on a subarray of length n/2ʲ. For the first point, start with level 0, where there is one recursive call. There are two recursive calls at level 1, and more generally, because MergeSort calls itself twice, the number of recursive calls at each level is double the number at the previous level. This recursive doubling implies

[^17]: "Q.e.d." is an abbreviation for quod erat demonstrandum and means "that which was to be demonstrated." In mathematical writing, it is used at the end of a proof to mark its completion.
[^18]: More precise definitions are possible, but we won't need them.

---
---

20

Introduction

that there are 2^j subproblems at each level j of the recursion tree. Similarly, because every recursive call gets only half the input of the previous one, after j levels of recursion the input length has dropped to n/2^j. Or for a different argument, we already know that there are 2^j subproblems at level j, and the original input array (of length n) is equally partitioned among these—exactly n/2^j elements per subproblem.

### 1.6 Guiding Principles for the Analysis of Algorithms

With our first real algorithm analysis under our belt (MergeSort, in Theorem 1.2), it’s the right time to take a step back and make explicit three assumptions that informed our running time analysis and interpretation of it. We will adopt these three assumptions as guiding principles for how to reason about algorithms, and use them to define what we actually mean by a “fast algorithm.”

The goal of these principles is to identify a sweet spot for the analysis of algorithms, one that balances accuracy with tractability. Exact running time analysis is possible only for the simplest algorithms; more generally, compromises are required. On the other hand, we don’t want to throw out the baby with the bathwater—we still want our mathematical analysis to have predictive power about whether an algorithm will be fast or slow in practice. Once we find the right balance, we’ll be able to prove good running time guarantees for dozens of fundamental algorithms, and these guarantees will paint an accurate picture of which algorithms tend to run faster than others.

#### 1.6.1 Principle #1: Worst-Case Analysis

Our running time bound of 6n log_2 n + 6n in Theorem 1.2 holds for every input array of length n, no matter what its contents. We made no assumptions about the input beyond its length n. Hypothetically, if there was an adversary whose sole purpose in life was to concoct a malevolent input designed to make MergeSort run as slow as possible, the 6n log_2 n + 6n bound would still apply. This type of analysis is called worst-case analysis, as it gives a running time bound that is valid even for the “worst” inputs.

Given how naturally worst-case analysis fell out of our analysis of MergeSort, you might well wonder what else we could do. One alternative approach is “average-case analysis,” which analyzes the average running time of an algorithm under some assumption about the relative frequencies of different inputs. For example, in the sorting problem, we could assume that all input arrays are equally likely and then study the average running time of different sorting algorithms. A second alternative is to look only at the performance of an algorithm on a small collection of “benchmark instances” that are thought to be representative of “typical” or “real-world” inputs.

Both average-case analysis and the analysis of benchmark instances can be useful when you have domain knowledge about your problem, and some understanding of which inputs are more representative than others. Worst-case analysis, in which you make absolutely no assumptions about the input, is particularly appropriate for general-purpose subroutines designed to work well across a range of application domains. To be useful to as many people as possible, this book focuses on such general-purpose subroutines and, accordingly, uses worst-case analysis to judge algorithm performance.

---
---

1.6 Guiding Principles for the Analysis of Algorithms

As a bonus, worst-case analysis is usually much more tractable mathematically than its alternatives. This is one reason why worst-case analysis naturally popped out of our `MergeSort` analysis, even though we had no a priori focus on worst-case inputs.

### 1.6.2 Principle #2: Big-Picture Analysis

The second and third guiding principles are closely related. Let’s call the second one **big-picture analysis** (warning: this is not a standard term). This principle states that we should not worry unduly about small constant factors or lower-order terms in running time bounds. We’ve already seen this philosophy at work in our analysis of `MergeSort`: when analyzing the running time of the Merge subroutine (Lemma 1.1), we first proved an upper bound of `4ℓ + 2` on the number of operations (where `ℓ` is the length of the output array) and then settled for the simpler upper bound of `6ℓ`, even though it suffers from a larger constant factor. How do we justify being so fast and loose with constant factors?

**Mathematical tractability.** The first reason for big-picture analysis is that it’s way easier mathematically than the alternative of pinning down precise constant factors or lower-order terms. This point was already evident in our running time analysis of `MergeSort`.

**Constants depend on environment-specific factors.** The second justification is less obvious but extremely important. At the level of granularity we’ll use to describe algorithms, as with the `MergeSort` algorithm, it would be foolish to get too obsessed over exactly what the constant factors are. For example, during our analysis of the `Merge` subroutine, there was ambiguity about exactly how many “primitive operations” are performed each loop iteration (4, 5, or something else?). Thus, different interpretations of the same pseudocode can lead to different constant factors. The ambiguity only increases once pseudocode gets translated into a concrete implementation in some high-level programming language, and then translated further into machine code—the constant factors will inevitably depend on the programming language used, the specific implementation, and the details of the compiler and processor. Our goal is to focus on properties of algorithms that transcend the details of the programming language and machine architecture, and these properties should be independent of small constant-factor changes in a running time bound.

**Lose little predictive power.** The third justification is simply that we’re going to be able to get away with it. You might be concerned that ignoring constant factors would lead us astray, tricking us into thinking that an algorithm is fast when it is actually slow in practice, or vice versa. Happily, this won’t happen for the algorithms discussed in this book. Even though we won’t be keeping track of lower-order terms and constant factors, the qualitative predictions of our mathematical analysis will be highly accurate—when analysis suggests that an algorithm should be fast, it will in fact be fast in practice, and conversely. So while big-picture analysis does discard some information, it preserves what we really care about: accurate guidance about which algorithms tend to be faster than others.

---

19 With one possible exception, the deterministic linear-time selection algorithm in the optional Section 6.3.

20 It’s still useful to have a general sense of the relevant constant factors, however. For example, in the highly tuned versions of `MergeSort` that you’ll find in many programming libraries, the algorithm switches from `MergeSort` over to `InsertionSort` (for its better constant factor) once the input array length becomes small (for example, at most seven elements).

---
---

1.6.3 Principle #3: Asymptotic Analysis

Our third and final guiding principle is to use asymptotic analysis and focus on the rate of growth of an algorithm’s running time, as the input size n grows large. This bias toward large inputs was already evident when we interpreted our running time bound for `MergeSort` (Theorem 1.2), of 6n log₂ n + 6n operations. We then cavalierly declared that `MergeSort` is “better than” simpler sorting methods with running time quadratic in the input size, such as `InsertionSort`. But is this really true?

For concreteness, suppose we have a sorting algorithm that performs at most ½n² operations when sorting an array of length n, and consider the comparison

6n log₂ n + 6n vs. ½n².

Looking at the behavior of these two functions in Figure 1.6, we see that ½n² is the smaller expression when n is small (at most 90 or so), while 6n log₂ n + 6n is smaller for all larger n. So when we say that `MergeSort` is faster than simpler sorting methods, what we really mean is that it is faster on sufficiently large instances.

![Graph](image)

(a) Small values of n

(b) Medium values of n

**Figure 1.6:** The function ½n² grows much more quickly than 6n log₂ n + 6n as n grows large. The scales of the x- and y-axes in (b) are one and two orders of magnitude, respectively, bigger than those in (a).

Why should we care more about large instances than small ones? Because large instances are the only ones that require algorithmic ingenuity. Almost any sorting method you can think of would sort an array of length 1000 instantaneously on a modern computer—there’s no need to learn about divide-and-conquer algorithms.

Given that computers are constantly getting faster, you might wonder if all computational problems will eventually become trivial to solve. In fact, faster computers make asymptotic analysis more relevant than ever! Our computational ambitions have always grown with our computational power, so as time goes on, we tackle larger and larger problem sizes. And the gulf in performance between algorithms with different asymptotic running times only becomes wider as inputs grow larger. For example, Figure 1.6(b) shows the difference between the functions 6n log₂ n + 6n and ½n² for larger (but still modest) values of n, and...

---
---

## 1.6 Guiding Principles for the Analysis of Algorithms

by the time *n* = 1500 there is roughly a factor-10 difference between them. If we scaled *n* up by another factor of 10, or 100, or 1000 to start reaching interesting problem sizes, the difference between the two functions would be huge.

For a different way to think about asymptotic analysis, suppose you have a fixed time budget, like an hour or a day. How does the solvable problem size scale with additional computing power? With an algorithm that runs in time proportional to the input size, a four-fold increase in computing power lets you solve problems four times as large as before. With an algorithm that runs in time proportional to the square of the input size, you would be able to solve only problems that are twice as large as before.

### 1.6.4 What Is a “Fast” Algorithm?

Our three guiding principles lead us to the following definition of a “fast algorithm”:

> A “fast algorithm” is an algorithm whose worst-case running time grows slowly with the input size.

Our first guiding principle, that we want running time guarantees that do not assume any domain knowledge, is the reason why we focus on the worst-case running time of an algorithm. Our second and third guiding principles, that constant factors are language- and machine-dependent and that large problems are the interesting ones, are the reasons why we focus on the rate of growth of the running time of an algorithm.

What do we mean that the running time of an algorithm “grows slowly”? For almost all of the problems we’ll discuss, the holy grail is a linear-time algorithm, meaning an algorithm with running time proportional to the input size. Linear time is even better than our bound on the running time of `MergeSort`, which is proportional to *n* log *n* and hence modestly super-linear. We will succeed in designing linear-time algorithms for some problems but not for others. In any case, it is the best-case scenario to which we will aspire.

---

**For-Free Primitives**

You can think of an algorithm with a linear or near-linear running time as a primitive that can be used essentially “for free”—the amount of time required barely exceeds what you need to read the input. Sorting is a canonical example of a for-free primitive, and we will also learn several others. When you have such a blazingly fast primitive that is relevant to your problem, why not use it? For example, you can always sort your data in a preprocessing step, even if you’re not quite sure how it will help later. One of the goals of this book is to stock your algorithmic toolbox with as many for-free primitives as possible, ready to be applied at will.

---

**The Upshot**

☆ An algorithm is a set of well-defined rules for solving some computational problem.

---
---

- The number of primitive operations performed by the algorithm you learned in grade school to multiply two *n*-digit integers scales as a quadratic function of the number *n*.

- Karatsuba multiplication is a recursive algorithm for integer multiplication, and it uses Gauss’s trick to save one recursive call over a more straightforward recursive algorithm.

- Seasoned programmers and computer scientists generally think and communicate about algorithms using high-level descriptions rather than detailed implementations.

- The `MergeSort` algorithm is a “divide-and-conquer” algorithm that splits the input array into two halves, recursively sorts each half, and combines the results using the `Merge` subroutine.

- Ignoring constant factors and lower-order terms, the number of operations performed by `MergeSort` to sort *n* elements grows like the function *n* log₂ *n*. The analysis uses a recursion tree to conveniently organize the work done by all the recursive calls.

- Because the function log₂ *n* grows slowly with *n*, `MergeSort` is typically faster than simpler sorting algorithms, which all require a quadratic number of operations. For large *n*, the improvement is dramatic.

- Three guiding principles for the analysis of algorithms are: (i) worst-case analysis, to promote general-purpose algorithms that work well with no assumptions about the input; (ii) big-picture analysis, which balances predictive power with mathematical tractability by ignoring constant factors and lower-order terms; and (iii) asymptotic analysis, which is a bias toward large inputs, which are the inputs that require algorithmic ingenuity.

- A “fast algorithm” is an algorithm whose worst-case running time grows slowly with the input size.

- A “for-free primitive” is an algorithm that runs in linear or near-linear time, barely more than what is required to read the input.

---

**Test Your Understanding**

**Problem 1.1 (S)** Suppose we run `MergeSort` on the following input array:

\[ 5 \quad 3 \quad 8 \quad 9 \quad 1 \quad 7 \quad 0 \quad 2 \quad 6 \quad 4 \]

---
---

Fast forward to the moment after the two outermost recursive calls complete, but before the final **Merge** step. Thinking of the two 5-element output arrays of the recursive calls as a glued-together 10-element array, which number is in the 7th position?

**Problem 1.2** *(H)* Consider the following modification to the **MergeSort** algorithm: divide the input array into thirds (rather than halves), recursively sort each third, and finally combine the results using a three-way **Merge** subroutine. What is the running time of this algorithm as a function of the length *n* of the input array, ignoring constant factors and lower-order terms?

a) *n*

b) *n* log *n*

c) *n*(log *n*)²

d) *n*² log *n*

**Problem 1.3** *(H)* Suppose you are given *k* sorted arrays, each with *n* elements, and you want to combine them into a single array of *kn* elements. One approach is to use the **Merge** subroutine from Section 1.4.5 repeatedly, first merging the first two arrays, then merging the result with the third array, then with the fourth array, and so on until you merge in the *k*th and final input array. What is the running time taken by this successive merging algorithm, as a function of *k* and *n*, ignoring constant factors and lower-order terms?

a) *n* log *k*

b) *nk*

c) *nk* log *k*

d) *nk* log *n*

e) *nk*²

f) *n*²*k*

**Problem 1.4** *(S)* Consider again the problem of merging *k* sorted length-*n* arrays into a single sorted length-*kn* array. Consider the algorithm that first divides the *k* arrays into *k*/2 pairs of arrays, and uses the **Merge** subroutine to combine each pair, resulting in *k*/2 sorted length-2*n* arrays. The algorithm repeats this step until there is only one length-*kn* sorted array. What is the running time of this procedure, as a function of *k* and *n*, ignoring constant factors and lower-order terms?

a) *n* log *k*

b) *nk*

c) *nk* log *k*

d) *nk* log *n*

---
---

e) \( nk^2 \)

f) \( n^2k \)

**Problem 1.5** (S) The following problems all concern an input array of integers, possibly with duplicate entries. Which of them can be solved using a single invocation of a sorting subroutine followed by a single pass over the sorted array? (Choose all that apply.)

a) Compute the minimum gap between any pair of array elements.

b) Compute the number of distinct integers contained in the array.

c) Compute a “de-duplicated” version of the input array, meaning an output array that contains exactly one copy of each of the distinct integers in the input array.

d) Compute the mode (the most frequently appearing integer) of the array. If there is a tie and there are two or more modes, the algorithm should return all of them.

e) For this part, assume that the array’s integers are distinct and that the array has odd length. Compute the median of the array—the “middle element,” with the number of other elements less than it equal to the number of other elements greater than it.

**Challenge Problems**

**Problem 1.6** (H) Intuitively, every algorithm that checks whether an array \( A \) of \( n \) integers contains a given integer \( t \) must examine every array element and therefore requires at least \( n \) primitive operations. Suppose now that the input includes not one but \( k \) integers \( t_1, t_2, \ldots, t_k \), and the goal is to identify which of the \( t_i \)’s appear in \( A \). Does every algorithm for this problem require at least \( kn \) operations? Does the answer depend on \( k \)?

**Problem 1.7** (H) You are given as input an unsorted array of \( n \) distinct numbers, where \( n \) is a power of 2. Give an algorithm that identifies the second-largest number in the array while using at most \( n + \log_2 n - 2 \) comparisons.

**Programming Problems**

**Problem 1.8** Implement Karatsuba’s integer multiplication algorithm in your favorite programming language. To get the most out of this problem, your program should invoke the language’s multiplication operator only on pairs of single-digit numbers.

For a concrete challenge, what’s the product of the following two 64-digit numbers?

3141592653589793238462643383279502884197169399375105820974944592

2718281828459045235360287471352662497757247093699959574966967627

[^21]: Food for thought: does it make your life easier if the number of digits of each integer is a power of 2?
[^22]: If you need help or want to compare notes with other readers, visit the discussion forums at www.algorithmsilluminated.org.

---
---

