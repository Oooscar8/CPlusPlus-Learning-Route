[toc]



# Inheritance and Polymophism

> https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp6_Inheritance.html



#### Inheritance

```c++
class SubclassName : inheritance-access-specifier SuperclassName {
   ......
};
```

* **Access Specifier:** 

A `private` member is accessible within the **class** by member functions and by ***friends* of that class**. 

A `public` member is accessible by all. 

A `protected` member can be accessed by **itself and its friend**, as well as its **subclasses and their friends**.

* **Inheritance Access Specifier:**

`public`-inheritance(most common): `public` members in the superclass becomes `public` members in the derived class; `protected` members in the base class become `protected` member in the derived class.

`protected`-inheritance: `public` and `protected` members in the base class become `protected` members in the derived class. 

`private`-inheritance: `public` and `protected` members in the base class become `private` member in the derived class.

> [!NOTE]
>
> When the subclass construct its instance, it must first construct a superclass object, which it inherited.



#### Polymorphism

> One interface, multiple implementations. Use virtual functions.

> [!NOTE]
>
> Polymorphism works on **object pointers** and **references** using so-called **dynamic binding at run-time**. 
>
> Does not work on regular objects, which uses **static binding** during the compile-time:
>
> ```c++
> // Using object with explicit constructor
>    Point p3 = MovablePoint(31, 32, 33, 34);  // upcast
>    p3.print();     // Point @ (31,32) - Run superclass version!!
>    cout << endl;
> ```
>
> **Virtual Functions:** To implement polymorphism, we need to use the keyword `virtual` for functions that are meant to be polymorphic.
>
> ```c++
> // Point.h
> class Point {
>    ......
>    virtual void print() const;
> }
> ```
>
> It is recommended that functions to be **overridden** in the subclass be declared `virtual` in the superclass.
>
> Constructor can't be `virtual`, because it is not inherited. Subclass defines its own constructor, which **invokes the superclass constructor** to initialize the inherited data members.
>
> Destructor should be declared virtual, if a class is to be used as a superclass, so that the appropriate object destructor is invoked to free the dynamically allocated memory in the subclass, if any.
>
> If you override function in the subclass, the overridden function shall have the **same parameter list** as the superclass' version.

* **Pure Virtual Function**

```c++
virtual double getArea() = 0;  // Pure virtual function, to be implemented by subclass
```

The `=0` simply make the class *abstract*. As the result, you cannot create instances.

> [!CAUTION]
>
> const object cannot invoke non-const member function
>
> const data member can only be initialized by the constructor using *member initializer list*



#### `friend` function and `friend` class

* **friend function**

Friend functions are declared inside the class and are implemented outside the class.

Friend functions are not the members of the class, but their arguments of the class can access all class members.

Friend functions will not be inherited by the subclass. Friends can't be virtual, as friends are not class member.

```c++
class Point {
   // A friend function defined outside this class, but its argument of
   // this class can access all class members (including private members).
   friend void set(Point & point, int x, int y);  // prototype
private:
   int x, y;
public:
   Point(int x = 0, int y = 0) : x(x), y(y) { }
   void print() const { cout << "(" << x << "," << y << ")" << endl; }
};
 
// Friend function is defined outside the class
void set(Point & point, int x, int y) {
  point.x = x;  // can access private data x and y
  point.y = y;
}
```

* **friend class**

To declare all member functions of a class (says `Class1`) friend functions of another class (says `Class2`), declared "`friend class Class1;`" in `Class2`.

