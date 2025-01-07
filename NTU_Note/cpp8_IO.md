[toc]

# Stream IO and File IO

---

> https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp10_IO.html



## Stream IO



### the Standard Stream Objects

* The `<iostream>` header declares these *standard stream objects*:

1. `cin` (of `istream` class, `basic_istream<char>` specialization), `wcin` (of `wistream` class, `basic_istream<wchar_t>` specialization): corresponding to the *standard input stream*, defaulted to keyword.

2. `cout` (of `ostream` class), `wcout` (of `wostream` class): corresponding to the *standard output stream*, defaulted to the display console.

3. `cerr` (of `ostream` class), `wcerr` (of `wostream` class): corresponding to the *standard error stream*, defaulted to the display console.

4. `clog` (of `ostream` class), `wclog` (of `wostream` class): corresponding to the *standard log stream*, defaulted to the display console.

   

### The `ostream` class

* Formatting Output via the Overloaded Stream Insertion << Operator

```c++
ostream & operator<< (type)   // type of int, double etc
```

> [!TIP]
>
> `void *`: can be used to print an address.
>
> ```c++
> char str1[] = "apple";
> const char * str2 = "orange";
>  
> cout << str1 << endl;   // with char *, print C-string
> cout << str2 << endl;   // with char *, print C-string
> cout << (void *) str1 << endl;  // with void *, print address (regular cast)
> cout << static_cast<void *>(str2) << endl;  // with void *, print address
> ```

* Flushing the Output Buffer

1. flush member function or manipulator:

```c++
// Member function of ostream class - std::ostream::flush
ostream & flush ();
// Example
cout << "hello";
cout.flush();
 
// Manipulator - std::flush
ostream & flush (ostream & os);
// Example
cout << "hello" << flush;
```

2. `endl` manipulator, which inserts a newline and flush the buffer. Outputting a newline character '\n' may not flush the output buffer; but `endl` does.

```c++
// Manipulator - std::endl
ostream & endl (ostream & os)
```

3. `cin`: output buffer is flushed when input is pending, e.g.,

```c++
cout << "Enter a number: ";
int number;
cin << number;  // flush output buffer so as to show the prompting message
```



### The `istream` class

* Formatting Input via the Overloaded Stream Extraction >> Operator

```c++
istream & operator<< (type &)   // type of int, double etc.
```

* Flushing the Input Buffer - ignore()

You can use the `ignore()` to discard characters in the input buffer:

```c++
istream & ignore (int n = 1, int delim = EOF);
    // Read and discard up to n characters or delim, whichever comes first
 
// Examples
cin.ignore(numeric_limits<streamsize>::max());        // Ignore to the end-of-file
cin.ignore(numeric_limits<streamsize>::max(), '\n');  // Ignore to the end-of-line
```



### Unformatted Input/Output Functions

* `put(), get() and getline()`

The `ostream`'s member function `put()` can be used to put out a `char`. `put()` returns the invoking `ostream` reference, and thus, can be cascaded. For example,

```c++
// ostream class
ostream & put (char c);  // put char c to ostream
// Examples
cout.put('A');
cout.put('A').put('p').put('p').put('\n');
cout.put(65);
```

```c++
// istream class
// Single character input
int get ();   
      // Get a char and return as int. It returns EOF at end-of-file
istream & get (char & c);
      // Get a char, store in c and return the invoking istream reference
 
// C-string input
istream & get (char * cstr, streamsize n, char delim = '\n');
      // Get n-1 chars or until delimiter and store in C-string array cstr.
      // Append null char to terminate C-string
      // Keep the delim char in the input stream.
istream & getline (char * cstr, streamsize n, char delim = '\n');
      // Same as get(), but extract and discard delim char from the
      // input stream.
```

```c++
// Examples
int inChar;
while ((inChar = cin.get()) != EOF) {  // Read till End-of-file
   cout.put(inchar);
}
```

* `read(), write() and gcount()`

```c++
// istream class
istream & read (char * buf, streamsize n);
      // Read n characters from istream and keep in char array buf.
      // Unlike get()/getline(), it does not append null char at the end of input.
      // It is used for binary input, instead of C-string.
streamsize gcount() const;
      // Return the number of character extracted by the last unformatted input operation
      // get(), getline(), ignore() or read().
 
// ostream class
ostream & write (const char * buf, streamsize n)
```

* `peek() and putback()`

```c++
char peek ();
      //returns the next character in the input buffer without extracting it.
 
istream & putback (char c);
      // insert the character back to the input buffer.
```



## File Input/Output (Header <fstream>)



### File Output

Steps:

1. Construct an `ostream` object.
2. Connect it to a file (i.e., file open) and set the mode of file operation (e.g, truncate, append).
3. Perform output operation via insertion `>>` operator or `write()`, `put()` functions.
4. Disconnect (close the file which flushes the output buffer) and free the `ostream` object.

```c++
#include <fstream>
.......
ofstream fout;
fout.open(filename, mode);
......
fout.close();
 
// OR combine declaration and open()
ofstream fout(filename, mode);
```

> [!CAUTION]
>
> File Modes:
>
> `ios::trunc` - truncate the file and discard old contents.
>
> For output, the default mode is `ios::out | ios::trunc`. 
>
> For input, the default mode is `ios::in`.



### File Input

Steps:

1. Construct an `istream` object.
2. Connect it to a file (i.e., file open) and set the mode of file operation.
3. Perform output operation via extraction `<<` operator or `read()`, `get()`, `getline()` functions.
4. Disconnect (close the file) and free the `istream` object.

```c++
#include <fstream>
.......
ifstream fin;
fin.open(filename, mode);
......
fin.close();
 
// OR combine declaration and open()
ifstream fin(filename, mode);
```

> [!CAUTION]
>
> * Most of the `<fstream>` functions (such as constructors, `open()`) **supports filename in C-string only**. You may need to extract the C-string from `string` object via the `c_str()` member function.
>
> * The `get(char &)` function returns a null pointer (converted to `false`) when it reaches end-of-file.
>
> ```c++
> string filename = "test.txt";
> 
> // Write to File
> ofstream fout(filename.c_str());  // default mode is ios::out | ios::trunc
> fout << "apple" << endl;
> fout << "orange" << endl;
> fout << "banana" << endl;
> 
> // Read from file
> ifstream fin(filename.c_str());  // default mode ios::in
> char ch;
> while (fin.get(ch)) {  // till end-of-file
>     cout << ch;
> }
> ```



### Binary file, read() and write()

We need to use `read()` and `write()` member functions for binary file (file mode of `ios::binary`), which read/write raw bytes without interpreting the bytes.

```c++
int main() {
   string filename = "test.bin";
 
   // Write to File
   ofstream fout(filename.c_str(), ios::out | ios::binary);
   if (!fout.is_open()) {
      cerr << "error: open file for output failed!" << endl;
      abort();
   }
   int i = 1234;
   double d = 12.34;
   fout.write((char *)&i, sizeof(int));
   fout.write((char *)&d, sizeof(double));
   fout.close();
 
   // Read from file
   ifstream fin(filename.c_str(), ios::in | ios::binary);
   if (!fin.is_open()) {
      cerr << "error: open file for input failed!" << endl;
      abort();
   }
   int i_in;
   double d_in;
   fin.read((char *)&i_in, sizeof(int));
   cout << i_in << endl;
   fin.read((char *)&d_in, sizeof(double));
   cout << d_in << endl;
   fin.close();
   return 0;
}
```



## String Streams



### `ostringstream` and `istringstream`

String streams act like a **string buffer**.

Input string stream can be used to validate input data; output string stream can be used to format the output.

```c++
// construct output string stream (buffer) - need <sstream> header
ostringstream sout;  
    
// Write into string buffer
sout << "apple" << endl;
sout << "orange" << endl;
sout << "banana" << endl;
 
// Get contents
cout << sout.str() << endl;

// construct input string stream (buffer) - need <sstream> header
istringstream sin("123 12.34 hello");
 
// Read from buffer
int i;
double d;
string s;
sin >> i >> d >> s;
cout << i << "," << d << "," << s << endl;
```

