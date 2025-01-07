# Operator Overloading

> https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp7_OperatorOverloading.html



#### User-defined Operator Overloading

operator function:

```c++
return-type operatorΔ(parameter-list)
```

Example:
```c++
// Member function overloading '+' operator
const Point Point::operator+(const Point & rhs) const {
   return Point(x + rhs.x, y + rhs.y);
   // Return by value as local variable cannot be returned by reference
}
```

Usage:

`p1 + p2 = p1.operator+(p2)`

```c++
// Invoke via usual dot syntax, same as p1+p2
Point p4 = p1.operator+(p2);
p4.print();  // (5,7)

// Chaining
Point p5 = p1 + p2 + p3 + p4;
p5.print();  // (15,21)
```



#### Overloading Operator via "friend" non-member function

> Why can't we always use Member Function for Operator Overloading?

You cannot use member function to overload an operator if the **left operand is not an object** of that particular class.

Use non-member `friend` function!

Example:

> [!NOTE]
>
> Return type of the operator <<(>>) function is the reference of the stream object.

```c++
// Class Declaration
class Point {
private:
   int x, y;
 
public:
   Point(int x = 0, int y = 0);
   int getX() const; // Getters
   int getY() const;
   void setX(int x); // Setters
   void setY(int y);
 
   friend std::ostream & operator<<(std::ostream & out, const Point & point);
   friend std::istream & operator>>(std::istream & in, Point & point);
};

ostream & operator<<(ostream & out, const Point & point) {
   out << "(" << point.x << "," << point.y << ")";  // access private data
   return out;
}
 
istream & operator>>(istream & in, Point & point) {
   cout << "Enter x and y coord: ";
   in >> point.x >> point.y;  // access private data
   return in;
}
```

Usage:

`cout << p1` = `operator<<(cout, p1)`

```c++
int main() {
   Point p1(1, 2), p2;
 
   // Using overloaded operator <<
   cout << p1 << endl;    // support cascading
   operator<<(cout, p1);  // same as cout << p1
   cout << endl;
 
   // Using overloaded operator >>
   cin >> p1;
   cout << p1 << endl;
   operator>>(cin, p1);  // same as cin >> p1
   cout << p1 << endl;
   cin >> p1 >> p2;      // support cascading
   cout << p1 << endl;
   cout << p2 << endl;
}
```

The overloaded >> and << can also be used for file input/output, as the file IO stream `ifstream`/`ofstream` (in `fstream` header) is a subclass of `istream`/`ostream`.

```c++
#include <fstream>
#include "Point.h"
using namespace std;
 
int main() {
   Point p1(1, 2);
 
   ofstream fout("out.txt");
   fout << p1 << endl;
 
   ifstream fin("in.txt"); // contains "3 4"
   fin >> p1;
   cout << p1 << endl;
}
```

> How to overload unary postfix operator?

Introduce a "dummy" argument!

Example:

```c++
class Counter {
private:
   int count;
public:
   Counter(int count = 0);   // Constructor
   int getCount() const;     // Getters
   void setCount(int count); // Setters
   Counter & operator++();              // ++prefix
   const Counter operator++(int dummy); // postfix++
 
   friend std::ostream & operator<<(std::ostream & out, const Counter & counter);
};

// ++prefix, return reference of this
Counter & Counter::operator++() {
   ++count;
   return *this;
}
 
// postfix++, return old value by value
const Counter Counter::operator++(int dummy) {
   Counter old(*this);
   ++count;
   return old;
}
 
// Overload stream insertion << operator
ostream & operator<<(ostream & out, const Counter & counter) {
   out << counter.count;
   return out;
}
```

> [!NOTE]
>
> The prefix function returns a reference to this instance, to support chaining (or cascading), e.g., `++++c` as `++(++c)`. 
>
> The postfix function returns a `const` object(a temporary object) by value.



#### Implicit Conversion via Single-argument Constructor & Keyword "explicit"

A ***single-argument* constructor** can be used to implicitly convert a value to an object.

Example:

```c++
class Counter {
private:
   int count;
public:
   Counter(int c = 0) : count(c) { }
         // A single-argument Constructor which takes an int
         // It can be used to implicitly convert an int to a Counter object
   int getCount() const { return count; }    // Getter
   void setCount(int c) { count = c; } // Setter
};
 
int main() {
   Counter c1; // Declare an instance and invoke default constructor
   cout << c1.getCount() << endl;  // 0
 
   c1 = 9;
     // Implicit conversion
     // Invoke single-argument constructor Counter(9) to construct a temporary object.
     // Then copy into c1 via memberwise assignment.
   cout << c1.getCount() << endl;  // 9
}
```

To disable implicit conversion, use keyword "`explicit`". Nonetheless, you can still perform **explicit conversion via type cast operator**. 

> [!NOTE]
>
> Although implicit conversion is disabled using keyword "explicit", we can still use explicit conversion via type casting.
>
> ```c++
> c1 = (Counter)9;  // Explicit conversion via type casting
> ```

```c++
class Counter {
private:
   int count;
public:
   explicit Counter(int c = 0) : count(c) { }
      // Single-argument Constructor
      // Use keyword "explicit" to disable implicit automatic conversion in assignment
   int getCount() const { return count; }    // Getter
   void setCount(int c) { count = c; } // Setter
};
 
int main() {
   Counter c1; // Declare an instance and invoke default constructor
   cout << c1.getCount() << endl;  // 0
 
// Counter c2 = 9;
     // error: conversion from 'int' to non-scalar type 'Counter' requested
 
   c1 = (Counter)9;  // Explicit conversion via type casting operator
   cout << c1.getCount() << endl;  // 9
}
```



#### Dynamic Memory Allocation in Object

> [!CAUTION]
>
> If you dynamically allocate memory in the constructor, you need to provide your own **destructor, copy constructor and assignment operator** to manage the dynamically allocated memory. The defaults provided by the C++ compiler do not work for dynamic memory.

Example:
```c++
class MyDynamicArray {
private:
   int size_;   // size of array
   double * ptr;  // pointer to the elements
 
public:
   explicit MyDynamicArray (int n = 8);         // Default constructor
   explicit MyDynamicArray (const MyDynamicArray & a); // Copy constructor
   MyDynamicArray (const double a[], int n);    // Construct from double[]
   ~MyDynamicArray();                           // Destructor
 
   const MyDynamicArray & operator= (const MyDynamicArray & rhs); // Assignment a1 = a2
   bool operator== (const MyDynamicArray & rhs) const;     // a1 == a2
   bool operator!= (const MyDynamicArray & rhs) const;     // a1 != a2
 
   double operator[] (int index) const;  // a[i]
   double & operator[] (int index);      // a[i] = x
 
   int size() const { return size_; }    // return size of array
 
   // friends
   friend std::ostream & operator<< (std::ostream & out, const MyDynamicArray & a); // out << a
   friend std::istream & operator>> (std::istream & in, MyDynamicArray & a);        // in >> a
};
```

