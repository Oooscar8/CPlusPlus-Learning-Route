

[toc]

# Miscellaneous, Tips and Traps

---

> https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp11_TipsTraps.html



## Exception Handling



### throw, try and catch

Suppose that we have a class called `PositiveInteger`, which maintains a data member `value` containing a positive integer.

`PositiveInteger.cpp`

```c++
// Setter with input validation
void PositiveInteger::setValue(int v) {
   if (v > 0) {
      value = v;
   } else {
      throw invalid_argument("value shall be more than 0.");
            // need <stdexcept>
   }
}
```

`TestPositiveInteger.cpp`:

```c++
// Graceful handling of exception with try-catch
try {
    cout << "begin try 1..." << endl;
    PositiveInteger i3(-8);
    // Exception thrown.
    // Skip the remaining statements in try and jump to catch.
    cout << i3.getValue() << endl;
    cout << "end try 1..." << endl;
    // Continue to the next statement after try-catch, if there is no exception
} catch (invalid_argument & ex) {  // need <stdexcept>
    cout << "Exception: " << ex.what() << endl;
    // Continue to the next statement after try-catch
}
cout << "after try-catch 1..." << endl;
```



### Class `exception` and its subclasses

![image-20250109125259078](https://gitee.com/OooAlex/study_note/raw/master/img/202501091252319.png)



### Creating Your Own exception subclass

You can create your own exception by subclassing `exception` or its subclasses (such as `logic_error` or `runtime_error`). For example,

`MyException.h`:

```c++
/* Header for the MyException class (MyException.h) */
#ifndef MY_EXCEPTION_H
#define MY_EXCEPTION_H

#include <stdexcept>

class MyException : public std::logic_error {
public:
   // Constructor
   MyException() : std::logic_error("my custom error") { };
};
 
#endif
```

> [!NOTE]
>
> Provide a constructor with a custom what-message.

`TestMyException.cpp`:

```c++
void fun() {
   throw MyException();
}
 
int main() {
   try {
      fun();
   } catch (MyException & ex) {
      cout << ex.what() << endl;
   }
}
```



## Storage Duration, Scopes and Linkages

The *duration* determines when the variable is created and destroyed; 

The *Scope* determines which part of the program can access the variable; 

The *linkage* determines whether the variable is available in other source files.

<img src="https://gitee.com/OooAlex/study_note/raw/master/img/202501091438580.png" alt="image-20250109143813479" style="zoom:150%;" />



### Automatic Local Variables ("auto" Specifier)

Local variables have default type `auto`.



### Static Variables ("static" Specifier)

Static variables has three types of linkage:

1. external: global static variables **visible in other source files** - defined outside all functions **without keyword `static`**.
2. internal: global static file-scope variables **visible in the file** that it is defined - defined outside all functions **with keyword `static`**.
3. no linkage: local static variable visible within a function or block for which it is defined - defined inside a function **with keyword `static`**.

* static class members

A `static` class member (data or function) belongs to the class, instead of instances. It can be **referenced directly from the class**, without creating instances, via `*Classname*::*staticMemberName*`. A `static` data member retains its value throughout the program execution.

> [!NOTE]
>
> * Summary of `static` Keyword
>
> A `static` variable defined inside a block has block-scope, but having duration for the entire program. It retains its memory and value across multiple invocations.
>
> A `static` global variable defined outside all functions has file-scope with internal linkage (i.e., visible only to the file in which it is defined, but not other source files). It has duration for the entire program, and retains its memory and value throughout the program execution. On the other hand, a global variable without the static keyword has file-scope with external linkage. It can be referenced by other file via the `extern` keyword.
>
> A `static` class member belongs to the class, instead of the instances. There is one copy shared by all the instances. It has class scope. To reference it outside the class, use the scope resolution operator `*classname*::*static_membername*`.



### External Variables ("extern" Specifier)

The `extern` specifies linkage to another source file. It tells the compiler that the identifier is defined in another (external) source file.

```c++
// File1.cpp
extern int globalVar;  // Declare that this variable is defined in another file (external variable).
                       // Cannot assign an value.
                       // Need to link to the other file.
```

```c++
// File2.cpp
int globalVar = 88;         // Definition here
// or
extern int globalVar = 88;  // The "extern" specifier is optional.
                            // The initialization indicates definition
```



2.5 CV-Qualifiers (const and volatile) and mutable

The "`const`" qualifier indicates that the content of the storage location shall not be changed after initialized.

The "volatile" qualifier indicates that the content of the storage location could be altered outside your program, e.g., by an external hardware. This qualifier is needed to tell compiler not to optimize this particular location (e.g., not to store in register, not to re-order the statement, or collapse multiple statements).

The `mutable` specifier can be used in `struct` or `class` to indicate that a particular data member is **modifiable even though the instance is declared `const`**.

> [!CAUTION]
>
> * "const" Global Variables
>
> By default, a global variable (defined outside all functions) has external linkage. However, `const` global variable has internal linkage (as if `static` specifier is used). As the result, you can place all `const` global variables in a header file, and include the header in all the source files. To set a `const` global variable to external linkage, include "`extern`" specifier.



## Type Casting Operators

C++ introduces 4 new type casting operators: `const_cast<new-type>(value)`, `static_cast<new-type>(value)`, `dynamic_cast<new-type>(value)`, `reinterpret_cast<new-type>(value)` to regulate type casting. 

> [!TIP]
>
> C++ supports C's explicit type casting operations `(new-type)value` (C-style cast), or `new-type(value)` (Function-style cast), called *regular cast*. 
>
> Although the old styles is still acceptable in C++, new styles are preferable.

* `static_cast`

`static_cast` is used for force implicit conversion. It throws a type-cast error if the conversion fails.

Can be used to convert values of various fundamental types, e.g., from `double` to `int`, from `float` to `long`.

* `dynamic_cast`

`dynamic_cast` can be used to verify the type of an object at runtime, before performing the type conversion. It is primarily used to perform "safe downcasting".

```c++
dynamic_cast<Type *>(ptr)
```

```c++
Base* basePtr = new Derived();
Derived* derivedPtr = dynamic_cast<Derived*>(basePtr);
```

```c++
class Shape {
    virtual void draw() = 0;
};

class Circle : public Shape {
    void draw() override {}
    void radius() {}  // Circle specific method
};

void processShape(Shape* shape) {
    if (Circle* circle = dynamic_cast<Circle*>(shape)) {
        circle->radius();
    }
}
```

* `const_cast`

The `const_cast` can be used to drop the `const` label, so as to alter its contents (i.e., cast away the `const`-ness or `volatile`-ness). This is useful if you have a variable which is constant most of the time, but need to be changed in some circumstances. You can declare the variable as `const`, and use `const_cast` to alter its value. The syntax is:

```
const_cast<Type>(expression)
```

`const_cast` cannot change the type of an object.

* `reinterpret_cast`

Used for low-level casts that yield implementation-dependent result, e.g., casting a pointer to an `int`.

