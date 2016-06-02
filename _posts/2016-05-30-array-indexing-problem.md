---
layout: post
title:  "Array Indexing Problem"
category: fun
tags:
  - Algorithms
  - Interview Questions
language: en
---

From time to time I enjoy solving some challenges just for fun. A couple of months ago I found [this problem on Quora][95b4d15a]. What caught my attention was that it's an indexing question, and although there was clearly a pattern there all answers included a loop. I knew there was a way to do this with index mapping, so I came up with a solution worth sharing.

I challenge you to first solve the problem, then read my explained solution.


# The Problem


> I have a single dimensional array [1,2,3,4,5,6,7,8,9]. I want to convert it two dimensional array with [1,3,6] in first row, [2,5,8] in second row and [4,7,9] in third row, but the catch is we can't use nested loops. How can it be done using a single loop?

---

# The Solution

**TL; DR**

This is a O(1) space and O(1) time solution, no loops, no iterators:


``` python
def intsum(k):
  return k*(k+1)/2

def map2Dto1D(i,j,n):
  d = row+col
  if (row*self.n+col) <= len(self.items)/2:
      return self._intsum(d) + col
  # Mirrored
  n = self.n
  return n * n - 1  - self._map2Dto1D(n-row-1, n-col-1)

```
---
** Explained  version **

Instead of using loops, we will create a data structure that make it seem like it has changed? The Data Structure will wrap the array, and we will add getters and setters that will receive the row and column and operate on the 1D array. This uses **exactly 0 loops, just sweet and delicious math**.

With this, our problem is now the inverse:

> Having the 2D array [[1,3,6] , [2,5,8], [4,7,9]], how can I convert it to the 1D array [1,2,3,4,5,6,7,8,9]

We will focus first on the implementation of the mapping method `map2Dto1D`, that will take a row, a column and the size of the array and map to the 1D position:

  ``` python
  def map2Dto1D(i,j,n):
    pass
  ```

The first step is to understand the transformation:

$$
\begin{bmatrix}
1 & 3 & 6 \\
2 & 5 & 8 \\
4 & 7 & 9
\end{bmatrix} \rightarrow [1,2,3,4,5,6,7,8,9]
$$

So the expected behavior of our function is:

  ``` python
  a = [1,2,3,4,5,6,7,8,9]
  a[map2Dto1D(0,0,3)] // => a[0] (item 1)
  a[map2Dto1D(1,1,3)] // => a[4] (item 5)
  a[map2Dto1D(2,2,3)] // => a[8] (item 9)
  a[map2Dto1D(2,1,3)] // => a[6] (item 7)
  ```

Didn't stop the correlation yet? Maybe if I write it this way:

![Disposition of numbers](/img/matrix-lines.png)

Our 2D array is obtained by putting in the diagonals starting on the left side. Following the lines,
we have

$$
\begin{array}{ll}
 d_0: 1 \\
 d_1: 2 - 3 \\
 d_2: 4 -  5 - 6 \\
 d_3: 7 - 8 \\
 d_4: 9
\end{array}
$$

Note that I just put one diagonal per line. Some tips here: there are exactly 2 * n - 1 diagonals (this is true for every square matrix of dimension n, in this case 3). Also, take a look at d2:

$$ d_2: 4 (0,2) - 5 (1,1) - 6 (0,2) $$

For (i*n+j) <= n*n/2, the indexes of 2D matrix sum up to the diagonal that number is located. Also the column j indicates the position of the element on that diagonal. Eureka!

Let's solve first for (i*n+j) <= n*n/2 then.

To compute the position, we first find the diagonal the number is located (i+j). then, we count how many elements there are in the previous diagonals, and check how many elements exist in this diagonal prior to our current one.

In the upper left side of the matrix, we have this nice property that d0 has 1 element, d1 has 2, d2 has 3 and so on (for a sufficiently big n). Therefore, if we are at d3 the number of elements prior to d3 is 1+2+3 = 6, or the sum of integers from 1 to d. This can be computed using the following expression:

$$\sum_{i=1}^k i  = k*(k+1)/2$$

So, for the upper side we have that:

``` python
def map2Dto1D(i,j,n):
  d = i+j # current diagonal
  if (row*self.n+col) <= len(self.items)/2:
    return d*(d+1)/2 + j
```

Now, to solve for the lower side, the catch is to realize the the problem is mirrored. I will not derive the solution, but the idea is instead of counting how many elements exist BEFORE, count how many numbers are AFTER. Working on this will give the final mapping function:

``` python
def intsum(k):
  return k*(k+1)/2

def map2Dto1D(i,j,n):
  d = row+col
    if (row*self.n+col) <= len(self.items)/2:
        return self._intsum(d) + col
    # Mirrored
    n = self.n
    return n * n - 1  - self._map2Dto1D(n-row-1, n-col-1)

```

Let's check:

1. c(0,0) = intsum(0)+0 = 0
2. c(1,0) = intsum(1)+0 = 1
3. c(1,1) = intsum(1)+1 = 2
4. c(2,0) = intsum(2)+0 = 3
5. c(1,1) = intsum(2)+1 = 4
6. c(0,2) = 9 -1 - c(2,0) = 9 -1 - (intsum(2)+0) = 5
7. c(2,1) = 9 -1 - c(0,1) = 9 -1 - (intsum(1)+1) = 6
8. c(1,2) = 9 -1 - c(1,0) = 9 -1 - (intsum(1)+0) = 7
9. c(1,2) = 9 -1 - c(0,0) = 9 -1 - (intsum(0)+0) = 8

Now, we can plug the `map2Dto1D` into a data structure that we will call `Array2D`:


``` python
from math import ceil, sqrt

class Array2D():

  def __init__(self, items) :
    self.n = int(sqrt(len(items)))
    self.items = items

  def get(self,row,col):
    return self.items[self._map2Dto1D(row,col)]

  def set(self,row, col, item):
    self.items[self._map2Dto1D(row,col)] = item

  def _intsum(self, k):
    return k*(k+1)/2

  def _map2Dto1D(self, row, col):
    d = row+col
    if (row*self.n+col) <= len(self.items)/2:
        return self._intsum(d) + col
    # Mirrored
    n = self.n
    return n * n - 1  - self._map2Dto1D(n-row-1, n-col-1)
```

And how it is used:

``` python
a = Array2D([1,2,3,4,5,6,7,8,9])
a.get(2,1) // returns (9 - intsum(5-3)-(2-1))= 6 returns 7
a.get(0,1) // Points to (instum(1) + 1) =2 returns 3
a.get(0,0) // Points to (intsum(0) + 0) =0 returns 1
a.get(2,2)  // Points to (9 - intsum(1) - (2-2)) = 8 returns 9
```

[95b4d15a]:https://www.quora.com/Java-programming-language-I-have-a-single-dimensional-array-1-2-3-4-5-6-7-8-9-in-Java-I-want-to-convert-it-two-dimensional-array-with-1-3-6-in-first-row-2-5-8in-second-row-and-4-7-9-in-third-row-but-the-catch-is-we-cant-use-nested-loops-How-can-it-be-done-using-a-single-loop?srid=n2J5 "Java (programming language): I have a singledimensional array [1,2,3,4,5,6,7,8,9] in Java. I want to convert it two dimensional array with [1,3,6] in first row, [2,5,8] in second row and [4,7,9] in third row, but the catch iswe can't use nested loops. How can it be done using a single loop?"
