Structures are user-defined data types composed of heterogeneous data, i.e. you can group together different data types into one.

# Defining Stuctures

Keyword is struct

struct[tag_name] {member_declaration_list}


Example:


- the above is a **definition** of a struct but it does not allocate memory, it must be initialized to be used

# Structure Instance In Memory

  


# Pointer to Structure Type

- Pointer to must be the type of struct.
- *pst is of type Point



## Access Structure Data Members via PTR

- must de-reference pointer:
    
    
    - brackets required b/c dot operator has higher precedence

### Arrow Notation

- can use arrow - > operator
    
    
    > [!important]  
    > the thing to left of the array must be a ptr of type struct  
    

All three of syntax are equivalent:


# Structure as Function Argument


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
    
    - now we can just say Staff instead of struct Staff
