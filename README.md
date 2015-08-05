# Build and Run 
1. change directory into the target directory (1_poker, 2_prime_numbers, 3_fibonacci, 4_median_of_array)
2. $make
3. $./main

**Test environments**

machine1:

**Comipler:** Apple LLVM version 6.1.0 (clang-602.0.49) (C++11 supported)

**OS:** OS X Yosemite 10.10.2 

machine2:

**Compiler:** g++ 4.8.4 (c++11 supported)

**OS:** Ubuntu 14.04.2 LTS

# Q1. How to implement poker card-wash function, then pick up 5 cards?

## Shuffle
Use Fisher–Yates shuffle algorithm; every permutation of deck generated by this algorithem is equally likely.

```
To shuffle an array A of n elements (indices 0..n-1):
  for i from n − 1 downto 1 do
       j = random integer such that 0 <= j <= i
       swap A[j] and A[i]
```

### Complexity
Time Complexity: ```O(n)```, assumes generating a random integer takes ```O(1)``` time.

Space Complexity: ```O(1)```

### Code
1_poker/deck.cpp: ```Deck::shuffle```

## Pick Up 5 Cards

First, implement the function to pick up one cards in the deck.
Use a std::vector to store cards within a deck. 
Assume the front of the vector is the bottom of the deck, 
and the back of the vector is the top of the deck. 
When picking up a card, always pick up from the top of the deck.

**pick up one card function:**

```
To pick up a card from a deck as a vector:
if the vector is not empty
	pop out the card from the back of the vector and return it
else
	return a null pointer
```

**pick up five card**

```
To pick up 5 cards from a deck:
run the pick up one card function 5 times
```

### Code

1_poker/deck.cpp: ```Deck::pickOneCard```

1_poker/main.cpp: starts from ```// picking up cards from the deck```


### Sample Output

**Suits:**
C - Club, H - Heart, D - Diamond, S - Spade

**Ranks:**
A - Ace, J - Jack, Q - Queen, K - King, Number - Point of the card

e.g. CA is Club Ace, S5 is Spade 5

```
Before shuffling:
CA C2 C3 C4 C5 C6 C7 C8 C9 C10 CJ CQ CK DA D2 D3 D4 D5 D6 D7 D8 D9 D10 DJ DQ DK HA H2 H3
H4 H5 H6 H7 H8 H9 H10 HJ HQ HK SA S2 S3 S4 S5 S6 S7 S8 S9 S10 SJ SQ SK
After shuffling:
H5 C2 H10 HQ D2 CA D8 S9 S4 S8 S6 H9 C5 C9 SK C8 C3 D5 S5 H3 H4 C7 DJ S2 S3 CJ CK D7 D10 
H2 H7 HJ C6 DQ D9 HK D6 S7 HA SJ DA D4 CQ H6 DK S10 C4 D3 C10 H8 SQ SA
Picking up 5 cards:
SA
SQ
H8
C10
D3
```

p.s. the print deck function prints the card vector from the front to the back.

# Q2. How to find 2 to 100 prime numbers?

Use Sieve Of Eratosthenes algorithm to find all prime numbers.
The algorithm generates a boolean table of size 99 (indices 0..98).
Each entry indicates whether the number (index number + 2) is prime or not. true => "prime", false => "not prime"

algorithm:

```
Let A be an array of Boolean values, indexed by integers 0 to 98;
initially set indices from 0 to 98 to true.
 
for i = 2, 3, 4, ..., 100
	if A[i - 2] is true
		for j = i+i, i+2*i, i+3*i, ...,  not exceeding 100
			A[j - 2] = false

all i such that A[i - 2] is true are prime numbers
```

## Optimizations

1. From the theorem: if a positive integer N doesn't have any prime factor <= sqrt(N), it must be a prime number. Therefore, we can just iterate through 2,3,4, ... sqrt(100), find prime numbers and mark all their multiples within table as "not prime". At the moment, the numbers still marked as "prime" must not have any prime factor <= sqrt(N), so they are certainly to be prime numbers.

2. When finding a prime number, we mark all its multiples within table as "not prime". However, there will be numbers that has already been marked by the smaller prime numbers. e.g. when finding 7, we will mark 7*2, 7*3, 7*4 ... as "not prime", but actually 7*(2~6) has already been marked by smaller prime numbers: 2, 3, 5. Therefore, after finding a prime number P, we can directly start from P^2 to mark the table instead of from P*2.

improved algorithm:

```
Let A be an array of Boolean values, indexed by integers 0 to 98;
initially set indices from 0 to 98 to true.
 
for i = 2, 3, 4, ... sqrt(100):
	if A[i - 2] is true
		for j = i^2, i^2+i, i^2+2*i, i^2+3*i, ...,  not exceeding 100
			A[j - 2] = false

all i such that A[i - 2] is true are prime numbers
```

## Complexity

Time Complexity: ```O(n log log n)```

Space Complexity: ```O(n)```

## Code
2_prime_numbers/main.cpp: ```printPrimes```

## Sample Output
```
Prime numbers from 2 to 100
2 3 5 7 11 13 17 19 23 29 31 37 41 43 47 53 59 61 67 71 73 79 83 89 97
```

# Q3. Given a sequence 1,1,2,3,5,8,13,21,34,55... Please write a function can return the nth value. Please give us at least 3 different solutions.

## Solution 1: Bottom Up
Calculate each fibonacci value from 3rd to nth. (1st and 2nd fibonacci value is known as 1)

```
if n is 1 or 2 
	return 1 // 1st and 2nd fibonacci value is 1
a = 1
b = 1
for i from 3 upto n
	f = a + b
	a = b
	b = f
return f // f is the nth fibonacci value
```
## Complexity
Time Complexity: ```O(n)```
Space Complexity: ```O(1)```

## Code 
3_fibonacci/main.cpp: ```fibBottomUp```

## Solution 2: Top Down with Memorization
Recursively calculate the nth fibonacci value from top to bottom.
Memorize the calculated fibonacci value.
If we encounter the same sub-problem during the recursion, return the answer from memorization table.

```
fib is a n size array memorizing fibonacci values (fib[i] is (i + 1) th fibonacci number)
initialize fib[0] and fib[1] to 1 and others to -1 meaning "no answer yet"

function topdown(fib, n)
	if fib[n - 1] is not -1
		return fib[n - 1]
	ret = topdown(fib, n - 1) + topdown(fib, n - 2)
	fib[n - 1] = ret
	return ret

```
## Complexity
Time Complexity: ```O(n)```
Space Complexity: ```O(n)```

## Code 
3_fibonacci/main.cpp: ```fibTopDown```

## Solution 3: Matrix Exponentiation

We can get nth fibonacci value from the below formula
e.g. if we want to get the 10th fibonacci value, we calculate matrix [[1,1],[1,0]] to the power of 9. The power result's (0,0) position will be the 10th fibonacci value

![https://en.wikipedia.org/wiki/Fibonacci_number#Matrix_form](https://upload.wikimedia.org/math/a/6/0/a6083f85f39b468210f5715a8e30d572.png)
from wikipedia:Fibonacci_number

The matrix power can be calculated recursivly, so the time complexity can be reduced to O(log n)
e.g. if we want to get mat^8, we first calculate mat^4 (mat^4 will be calculated by calculating mat^2, and so on...)
and then we multiply mat^4 by mat^4 to get the mat^8

if we are calculating matrix to the power of an odd number 
we need to multiply the original base matrix one more time
e.g. when calculating mat^9, we use recursive calculation to get the mat^8
and then we multiply the mat^8 by mat to get mat^9

```
initialize fib[2][2] to matrix [[1,1],[1,0]]
recursively calculate fib to the power of n - 1

the fib[0][0] will be n-th fibonacci value
```

## Complexity
Time complexity: ```O(log n)```

Space complexity: ```O(1)```

## Code
3_fibonacci/main.cpp: ```fibMatrix```, ```matrixPow```, ```matrixMul```

## Sample Input/Output
```
Enter n to find nth fibonacci value: (1 <= n <= 93)
50
Solution 1: Bottom Up
12586269025
Solution 2: Top Down with Memorization
12586269025
Solution 3: Matrix Exponentiation
12586269025
```

p.s. 
Since maximum unsigned long long is      18,446,744,073,709,551,615 ,
it cannot correctly store 94th fibonacci 19,740,274,219,868,223,167.
The maximum nth allowed to be entered is 93.

# Q4. Show us how to figure the medium-number of array (6,38,49,55,11,44,33); and array (11,41,42,43,31,91,81,71,61).

Use median of medians algorithm which find k-th smallest element in an array. If array length N is odd, the median will be (N/2+1)-th smallest element. If array length N is even, the median is the average number of (N/2)-th smallest element and (N/2+1)-th smallest element.

**Median of Medians Algorithm:**

```
function findKthSmallest(array, left, right, k)

	n = right - left + 1    is the length of the array
  
	1. Divide array into ceil(n/5) groups. Each group with size 5 
	except for the last group which may have less than 5 elements

	2. Sort each group, find each group's median and store them in an array median[]

	medianNum = number of medians within median[] array
	3. find median of medians: medOfMed = findKthSmallest(median[0..medianNum - 1], medianNum - 2) 
	note that we don't need to consider odd and even array length cases since this median is used
	for partition
	
	4. partition array around medOfMed and get its position  
	pos = partition(arr, n, medOfMed)
  
	5. judge whether pos is the kth element or recursively find kth element within sub-array
	if pos = k
		return medOfMed
	if pos < k
		return findKthSmallest(arr[l..pos-1], left, pos-1, k)
	if pos > k
  	return findKthSmallest(arr[pos+1..r], pos+1, right, k-pos+l-1)
   
```

## Complexity
Time complexity: ```O(n)```
Space complexity: ```O(1)```

## Code
4_median_of_array/main.cpp

## Sample Output

```
Array: 6 38 49 55 11 44 33
Median: 38
Array: 11 41 42 43 31 91 81 71 61
Median: 43
```
