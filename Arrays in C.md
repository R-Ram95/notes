---
tags:
  - C/C++
---
1.0 Arrays Introduction

1.1 Declaration

1.2 Initialization

1.3 Copying Issues

2.0 Array of Characters & C-Strings

2.1 Character, Character Constant, String Constant

2.1.1 Character

2.1.2 Character Constant

2.1.3 String Constant

2.2 C-Strings

3.0 Arrays and Pointers

3.1 Strings & Pointers

3.2 Passing Arrays to Functions

4.0 Using scanf to Read Strings & Characters

4.1 const Keyword as Function Argument

5.0 Pointers to Constant Characters

# 1.0 Arrays Introduction

An array contains objects of a given type, stored **consecutively** in a **continuous** memory block

Syntax:

**type_name identifier [ number_of_elements ];**

Example:

```C
int a[3] = {44, 66, 85}; // declares array of three ints
```

in memory:

![[/Untitled 11.png|Untitled 11.png]]

## 1.1 Declaration

- Can have any storage class (visibility and location of variable)
    - declared outside fcn
    - declared local variable
    - declared within block
    - declared as local or global **static**
- Arrays must be passed by reference:
    
    The only restriction on arrays is that they cannot be used as an argument to functions, a pointer to the first element of an array must be used.
    
- C99 allows for variable-length array declaration:
    
    ![[/Untitled 1 4.png|Untitled 1 4.png]]
    

## 1.2 Initialization

- use initialization list to explicitly define array:
    
    ```C
    int a[5] = {10, 22, 33, 44, 66};
    ```
    
- if not explicitly defined:
    - automatic storage → elements have undefined values (garbage)
    - for other storage → elements initialized to 0

## 1.3 Copying Issues

- arrays in C cannot be copies:
    
    ```C
    int a[2];
    int b[2];
    a[0] = 55;
    a[1] = 60;
    b = a; // ERROR this is not allowed
    ```
    

- Index range checking

# 2.0 Array of Characters & C-Strings

## 2.1 Character, Character Constant, String Constant

### 2.1.1 Character

- one byte integer
- can assign integer of character constant
    
    ```C
    char c;
    c = 65;
    c = 'A'
    // both are legal
    ```
    

### 2.1.2 Character Constant

- Confined between **single quotation marks**
- can be a letter, digit or non-printing character

### 2.1.3 String Constant

- Confined between **double quotation marks**

## 2.2 C-Strings

- C-Strings MUST terminate with the NULL ('\0') character
- a string is an **array of character** terminated by **NULL('\0')**
    
    ```C
    char name[6] = "Jack";
    ```
    

![[/Untitled 2 5.png|Untitled 2 5.png]]

- when declaring a C-string, the length of the Character array must be able to include NULL at the end
    
    ```C
    char str1[5] = "Apple"; // Array of chars but NOT a C-String
    char str2[] = "XYZ" // Array of chars AND C-String
    char str3[3] = "AB"; // Array of chars AND C-String
    ```
    
- displaying strings:
    
    ```C
    printf("%s", str2); // displays XYZ (all chars from first element to NULL)
    ```
    

# 3.0 Arrays and Pointers

- name of an array is treated like a fixed-pointer that always **points to the first element of an array**
    - it cannot hold another address
    - the name holds the address of the first byte of the first element

## 3.1 Strings & Pointers

- String name is pointer to the first element of an array of chars
    
    ```C
    char name[6] = "Jack";
    name == &name[0] // TRUE - name equals the address of the first element in name
    ```
    

## 3.2 Passing Arrays to Functions

- can pass array to fcn using it's name as an argument
- fcn's argument must be a pointer b/c the name of an array is an address
    
    ```C
    \#include <stdio.h>
    
    int largest (const int *arr, int n); // argument to fcn is pointer in pointer notation
    
    int main(){
    	int x[5] = {90, 3, 4, 5, 1};
    	int result;
    	result = largest(x, 5) // passing name of array, i.e. the address of(&) the first element
    	...
    }
    ```
    
- fcn argument can also be in array notation
    
    ```C
    \#include <stdio.h>
    
    int largest (const int arr[], int n); // argument to fcn is pointer in array notation
    
    int main(){
    	int x[5] = {90, 3, 4, 5, 1};
    	int result;
    	result = largest(x, 5) // passing name of array, i.e. the address of(&) the first element
    	...
    }
    ```
    

# 4.0 Using scanf to Read Strings & Characters

- scanf uses %s as string identifier
- it reads till it encounters a whitespace from keyboard
    - white spaces include space bar, tab and return
- does not require address of (&) operator to read strings because the name of a C-string is an address
    - & is required to read character though
        
        ```C
        scanf (“%c”, &lastName[0]);
        ```
        
- other library functions to read strings
    - gets
    - fgets
    - getc
    - fgetc

## 4.1 const Keyword as Function Argument

- use to make pointers read-only
- protects from unwanted changes
    
    ```C
    void doSothing( const double *p)
    {
    *p = 100.5; // illegal
    // more code..
    }
    ```
    

# 5.0 Pointers to Constant Characters

- char* can be used to define a c-string
    - char *p = "KLH" // not as good
    - const char *ptc = "CBC" // better - why??
- AR diagram:
    
    ![[/Untitled 3 5.png|Untitled 3 5.png]]
    
    - const char *ptr is located in String Constant Area in Static
    - char str [] is located on the stack