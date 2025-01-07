

[toc]



# Object-Oriented Programming (OOP) in C++

> https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp3_OOP.html
>
> https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp5_OOPExamples.html



#### "const" Member Functions

A const member function, identified by a const keyword at the end of the member function's header, **cannot modifies any data member** of this object. For example,

```C++
double getRadius() const {  // const member function
   radius = 0;  
      // error: assignment of data-member 'Circle::radius' in read-only structure
   return radius;
}
```



#### Constructor's Member Initializer List

```C++
Circle(double r = 1.0, string c = "red") : radius(r), color(c) { }
```

Object data member shall be constructed via the member initializer list, not in the body.

```c++
Book::Book(string name, Author author, double price, int qtyInStock) : name(name), author(author) {
   setPrice(price);
   setQtyInStock(qtyInStock);
}
```



#### Copy Constructor

The default copy constructor performs *shadow copy*. It does **not** copy the dynamically allocated data members created via `new` or `new[]` operator.

```c++
class MyClass {
private:
   T1 member1;
   T2 member2;
public:
   // The default copy constructor which constructs an object via memberwise copy
   MyClass(const MyClass & rhs) {
      member1 = rhs.member1;
      member2 = rhs.member2;
   }
......
}
```

```c++
// Default copy constructor of Author class provided by C++
Author::Author(const Author& other)
      : name(other.name), email(other.email), gender(other.gender) { }  // memberwise copy
```

* **Copy Assignment Operator**

The default copy assignment operator performs *shadow copy*. It does **not** copy the dynamically allocated data members created via `new` or `new[]` operator.

> [!CAUTION]
>
> The **copy constructor**, instead of copy assignment operator, is used in declaration:
>
> ```c++
> Circle c8 = c6;  // Invoke the copy constructor, NOT copy assignment operator
>                  // Same as Circle c8(c6)
> ```

The copy assignment operator has the following signature:

```c++
class MyClass {
private:
   T1 member1;
   T2 member2;
public:
   // The default copy assignment operator which assigns an object via memberwise copy
   MyClass & operator=(const MyClass & rhs) {
      member1 = rhs.member1;
      member2 = rhs.member2;
      return *this;
   }
......
}
```

You could **overload** the assignment operator to override the default.

> Why do we use `const classname &`?

The pass-by-reference prevents the large copy cost by pass-by-value, while keeping the values of the members in `rhs` unchanged.

> [!IMPORTANT]
>
> The default copy constructor and assignment operator are both **shadow copy**. 
>
> If you use `new` (or `new[]`) to dynamically allocate memory in the constructor to object data member pointers, you should define a copy constructor and assignment operator that **deep copies** one object into another.

* **Example**

```c++
class ClassName {
private:
   T * pObj;   // object data member pointer
public:
   // Constructors
   ClassName(...) {
      pObj = new T(...); // or new[]
      ....
   }
   // Destructor
   ~ClassName() {
      delete pObj;      // OR delete[]
   }
   // Copy constructor
   ClassName & ClassName(const ClassName &);
 
   // Overload Assignment Operator
   ClassName & operator=(const ClassName &);   
......
}
```



#### Exception Handling

```c++
void Time::setHour(int h)
   if (h >= 0 && h <= 23) {
      hour = h;
   } else {
      throw invalid_argument("Invalid hour! Hour shall be 0-23.");
   }
}
```

TestTime.cpp:

```c++
/* Test Driver for the Time class (TestTime.cpp) */
#include <iostream>
#include <stdexcept>  // Needed for exception handling
#include "Time.h"
using namespace std;
 
int main() {
   //   Time t2(25, 0, 0); // program terminates abruptly
   //   t2.print();        // The rest of program will not be run
 
   // Graceful handling of exception
   try {
      Time t1(25, 0, 0); // Skip the remaining statements in try-clause an jump to catch-clause if an exception is thrown
      t1.print();
      // Continue to the next statement after try-catch, if there is no exception
   } catch (invalid_argument& ex) {  // need <stdexcept>
      cout << "Exception: " << ex.what() << endl;
         // Continue to the next statement after try-catch
   }
   cout << "Next statement after try-catch" << endl;
}
```



#### `static` keyword in class

`Date.h`:

```c++
class Date {
private:
   int year;    // 1753-9999
   int month;   // 1-12
   int day;     // 1-31
   const static string STR_MONTHS[];
   const static string STR_DAYS[];
   const static int DAYS_IN_MONTHS[];
   const static int YEAR_MIN = 1753;
   const static int YEAR_MAX = 9999;

public:
   static bool isLeapYear(int y);
   static bool isValidDate(int y, int m, int d);
   static int getDayOfWeek(int y, int m, int d);
   ...
}
```

`Date.c`:

Static variable should be initialized outside the class declaration.

Static members(data and functions) are owned by the class, not the objects(instances).

A `static` function can only access `static` variables, and cannot access non-`static` variables. 

```c++
// Initialize static non-integer variable (must be done outside the class declaration)
const string Date::STR_MONTHS[] = {"Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"};

const int Date::DAYS_IN_MONTHS[] = {31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};

const string Date::STR_DAYS[] = {"Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"};

// A static function that returns true if the given year is a leap year
bool Date::isLeapYear(int year) {
   return ((year % 4 == 0 && year % 100 != 0) || (year % 400 == 0));
}
 
// A static function that returns true if the given y, m, d constitutes a valid date
bool Date::isValidDate(int y, int m, int d) {
   if (y >= YEAR_MIN && y <= YEAR_MAX && m >= 1 && m <= 12) {
      int lastDayOfMonth = DAYS_IN_MONTHS[m-1];
      if (m == 2 && isLeapYear(y)) {
         lastDayOfMonth = 29;
      }
      return (d >= 1 && d <= lastDayOfMonth);
   } else {
      return false;
   }
}
 
// A static function that returns the day of the week (0:Sun, 6:Sat) for the given date
// Wiki "Determination of the day of the week" for the algorithm
int Date::getDayOfWeek(int y, int m, int d) {
   int centuryTable[] = {4, 2, 0, 6, 4, 2, 0, 6}; // 17xx, 18xx, ...
   int MonthTable[] = {0, 3, 3, 6, 1, 4, 6, 2, 5, 0, 3, 5};
   int MonthLeapYearTable[] = {6, 2, 3, 6, 1, 4, 6, 2, 5, 0, 3, 5};
 
   int century = y / 100;
   int twoDigitYear = y % 100;
   int centuryTableIndex = (century - 17) % 8;
   // Date before 17xx are not valid, but needed to prevent negative index
   if (centuryTableIndex < 0) {
      centuryTableIndex += 8;
   }
   int sum = centuryTable[centuryTableIndex] + twoDigitYear + twoDigitYear / 4;
   if (isLeapYear(y)) {
      sum += MonthLeapYearTable[m-1];
   } else {
      sum += MonthTable[m-1];
   }
   sum += d;
   return sum % 7;
}
```



