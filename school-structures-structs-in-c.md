Structures are user-defined data types composed of heterogeneous data, i.e. you can group together different data types into one.

# Defining Stuctures

Keyword is struct

struct[tag_name] {member_declaration_list}

![[/Untitled 63.png|Untitled 63.png]]

Example:

![[/Untitled 1 43.png|Untitled 1 43.png]]

- the above is a **definition** of a struct but it does not allocate memory, it must be initialized to be used

# Structure Instance In Memory

  

![[/Untitled 2 34.png|Untitled 2 34.png]]

# Pointer to Structure Type

- Pointer to must be the type of struct.
- *pst is of type Point

![[/Untitled 3 33.png|Untitled 3 33.png]]

![[/Untitled 4 23.png|Untitled 4 23.png]]

## Access Structure Data Members via PTR

- must de-reference pointer:
    
    ![[/Untitled 5 17.png|Untitled 5 17.png]]
    
    - brackets required b/c dot operator has higher precedence

### Arrow Notation

- can use arrow - > operator
    
    ![[/Untitled 6 14.png|Untitled 6 14.png]]
    
    > [!important]  
    > the thing to left of the array must be a ptr of type struct  
    

All three of syntax are equivalent:

![[/Untitled 7 10.png|Untitled 7 10.png]]

# Structure as Function Argument

![[/Untitled 8 9.png|Untitled 8 9.png]]

> [!important]  
> You can pass structures by reference OR value  

  

# Copying Structure Instances

You can copy arrays in one struct into arrays of another struct

```C
struct Foo {
	int a [5];
};

struct Bar{
	int b[6];
};

int main(void){
	struct Foo a, b;
	a = b; // this is legal??
}
```

# Using Typedef

- used to simplify naming structures
- format:
    
    typedef type_name alias_name;
    
    ![[/Untitled 9 6.png|Untitled 9 6.png]]
    
    - now we can just say Staff instead of struct Staff
