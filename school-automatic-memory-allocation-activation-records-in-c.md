# Memory Allocation Space

Programs are run in RAM. Four memory segments are used:

- Code Segement
    - machine code of the program is stored
- Static Segment
    - stores global and static variables
    - includes String Constant Area where all program strings are stored
- Stack
    - this is where functions and their local variables are stored
- Heap
    - used for dynamic memory allocation


Global variables are initialized to 0

variables on stack that aren't initialized hold garbage

# Activation Record (AR)

At this point there are three ways of memory allocation:

1) global declaration

2) local variables in the body of functions

3) argument declaration

  

2) & 3) are **automatic** declaration b/c these variables are allocated when a fcn is active and de-allocated when the fcn dies

```C
const double PI = 3.1415; // PI is a global constant - not automatic 
double square (int x) // x a function argument and automatic var.
{
	double result; // result is a local and automatic variable
	result = x * x;
	return result;
}
```

** there are other ways of memory allocation

An Activation Record is a snapshot of the stack showing at a moment in time showing which functions are active and the local variables in memory

## AR: Anatomy

- divided into two regions
    - upper region - local variables
    - lower region - fcn arguments
- drawn on stack
- variable should have value or ? if value is unknown
- drawn in order from bottom to top (main on the bottom)


  


