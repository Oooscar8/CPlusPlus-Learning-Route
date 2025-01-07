[toc]

# Template and Generic Programming

> https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp8_Template.html



#### STL's vector Template Class

```c++
vector<int> v1(5);  // Create a vector with 5 elements;
v1[i];	// no index-bound check for []
v1.at(i);	// do index-bound check with at()

vector<int> v2;   // Create a vector with 0 elements
// Assign v1 to v2 memberwise
v2 = v1;

// Compare 2 vectors memberwise
cout << boolalpha << (v1 == v2) << endl;

// Append more elements - dynamically allocate memory
v1.push_back(80);
v1.push_back(81);

// Remove element from the end
v1.pop_back();
```



#### Function Template

The syntax of defining function template is:

```c++
template <typename T> OR template <class T>
return-type function-name(function-parameter-list) { ...... }
```

* **Explicit Specialization**

```c++
template <typename T>
void mySwap(T &a, T &b);  // Template
 
template <>
void mySwap<int>(int &a, int &b);
   // Explicit Specialization for type int

template <typename T>
void mySwap(T &a, T &b) {
   cout << "Template" << endl;
   T temp;
   temp = a;
   a = b;
   b = temp;
}

template <>
void mySwap<int>(int &a, int &b) {
   cout << "Specialization" << endl;
   int temp;
   temp = a;
   a = b;
   b = temp;
}
```

> [!CAUTION]
>
> If there is any **non-template definition** that matches the function call. The **non-template version** will take precedence over **explicit specialization**, then **template**.



#### Class Template

The syntax for defining a *class template*:

```c++
template <class T>    // OR template <typename T>
class ClassName {
   ......
}
```

Example:

```c++
template <typename T>
class Number {
private:
   T value;
public:
   Number(T value) { this->value = value; };
   T getValue() { return value; }
   void setValue(T value) { this->value = value; };
};
 
int main() {
   Number<int> i(55);
   ...
}
```

> [!CAUTION]
>
> * **Separating Template Function Declaration and Definition**
>
> If you separate the function implementation, you need to use keyword `template <typename T>` on each of the function implementation. For example,
>
> ```c++
> template <typename T>
> T Number<T>::getValue() {
>    return value;
> }
> ```

* More than one Type Parameters

```c++
template <typename T1, typename T2, ....>
class ClassName { ...... }
```

* Default Type

```c++
template <typename T = int>
class ClassName { ...... }
```

To instantiate with the default type, use `ClassName<>`.

* Specialization

```c++
// General Template
template <typename T>
class Complex { ...... }
 
// Specialization for type double
template <>
class Complex<double> { ...... }
 
// Specialization for type int
template <>
class Complex<int> { ....... }
```



#### Example: `MyComplex` Template Class

`MyComplex.h`:

```c++
/*
 * The MyComplex template class header (MyComplex.h)
 * All template codes are kept in the header, to be included in program
 * (Follow, modified and simplified from GNU GCC complex template class.)
 */
#ifndef MY_COMPLEX_H
#define MY_COMPLEX_H

#include <iostream>
 
// Forward declaration
template <typename T> class MyComplex;
 
template <typename T>
std::ostream & operator<< (std::ostream & out, const MyComplex<T> & c);
template <typename T>
std::istream & operator>> (std::istream & in, MyComplex<T> & c);
 
// MyComplex template class declaration
template <typename T>
class MyComplex {
private:
   T real, imag;
 
public:
   // Constructor
   explicit MyComplex<T> (T real = 0, T imag = 0)
         : real(real), imag(imag) { }
 
   // Overload += operator for c1 += c2
   MyComplex<T> & operator+= (const MyComplex<T> & rhs) {
      real += rhs.real;
      imag += rhs.imag;
      return *this;
   }
 
   // Overload += operator for c1 += value
   MyComplex<T> & operator+= (T value) {
      real += value;
      return *this;
   }
 
   // Overload comparison == operator for c1 == c2
   bool operator== (const MyComplex<T> & rhs) const {
      return (real == rhs.real && imag == rhs.imag);
   }
 
   // Overload comparison != operator for c1 != c2
   bool operator!= (const MyComplex<T> & rhs) const {
      return !(*this == rhs);
   }
 
   // Overload prefix increment operator ++c
   // (Separate implementation for illustration)
   MyComplex<T> & operator++ ();
 
   // Overload postfix increment operator c++
   const MyComplex<T> operator++ (int dummy);
 
   /* friends */
 
   // (Separate implementation for illustration)
   friend std::ostream & operator<< <>(std::ostream & out, const MyComplex<T> & c); // out << c
   friend std::istream & operator>> <>(std::istream & in, MyComplex<T> & c);        // in >> c
 
   // Overloading + operator for c1 + c2
   // (inline implementation for illustration)
   friend const MyComplex<T> operator+ (const MyComplex<T> & lhs, const MyComplex<T> & rhs) {
      MyComplex<T> result(lhs);
      result += rhs;  // uses overload +=
      return result;
   }
 
   // Overloading + operator for c + double
   friend const MyComplex<T> operator+ (const MyComplex<T> & lhs, T value) {
      MyComplex<T> result(lhs);
      result += value;  // uses overload +=
      return result;
   }
 
   // Overloading + operator for double + c
   friend const MyComplex<T> operator+ (T value, const MyComplex<T> & rhs) {
      return rhs + value;   // swap and use above function
   }
};
 
// Overload prefix increment operator ++c
template <typename T>
MyComplex<T> & MyComplex<T>::operator++ () {
  ++real;   // increment real part only
  return *this;
}
 
// Overload postfix increment operator c++
template <typename T>
const MyComplex<T> MyComplex<T>::operator++ (int dummy) {
   MyComplex<T> saved(*this);
   ++real;  // increment real part only
   return saved;
}
 
/* Definition of friend functions */
 
// Overload stream insertion operator out << c (friend)
template <typename T>
std::ostream & operator<< (std::ostream & out, const MyComplex<T> & c) {
   out << '(' << c.real << ',' << c.imag << ')';
   return out;
}
 
// Overload stream extraction operator in >> c (friend)
template <typename T>
std::istream & operator>> (std::istream & in, MyComplex<T> & c) {
   T inReal, inImag;
   char inChar;
   bool validInput = false;
   // Input shall be in the format "(real,imag)"
   in >> inChar;
   if (inChar == '(') {
      in >> inReal >> inChar;
      if (inChar == ',') {
         in >> inImag >> inChar;
         if (inChar == ')') {
            c = MyComplex<T>(inReal, inImag);
            validInput = true;
         }
      }
   }
   if (!validInput) in.setstate(std::ios_base::failbit);
   return in;
}
 
#endif
```

> [!NOTE]
>
> > Why do we need a `<>` when we declare operator function <</>> inside the class? When do we need a forward declaration of a friend function?
>
> When the compiler sees a declaration with <>, it knows this is a **specialization version of a template function**. The compiler will then **look for the corresponding forward declaration of the template function**. This is why we also need a forward declaration of the operator function <</>>: this is a template function.
>
> The declaration without <> simply tells the compiler this is an ordinary non-template function, which does not need a forward declaration.
>
> ```c++
> // Forward declaration
> template <typename T> class MyComplex;
>  
> template <typename T>
> std::ostream & operator<< (std::ostream & out, const MyComplex<T> & c);
> 
> template <typename T>
> class MyComplex {
> private:
>     T real, imag;
> 
> public:
>     // Situation1: using <>
>     friend std::ostream& operator<< <>(std::ostream& out, const MyComplex<T>& c);
> 
>     // Situation2: not using <>
>     friend void printComplex(const MyComplex<T>& c);
> };
> 
> /* Definition of friend functions */
>  
> // Overload stream insertion operator out << c (friend)
> template <typename T>
> std::ostream & operator<< (std::ostream & out, const MyComplex<T> & c) {
>    out << '(' << c.real << ',' << c.imag << ')';
>    return out;
> }
> ```
>
> > What if a member function of a template class is defined outside the class?
>
> When a member function of a template class is **defined outside the class**, we need to use keyword `template <typename T>` on the function implementation. 
>
> For example:
>
> ```c++
> // Declared inside the template class MyComplex
> // Defined outside the class
> // Overload prefix increment operator ++c
> template <typename T>
> MyComplex<T> & MyComplex<T>::operator++ () {
>   ++real;   // increment real part only
>   return *this;
> }
> ```

