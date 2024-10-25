
# Data on Memory

- variables and function argument data located on RAM
- Binary Representation:

![[/Untitled 44.png|Untitled 44.png]]

- integer-like data types can be signed or unsigned:
    - signed - left most bit indicated sign
        
        ![[/Untitled 1 29.png|Untitled 1 29.png]]
        
    - unsigned - all bits are used
        
        ![[/Untitled 2 24.png|Untitled 2 24.png]]
        
- adding one byte to the left double to max value of the data
- overflow results in garbage
    - eg. if x is signed int and we store 128 in x, the value of x will be garbage
- Hexadecimal is used to store addresses

# Memory and Address

## Memory Address

- every byte has its own address
    - address of an object is its first byte

## Address Operator(&) and Pointers

- & - "address of" operator
    - returns the address of a variable
- * - pointer
    - int *p - read like "p is an integer pointer"
    - pointer should point to the same type of object as the type of the pointer
    - size of pointer is 8 bytes on 64-bit machine, 4 bytes on 32-bit machine
    - called redirection or dereference when used in function argument
        - i.e. get the value in the memory address that the pointer holds
- pointers allow us to **pass-by-reference** rather than **pass-by-value**
    - pass-by-value → copies of data created in fcn call
    - pass-by-reference → fcn receives address of the data and has access to data through a pointer

### Example - pointer ex 1

**CODE:**

```C
int main () {
	int j, k, *p; // P is an int pointer
	// point one
	p = &j; // p points to j
	// point two
	*p = 3; // go to what p points to and set it to 3
	// point three
	p = &k; // p points to k
	// point four
	*p = 7; // go to what p points to and set it to 7
	// point five
	return 0;
}
```

**AR Diagram**

![[/Untitled 3 24.png|Untitled 3 24.png]]

### Example 2: Pass-by-Reference

Problme with Pass-by-value:

```C
void swap(int x, int y);
int main() {
	int a = 5, b = 8;
	swap(a, b);
	printf(“a = %d b = %d”, a, b); /* prints a=5 and b=8. */
}

void swap (int x, int y) {
	int tmp = x;
	x = y;
	y = tmp;
}
```

swap() gets copies of a and b but no reference to where these variables are stored in memory. swap() merely swaps it's local copies of a and b, but not the actual contents of a and b.

Fixed with Pass-by-Reference:

```C
void swap(int *x, int *y);
int main() {
	int a = 5, b = 8;
	swap(&a, &b); // pass the address of a & b
	printf(“a = %d b = %d”, a, b); /* prints a=8 and b=b. */
}

void swap (int *x, int *y) {
	int tmp = x;
	*x = y;
	*y = tmp;
}
```

### Example 3: Pointer and AR

![[/Untitled 4 17.png|Untitled 4 17.png]]

# More on Scanf

## Input Stream

- input as a stream of characters, not a sequence of lines of text
- scanf() consumes only enough characters to do its job
- the remaining characters on the stream are not consumed, they sit there and wait for another operation

Example:

```C
\#include <stdio.h>
void main( void )
{
	int i;
	char ch;
	printf(“Please enter an integer “);
	scanf(“%d”, &i );
	printf(“Please enter a character “);
	scanf(“%c”, &ch ); // this call does not work
}
```

- the input stream (input buffer) must be cleaned up before the second call to scanf
