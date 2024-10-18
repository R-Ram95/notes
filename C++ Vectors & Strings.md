---
tags:
  - C/C++
---
Vectors

Properties

Syntax

Declaration

Copying

Accessing

Methods

Passing to functions

Matrix: Vector of Vectors

Element Access

Example Code

Strings

Syntax

Accessing Elements

Methods

Assignment Operator

Passing Strings to Functions

Reading C++ Strings

Comparisons

# Vectors

- Vector container class is a better option to use than arrays
- vectors are a wrapper class for built-in arrays to extend the functionality
- part of the Standard Template Library:
    - \#include<vector>

![[/Untitled 34.png|Untitled 34.png]]

## Properties

- Can be copied
- has boundary checking
    - only if you use .at() access, not for [] access
- can be resized ⇒ .resize()
    - you do not need to know size at run time
- can pass by value → pass by ref is preferred

## Syntax

### Declaration

- calls vector default ctor
    
    ```C++
    vector<data type> arrayName;
    ```
    
- initialize with elements
    
    ```C++
    // empty vector
    vector<double> a(0); 
    // 5 elements all initialized to 0
    vector<double> a(5);
    ```
    
- all elements to non-zero value:
    
    ```C++
    // has 3 elements each = 300.0
    vector<double> a(3, 300);
    ```
    

### Copying

```C++
vector<int> v(2);
v[0] = 15;
v[1] = 31;
// elements in 'copy' are the same as elements in 'v'
vector<int> copy(a);
```

### Accessing

- similar to array access
    
    ```C++
    vector<double> grades(5);
    // sets first element to 5.0
    grades[0] = 5.0;
    // this is better b/c it does boundary checking
    grades.at(0, 5.0);
    ```
    

## Methods

- .resize() → used to change the size of an array
    
    ```C++
    vector<int> a(originalSize);
    a.resize(newSize);
    ```
    
- .==empty()== → returns ==true== ==if vector is empty==
- .==push_back(value)== → inserts value to ==end== of vector
- ==.pop_back()== → returns and removes the last element
- ==.at(index)== → same as v[index]
- ==.assign(n , value)== → assigns values to first n elements
- ==.clear()== → deletes all elements

## Passing to functions

- can pass one or more vectors by value
- recommended to pass by reference

- pass-by-ref example:
    
    ```C++
    // pass by ref but could also pass by address const vector<int> *data
    // pass by ref is better b/c it doesnt use memory
    double average( const vector<int>& data )
    {
    	double sum = 0;
    	for( int i = 0 ; i < data.size() ; ++i )
    	{
    		sum += data[i]; 
    	}
    	return sum / data.size();
    }
    ```
    

## Matrix: Vector of Vectors

- vector of vector definitions:
    
    ```C++
    // Option 1
    vector<vector<int> > v;
    
    // Option2 using typedef
    typedef vector<int> row;
    typedef vector<row> matrix;
    
    // Option 3
    typedef vector< vector<int> > matrix;
    
    // declaration
    matrix m; // m has no elements (0 rows, 0 columns)
    ```
    
- setting rows and columns
    
    ```C++
    const int numRows = 3; // number of rows
    const int numCols = 5; // number of columns
    
    m.resize( numRows ); // set number of rows
    
    // for each row, add columns
    for( int j = 0 ; j < numRows ; j++ )
    	// ...and set the number of columns for each row
    	m.at(j).resize( numCols ); 
    ```
    
    - optionally
        
        ```C++
        vector <vector <int> > m (3, vector<int> (5));
        ```
        
- the result:
    
    ![[/Untitled 1 21.png|Untitled 1 21.png]]
    

### Element Access

- using .at is better than [] notation

![[/Untitled 2 17.png|Untitled 2 17.png]]

- in code:
    
    ```C++
    // loop through rows
    for( int i = 0 ; i < m.size() ; i++ )
    	// repeated for each row 
    	for( int j = 0 ; j < m.at(i).size() ; j++ )
    		// repeated for each row and column 
    		cout << m.at(i).at(j) << endl;
    ```
    

## Example Code

- read 10 numbs and print in reverse order
    
    ```C++
    \#include <vector>
    int main()
    {
      // initialize vector with 10 elements
    	vector< int > numbers(10);
    	
    	// loop 10 times, up to but no including 10 b/c vectors are 0 indexed
    	for( int i = 0 ; i < number.size() ; ++i )
    	{
    		cout << “Please enter an integer number for:“ << endl;
    		cin >> numbers [ i ]; // ask user for element
    	}
    	// loop 10 times, start at 9 b/c vectors are 0 indexed
    	for( int i = number.size()-1 ; i >= 0 ; --i )
    		cout << numbers.at(i) << endl;
    	return 0;
    }
    ```
    

  

# Strings

- \#include<string>

- String is a character arrays wrapped in a String object

## Syntax

- empty string
    
    ```C++
    // calls default constructor
    string stringName;
    ```
    
- initialization
    
    ```C++
    string stringName("Initialization String Here")
    
    // sets J to the first character
    string firstInitial( 1,‘J’ );
    
    // sets J to 3 characters
    string firstInitial( 3,‘J’ );
    ```
    
- Creating string as a copy
    
    ```C++
    // this uses the copy ctor
    string stringName( otherStringName );
    
    // Example usage
    string student1( “John Smith” );
    string student2( student1 ); // contents of this string is John Smith
    ```
    

## Accessing Elements

- same as access to vectors
    - using []
    - using .at()
- element of a string is a char
- example:
    
    ```C++
    string student_name(“John Smith”);
    student_name.at(4) = ‘-’;
    cout << student_name; // ouputs "John-Smith”
    ```
    
    - in memory
        
        ![[/Untitled 3 17.png|Untitled 3 17.png]]
        
        ![[/Untitled 4 12.png|Untitled 4 12.png]]
        

## Methods

- ==.size() or .length()== - returns int number of characters in string excluding '\0'
- ==.append()== - can use += as well, adds argument to end of the string
- ==getline()==
    - not a member function
    - reads string up to but not including '\n' (default)
        
        ```C++
        string line_of_text;
        cout << “Enter a line of text: ”;
        // we must pass in cin object and we specify the delimeter, in this case, \n
        getline( cin, line_of_text, ‘\n’ ); 
        ```
        
- ==.erase(k, n)== - deletes n characters starting at index k
- ==.insert(k, "string")== - inserts string starting at index k
- ==.empty()== - returns true if string is empty
- ==.substr(exp1, exp2)== - returns substring starting at index exp1 of length exp2

## Assignment Operator

- string on lhs of operator will be automatically resized
    
    ```C++
    string a( “First String” );
    string b( “Second String” );
    
    a = “Third String”;
    
    cout << a << endl; // ouputs "“Third String”
    ```
    

## Passing Strings to Functions

- recommended to pass by ref but not required
    
    ```C++
    void get_filename(string& filename )
    {
    	cout << “Enter file name\n”;
    	cin >> filename;
    	cout << “You entered “ << filename << endl;
    }
    
    void main()
    {
    	string input_file_name;
    	// get input file name
    	get_filename(input_file_name); // pass input_file_name by reference
    	...
    }
    ```
    

## Reading C++ Strings

- can use input operator cin >>
    - this only reads till first: '\n', '\t', '\r'
    - can only read one word at a time

## Comparisons

- the comparison operators are overloaded:
    - <, >, <=, >=, !=, ==
- use ASCII to determiner correct order