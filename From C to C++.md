---
tags:
  - C/C++
---
C vs C++

Introduction to Standard I/O in C++

C++ Reference Type

AR Representation

Reference as Function Argument

Classes

Information Hiding

What is an Object?

Class Implementation

Getter Functions

Pointers to Objects

Constructor Concepts

In Memory

Extra Constructor Notes

# C vs C++

Similarities:

- rules to declares vars and consts
- simple data types
- aggregated data types (arrays)
- most operators
- control structures
    - if.. else, switch
    - for, while, do..while
    - break, continue, goto
- fcn declaration & definition
- require main() as starting

Differences:

C++:

- supports reference data type
- has different style of casting
- has different initialization style
- uses different standard I/O
- uses different file I/O

- is Object Oriented
    - Class data type

- C is procedural

- supports:
    - inheritence
    - overloading
    - templates

# Introduction to Standard I/O in C++

- Include **iostream** header file to use **cin** and **cout** objects
    
    ```C++
    \#include<iostream>
    using namespace std; // prevents name conflicts
    int main(){
    	int a,b;
    	cout <<"Enter two integer number:" << endl; // cout and cin are objects
    	cin >> a >> b;
    	cout << a << "+" <<b<< " is" << a + b <<:.\nl
    	return 0;
    }
    ```
    
    - >> cin extraction operator
        - read one or more data
        - cin is an **object**
    - << cout insertion operator
        - display on screen
        - cout is an **object**
    - endl is the end of line manipulator

# C++ Reference Type

Reference Type - an alias for a variable that **does not** allocate memory

- you can use the reference to a variable exactly how you would the actual variable
- **must** be initialized

- not the same as pointers, though pointers do exist in C++

- e.g.
    
    ```C++
    int x = 4;
    int& ref = x; // int& is a reference type
    ref = 18; 
    cout << x; // displays 18
    ```
    

## AR Representation

- reference does not have any memory allocated
- solid circle for where ref starts and open circle for the variable it is a reference for

![[/Untitled 13.png|Untitled 13.png]]

The above code uses 16 bytes of memory

## Reference as Function Argument

You can pass variable by reference to functions so that you can change or do operations on the actual variable

![[/Untitled 1 6.png|Untitled 1 6.png]]

x is a reference and a is called a referent of x

Passing const makes a temporary space of memory in main

![[/Untitled 2 7.png|Untitled 2 7.png]]

# Classes

Class is a set of objects that have a common structure

**Class definitions** go in a .h file:

![[/Untitled 3 7.png|Untitled 3 7.png]]

## Information Hiding

Private - members can only be accessed be other members of the same class

Public - members can be accessed from outside the class using dot operator

## What is an Object?

An object is a class that is instantiated, i.e. it takes up memory

## Class Implementation

- This is where you write the definition the functions and attributes of the class
- occurs in a .cpp file
- The **implementatio**n goes in a .cpp file:

![[/Untitled 4 4.png|Untitled 4 4.png]]

- **scope resolution operator** **(::) -** used to associate a function to it's corresponding class

- Constructor
    - Constructor::Constructor()

## Getter Functions

Functions to retrieve member variables of a class

![[/Untitled 5 4.png|Untitled 5 4.png]]

- do not need to change the values of x and y → we declare as read-only using the const keyword
- const functions cannot modify values

- functions that return pointers should declare the pointer const because we want the data to be protected →making the pointer const means the thing that calls the function cannot change the value that the pointer points to

## Pointers to Objects

- pointer can point to any addressable memory location
    
    Notation:
    
    ```C++
    Point c; // object instantiation
    Point *ptr // a Point
    ptr = &c // ptr points to the Point object, c
    cout << ptr ->get_x(); // arrow notation (de-reference) go to what ptr points to
    cout << (*ptr).get_c(); // same as above
    ```
    
- objects can be passed by value, reference or address

## Constructor Concepts

Constructor in the class definition:

![[/Untitled 6 3.png|Untitled 6 3.png]]

Constructor implemented:

![[/Untitled 7 2.png|Untitled 7 2.png]]

- constructor is automatically called when object is declared
- has no return type
- can be overloaded

### In Memory

![[/Untitled 8 2.png|Untitled 8 2.png]]

![[/Untitled 9 2.png|Untitled 9 2.png]]

### Extra Constructor Notes

- compiler creates a default constructor if you do not explicitly make one
- the compiler **will not** create a default constructor if you explicitly make one

There are two ways to initialize member variables:

1)

```C++
class_name::clase_name(v1, v2){
member1 = v1;
member2 = v;
}
```

2) Member Initialization Format

```C++
class_name::class_name(v1, v2): member1(v1),
																member2(v2)
{}
```