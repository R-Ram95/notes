#
- hierarchy of stream classes in C++ standard library

# File I/O

## Steps for File I/O

1. Create the streams
2. Open the files
    1. make sure file opened properly
3. Close the files
    
    ```C++
    \#include <fstream>
    
    int main(void){
    	// 1. create stream objects
    	ifstream in_stream; // file input
    	ofstream out_stream; // file output
    
    	// 2. Open the files
    	in_stream.open("input.txt");
    	out_stream.open("output.txt");
    
    	// 2a. make sure files opened properly
    	if(in_stream.fail())
    	{
    		cout<< "Error"; // print error message
    		exit(1); // leave the program
    		
    		// optional
    		in_stream.clear(); // clears the stream buffer if you want to read more files
    											 // if you dont do this, any other reading will fail
    	}
    
    	if(out_stream.fail())
    	{
    		cout<< "Error";
    		exit(1);
    	}
    
    	// DO YOUR READING AND WRITING HERE
    
    	// 3. close the files
    	in_stream.close();
    	out_stream.close();
    
    }
    ```
    

## On Opening a File

```C++
ofstream out_file( “output.dat”, ios::app ); // open the file for writing, append
```

- this line will:
    - create the file if it doesn't exists
    - overwrite the file if it exists UNLESS the ios::app flag is specified to say, append contents to the file

## I/O Member Functions

- ==.clear()== → clears failure flags from streams
- ==.eof()== → returns true if end of file was reached
- ==.get()== → use to read single characters, including white spaces
    - ==.get(string, n, delimeter)== → reads n characters or till delimeter is reached from string
        - the delimeter is not read, it stays in the buffer to be read
- ==getline()==→ reads all characters including the delimeter
- ==.putback(ch)== → pushes a character into the iostream
- ==peek()== → returns next character on stream, but does not extract it
- ==Ignore(int limit =1, int delim=EOF)== → discards up to limit characters and stops if it reaches delim character
- ==.put(ch)== → used as alternative to inserting character into the output stream

## Examples for File I/O

### Prompting for file names

```C++
\#include <string> // for strings
\#include <fstream> // for file i/o
\#include <iostream> // for keyboard/monitor i/o

using namespace std;

void main()
{
	string input_file_name; // string objects
	string output_file_name; // string objects
	cout << “Enter input file name: ” << endl; // read out using os
	cin >> input_file_name; // read in using os
	cout << “You entered “ << input_file_name << endl;

	ifstream in_file( input_file_name);

	// check that the file was opened properly (omitted)

	cout << “Enter output file name: ” << endl;
	cin >> output_file_name;
	cout << “You entered “ << output_file_name << endl;

	ofstream out_file( output_file_name.c_str(), ios::app );
	// **NOTE: we have to call: output_file_name.c_str() to convert
	// the file name to a C String

	// check that the file was opened properly (omitted)

	// use the streams here
	// close the streams
	in_file.close();
	out_file.close();
}
```

### Reading a Full Line

```C++
ifstream infile( “input.dat” ); 
// check that file opened properly here

string line_of_text;
// read lines from infile terminated by \n and store in string

getline( infile, line_of_text );
// **default delimiter is newline
```

  

### Example

```C++
\#include <fstream>
\#include <cstdlib>
using namespace std;

void main()
{
	int num1, num2, num3, num4;
	ifstream infile( "input.dat" );

	// check that input file was opened properly HERE!

	ofstream outfile( "output.dat" );

	infile >> num1 >> num2 >> num3 >> num4;// read data from infile into num1 - num4
	outfile << "The four numbers read were:\n" // insertion operator is overloaded
	<< num1 << endl << num2 << endl 
	<< num3 << endl << num4 << endl;
	infile.close();
	outfile.close();
}
```

### Using .eof, get() and getline()

```C++
// read a single character
char ch = streamObject.get(); 

while ( !streamObject.eof() ) 
{
	streamObject << ch;
// read single character at a time
	char ch = streamObject.get();
}
```

```C++
// read either 50 chars or up to but not including \n
streamObject.get(s, 50, ‘\n’);

while ( !streamObject.eof() ) 
{
	streamObject << ch; // read 
// read single character at a time
	char ch = streamObject.get();
}
```

```C++
char s[50];
// same as above except it reads \n off the buffer and discards it
streamObject.getline(s, 50, ‘\n’); 
while ( !streamObject.eof() ) 
{
	…
}
```

  

# Binary File I/O

## Opening Binary File

- need to open the file in binary mode:
    
    - using ios::binary flags
    
    ```C++
    ofstream outfile("myouput.bin", ios::out | ios::binary);
    ifstream infile("input.bin", ios::in | ios::binary):
    ```
    
    Alternatively use:
    
    ```C++
    fstream in_outfile("myfile.bin", ios::in | ios::out |ios::binary);
    ```
    

## Writing to Binary File

- .write() prototype:
    
    ostream& write (char* address, streamsize n);
    
    - address - address of the first element of the data we want to write to the file
    - n - size of the data we want to write
- We must cast the address to a (char*) because a char is the only datatype in c++ that is one byte

### Example: Writing Data

```C++
\#include <iostream>
\#include <fstream>
using namespace std;

int main(void)
{

	ofstream os ("test.bin", ios::binary);

	// data we want to write
	double a []= { 2.3, 3, 10, 44};
	
  // the address of a is cast to a char*	
	os.write ((char*) (a), sizeof(a)); // writes 32 bytes in one shot
	
	if (os)
		std::cout << "All 4 element of array a, 32 bytes, are written into test.bin.";
		else
			os.close();
	return 0;
}
```

### Example: Writing One Element at a time

```C++
\#include <iostream>
\#include <fstream>
using namespace std;

int main(void)
{

	ofstream os ("test.bin", ios::binary);

	// data we want to write
	double a []= { 2.3, 3, 10, 44};
	
	for(int i =0; i < 4; i++)
	{
		/**
				Now we use the address of a single element and every loop we only write 8 bytes,
				the size of a double
		**/
		os.write ((char*) (&a[i]), sizeof(double)); // Writes 8 bytes in each iteration
		if(!os)
		cerr << "Sorry! faild to write into the the output file...\n”;
	}
	
	os.close();
	return 0;
}
```

  

## Reading Binary File

- use .read():
    
    istream& read (char* address, streamsize n);
    
    - n - size of data to be read in bytes, streamsize is a long int
    - address - starting address of where to read the data into
- .gcount() - returns number of bytes read from input datastream

  

### Example: Reading Data

```C++
int main(void)
{
	ifstream is ("test.bin", ios::binary);
	double a [4];
	// read 32 bytes of data and store at address of a
	is.read ((char*) (a), sizeof(a))
	
	// libray function gcount returns number bytes read from input stream
	long int g = is.gcount(); 
	
	if (is)
		std::cout << ”Values of 4 element of array are read from binary file test.bin\n" <<
		"Number of bytes read from test.bin is: " << g << endl;
	else
		is.close();
	return 0;
}
```

  

### Example: Reading Data one Element at a Time

```C++
int main(void)
{
	ifstream is ("test.bin", ios::binary);
	double a [4];
	
	for(int i = 0; i < 4; i++){
		is.read ((char*)(&a[i]), sizeof(double));
		long int g = is.gcount(); // returns 8 bytes in each read
		cout << "i = " << i << " and g = " << g << endl;
	}

	for(int i =0; i < 4; i++)
		cout << a[i] << endl;

	is.close();
	return 0;
}
```
