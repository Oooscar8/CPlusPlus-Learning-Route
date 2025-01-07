[toc]

# Characters and Strings

> https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp9_String.html



#### Strings: The C-String and the string class

* **C-String Literals**

> [!WARNING]
>
> ```c++
> char * str1 = "hello";
>           // warning: deprecated conversion from string constant to 'char*'
>    char * str2 = const_cast<char *>("hello");   // remove the "const"
> ```
>
> Those two statements are dangerous. String literal "hello" is stored in **read-only memory**. Now you assign a non-const pointer to point to this area. If you try to modify this string literal, it will crash your program. 

> [!TIP]
>
> ```c++
> const char * str3 = "hello";
> // *(str3 + 1) = 'a';  // error: assignment of read-only location '*(str3 + 1u)'
> 
> // Creates a modifiable copy
> char str4[] = "hello";
> str4[1] = 'a';
> 
> const char str5[] = "hello";
> //  str5[1] = 'a';   // error: assignment of read-only location 'str5[1]'
> ```
>
> Those are correct! 

* **C++'s string class**

See [NTU Notes](https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp9_String.html) for details.



#### The C-String Input Methods

C-string has three input methods: stream extraction operator (`>>`), `getline()` and `get()`.

> [!NOTE]
>
> > What's the difference between those three methods?
>
> * `istream's` Overloaded Stream Extraction Operator (>>)
>
> Stream extraction operator does not discard the trailing whitespace(blank, tab, newline), and leave it in the input buffer.
>
> ```c++
> const int SIZE = 5;
> char str[SIZE];    // max strlen is SIZE - 1
> 
> cout << "Enter a word: ";
> // Extract SIZE-1 characters at most
> cin >> setw(SIZE) >> str;   // need <iomanip> header
> cout << str << endl;
> ```
>
> You may need to flush the input buffer:
>
> ```c++
> // Ignore to the end-of-file
> cin.ignore(numeric_limits<streamsize>::max());  // need header <limits>
> 
> // Ignore to the end-of-line
> cin.ignore(numeric_limits<streamsize>::max(), '\n');
> ```
>
> * `istream::getline()`
>
> The delimiter, if found, is extracted and discarded from input stream.
>
> If `n-1` character is read and the next character is not delimit, then the `failbit` (of the `istream`) is set. You can check the `failbit` via `bool` function `cin.fail()`. You need to **clear the error bits before issuing another `getline()` call**.
>
> ```c++
> const int SIZE = 5;
> char str[SIZE];    // max strlen is SIZE - 1
>  
> cout << "Enter a line: ";
> cin.getline(str, SIZE);     // Read a line (including whitespaces) until newline, discard newline
> cout << "\"" << str << "\" length=" << strlen(str) << endl;
> cout << "Number of characters extracted: " << cin.gcount() << endl;
>  
> if (cin.fail()) {
>    cout << "failbit is set!" << endl;
>    cin.clear();   // Clear all error bits. Otherwise, subsequent getline() fails
> }
> ```
>
> * `istream::get()`
>
> `get(str, n, delim)` is similar to `getline(str, n, delim)`, except that the `delim` character is not extracted and remains in the input stream.

