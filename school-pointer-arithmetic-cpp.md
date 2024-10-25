
# Legal Operations

Pointer + Integer

Integer + Pointer

Pointer - Integer

Pointer - Pointer

Pointer++

++Pointer

Pointer—

—Pointer

  

All other operations are illegal

# Pointer - Integer Addition (& Subtraction)

pointer + n → gives address of nth element from current address

What is actually says is this:

pointer + n == address_value + n * sizeof(type)

Example:

```C
double d[3] = {4.5, 6.9, 7.7};
double* ptr = &d[0]; // pointer refers to the address of the first element in d
ptr = ptr + 2; // The new value of ptr is 988 + 2 * 8 = 1004
```

![[/Untitled 47.png|Untitled 47.png]]

- *ptr now equals 7.7

# Pointer - Pointer

pointer - pointer → integer value representing the # of elements between the two pointers'

```C
int arr[5] = {2, 6, 4, 7, 9};
int* ptr;
int diff;
ptr = arr + 5; // ptr points to arr[5] after the last element
// Allowed to write: ptr = 5 + arr;
diff = ptr – arr;
```

last line is this:

diff = (address of last element - address of first element) / size(int)

  

Array notation and pointer notation are interchangeable

```C
int myArray[5] = { 31, 41, 22, 66, 90};
int* ptr = myArray + 2;
```

The following are all true:

![[/Untitled 1 31.png|Untitled 1 31.png]]
