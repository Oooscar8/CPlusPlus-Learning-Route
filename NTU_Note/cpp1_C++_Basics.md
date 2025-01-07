# C++ Basics

> https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp0_Introduction.html
>
> https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp1_Basics.html
>
> https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp2_MoreBasics.html
>
> https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp4_PointerReference.html#zz-1.7



#### IO Manipulators

Formatting Input/Output using IO Manipulators (Header <iomanip>)

```c++
#include <iomanip>    // Needed to do formatted I/O
```



#### Multi-Dimensional Array

For 2D array (table), the first index is the row number, second index is the column number. 

The elements are stored in a so-called ***row-major*** manner, where the column index runs out first.

![image-20241222090100542](https://gitee.com/OooAlex/study_note/raw/master/img/202412220901630.png)

Array is passed into function by reference. That is, the invoked function works on the same copy of the array as the caller.



#### Sorting Algorithm

1. Bubble sort

```c++
// Sort the given array of size
void bubbleSort(int a[], int size) {
   bool done = false; // terminate if no more swap thru a pass
   int temp;          // use for swapping
 
   while (!done) {
      done = true;
      // Pass thru the list, compare adjacent items and swap
      // them if they are in wrong order
      for (int i = 0; i < size - 1; ++i) {
         if (a[i] > a[i+1]) {
            print(a, size); // for tracing
            temp = a[i];
            a[i] = a[i+1];
            a[i+1] = temp;
            done = false;   // swap detected, one more pass
         }
      }
   }
}
```

2. Insertion sort

```c++
// Sort the given array of size using insertion sort
void insertionSort(int a[], int size) {
   int temp;   // for shifting elements
   for (int i = 1; i < size; ++i) {
      // For element at i, insert into proper position in [0, i-1]
      // which is already sorted.
      // Shift down the other elements
      for (int prev = 0; prev < i; ++prev) {
         if (a[i] < a[prev]) {
            // insert a[i] at prev, shift the elements down
            temp = a[i];
            for (int shift = i; shift > prev; --shift) {
               a[shift] = a[shift-1];
            }
            a[prev] = temp;
            break;
         }
      }
   }
}
```

3. Selection sort

```c++
// Sort the given array of size using selection sort
void selectionSort(int a[], int size) {
   int temp; // for swapping
   for (int i = 0; i < size - 1; ++i) {
      // [0, i-1] already sort
      // Search for the smallest element in [i, size-1]
      // and swap with a[i]
      int minIndex = i;  // assume fist element is the smallest
      for (int j = i + 1; j < size; ++j) {
         if (a[j] < a[minIndex]) minIndex = j;
      }
      if (minIndex != i) {  // swap
         temp = a[i];
         a[i] = a[minIndex];
         a[minIndex] = temp;
      }
   }
}
```



#### Namespace

To place an entity under a namespace, use keyword `namespace` as follow:

```c++
// create a namespace called myNamespace for the enclosed entities
namespace myNameSpace {  
   int foo;               // variable
   int f() { ...... };    // function
   class Bar { ...... };  // compound type such as class and struct
}
 
// To reference the entities, use
myNameSpace::foo
myNameSpace::f()
myNameSpace::Bar
```



#### Enumeration (enum)

```c++
enum Color {
   RED, GREEN, BLUE
} myColor;        // Define an enum and declare a variable of the enum
......
myColor = RED;    // Assign a value to an enum
Color yourColor;
yourColor = GREEN;
```



#### Ellipses (...)

Ellipses (`...`) can be used as the *last* parameter of a function to denote zero or more arguments of unknown type.

```c++
/*
 *  TestEllipses.cpp
 */
#include <iostream>
#include <cstdarg>
using namespace std;
 
int sum(int, ...);
 
int main() {
   cout << sum(3, 1, 2, 3) << endl;       // 6
   cout << sum(5, 1, 2, 3, 4, 5) << endl; // 15
 
   return 0;
}
 
int sum(int count, ...) {
   int sum = 0;
 
   // Ellipses are accessed thru a va_list
   va_list lst;  // Declare a va_list
   // Use function va_start to initialize the va_list,
   // with the list name and the number of parameters.
   va_start(lst, count);
   for (int i = 0; i < count; ++i) {
      // Use function va_arg to read each parameter from va_list,
      // with the type.
      sum += va_arg(lst, int);
   }
   // Cleanup the va_list.
   va_end(lst);
 
   return sum;
}
```



#### Function Pointer

The syntax for declaring a function pointer:

```c++
// Function-pointer declaration
return-type (* function-ptr-name) (parameter-list)
 
// Examples
double (*fp)(int, int)  // fp points to a function that takes two ints and returns a double (function-pointer)
double *dp;          // dp points to a double (double-pointer)
double *fun(int, int)   // fun is a function that takes two ints and returns a double-pointer
    
double f(int, int);      // f is a function that takes two ints and returns a double
fp = f;            // Assign function f to fp function-pointer
```

Example:

```c++
/* Test Function Pointers (TestFunctionPointer.cpp) */
#include <iostream>
using namespace std;
 
int arithmetic(int, int, int (*)(int, int));
    // Take 3 arguments, 2 int's and a function pointer
    //   int (*)(int, int), which takes two int's and return an int
int add(int, int);
int sub(int, int);
 
int add(int n1, int n2) { return n1 + n2; }
int sub(int n1, int n2) { return n1 - n2; }
 
int arithmetic(int n1, int n2, int (*operation) (int, int)) {
   return (*operation)(n1, n2);
}
 
int main() {
   int number1 = 5, number2 = 6;
 
   // add
   cout << arithmetic(number1, number2, add) << endl;
   // subtract
   cout << arithmetic(number1, number2, sub) << endl;
}
```

