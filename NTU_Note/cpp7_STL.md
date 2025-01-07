# C++ Standard Libraries and Standard Template Library (STL)

> https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp9_STL.html
>
> The cplusplus.com at http://www.cplusplus.com/reference provides a comprehensive online references for the C++ libraries.



## STL



### Containers

* Sequence Containers, Associative Containers and Adapters

STL provides the following types of containers:

1. Sequence Containers: linear data structures of elements
   - `vector`: dynamically resizable array. 
   - `deque`: double-ended queue. 
   - `list`: double-linked list.
2. Associative Containers: nonlinear data structures storing key-value pairs
   - `set`: No duplicate element. Support fast lookup.
   - `multiset`: Duplicate element allowed. Support fast lookup.
   - `map`: One-to-one mapping (associative array) with no duplicate. Support fast key lookup.
   - `multimap`: One-to-many mapping, with duplicates allowed. Support fast key lookup.
3. Container Adapter Classes:
   - `Stack`: Last-in-first-out (LIFO) queue, adapted from `deque` (default), or `vector`, or `list`. Support operations `back`, `push_back`, `pop_back`.
   - `queue`: First-in-first-out (FIFO) queue, adapted from `deque` (default), or `list`. Support operations `front`, `back`, `push_back`, `pop_front`.
   - `priority_queue`: highest priority element at front of the queue. adapted from `vector` (default) or `deque`. Support operations `front`, `push_back`, `pop_front`.

> [!NOTE]
>
> * First-class Containers, Adapters and Near Containers
>
> The containers can also be classified as:
>
> 1. First-class Containers: all sequence containers and associative containers are collectively known as first-class container.
> 2. Adapters: constrained first-class containers such as `stack` and `queue`.
> 3. Near Containers: Do not support all the first-class container operations. For example, the built-in array (pointer-like), `bitsets` (for maintaining a set of flags), `valarray` (support array computation), `string` (stores only character type).



### STL Pre-defined Iterators (in Header <iterator>)

* Example: `istream_iterator` and `ostream_iterator`

```c++
int main() {
   // Construct ostream_iterators to write int and string to cout
   ostream_iterator<int> iterOut(cout);
   ostream_iterator<string> iterOutStr(cout);
 
   *iterOutStr = "Enter two integers: ";
 
   // Construct an istream_iterator<int> to read int from cin
   istream_iterator<int> iterIn(cin);
   int number1 = *iterIn;  // dereference to get the value
   ++iterIn;               // next int in cin
   int number2 = *iterIn;
 
   *iterOutStr = "You have entered ";
   *iterOut = number1;
   *iterOutStr = " and ";
   *iterOut = number2;
}
```

* Example: `copy()` to `ostream_iterator`

```c++
int main() {
   const int SIZE = 10;
   int array[SIZE] = {11, 55, 44, 33, 88, 99, 11, 22, 66, 77};
   vector<int> v(array, array + SIZE);
 
   // Construct an ostream_iterator called out
   ostream_iterator<int, char> out (cout, " ");
   // Copy to ostream, via ostream_iterator - replacing the print()
   copy(v.begin(), v.end(), out);
   cout << endl;
 
   // Copy to ostream in reverse order
   copy(v.rbegin(), v.rend(), out);
   cout << endl;
 
   // Use an anonymous ostream_iterator
   copy(v.begin(), v.end(), ostream_iterator<int, char>(cout, " "));
   cout << endl;
   return 0;
}
```

* Example: insert_iterator

A `front_insert_iterator` inserts from the front; a `back_insert_iterator` inserts at the end; an `insert_iterator` inserts before a given location.

```c++
int main() {
   int a1[5] = {11, 55, 44, 33, 88};
   vector<int> v(a1, a1+5);
   ostream_iterator<int, char> out (cout, " ");  // for printing
   copy(v.begin(), v.end(), out);
   cout << endl;
    
   // Construct a back_insert_iterator to insert at the end
   back_insert_iterator<vector<int> > back (v);
   int a2[3] = {91, 92, 93};
   copy(a2, a2+3, back);
   copy(v.begin(), v.end(), out);
   cout << endl;
 
   // Use an anonymous insert_iterator to insert at the front
   int a3[3] = {81, 82, 83};
   copy(a3, a3+3, insert_iterator<vector<int> >(v, v.begin()));
   copy(v.begin(), v.end(), out);
   cout << endl;
   return 0;
}
```



### Algorithm

* Example 1: sort(), reverse(), random_shuffle() and find() on Iterators [first,last)

```c++
int main() {
   const int SIZE = 10;
   int array[SIZE] = {11, 55, 44, 33, 88, 99, 11, 22, 66, 77};
   vector<int> v(array, array + SIZE);
   print(v);
 
   // Sort
   sort(v.begin(), v.end());  // entire container [begin(),end())
   print(v);
 
   // Reverse
   reverse(v.begin(), v.begin() + v.size()/2);  // First half
   print(v);
 
   // Random Shuffle
   random_shuffle(v.begin() + 1, v.end() - 1);  // exclude first and last elements
   print(v);
 
   // Search
   int searchFor = 55;
   vector<int>::iterator found = find(v.begin(), v.end(), searchFor);
   if (found != v.end()) {
      cout << "Found" << endl;
   }
   return 0;
}
```

> [!NOTE]
>
> Most of the algorithm functions takes at least two iterators: `first` and `last`, to specify the range `[first,last)` of operation.
>
> All STL containers provides members functions `begin()` and `end()`, which return the begin and pass-the-end elements of the container, respectively.
>
> To apply sort, the elements shall have overloaded the `'<'` operator, which is used for comparing the order of the elements.

* Example 2: for_each()

```c++
int main() {
   vector<int> v;
   v.push_back(11);
   v.push_back(3);
   v.push_back(4);
   v.push_back(22);
 
   // Invoke the given function (print, square)
   // for each element in the range
   for_each(v.begin(), v.end, print);
   for_each(v.begin() + 1, v.begin() + 3, square);
   for_each(v.begin(), v.end, print);
   return 0;
}
 
void square(int & n) { n *= n; }
 
void print(int & n) { cout << n << " "; }
```



### Function Object (Functors)

* transform() algorithm

`transform()` algorithm has two versions, supporting unary and binary operations, respectively.

```c++
// Unary Operation
template <class InputIterator, class OutputIterator, class UnaryOperation>
OutputIterator transform (InputIterator first, InputIterator last,
                         OutputIterator result, UnaryOperation op)
   // Perform unary operation on each element in [first,last) and
   // store the output in range beginning at result
```

```c++
int main() {
   vector<int> v;
   v.push_back(2);
   v.push_back(-3);
   v.push_back(4);
   v.push_back(3);
   ostream_iterator<int, char> out(cout, " ");
   copy(v.begin(), v.end(), out);
   cout << endl;
 
   transform(v.begin(), v.end(), v.begin(), square);
   copy(v.begin(), v.end(), out);
   cout << endl;
 
   transform(v.begin(), v.end(), out, ::sqrt);  // in <cmath>
   return 0;
}
```

```c++
// Binary Operation
template <class InputIterator1, class InputIterator2, class OutputIterator, class BinaryOperation>
OutputIterator transform (InputIterator1 first1, InputIterator1 last1,
                         InputIterator2 first2,
                         OutputIterator result, BinaryOperation op)
   // Perform binary operation on each element in [first1,last1) as first argument,
   // and the respective [frist2,...) as second argument.
   // Store the output in range beginning at result
```

```c++
int main() {
   int a1[5] = {1, 2, 3, 4, 5};
   int a2[5] = {11, 12, 13, 14, 15};
 
   vector<int> v1(a1, a1+5);
   vector<int> v2(a2, a2+5);
   ostream_iterator<int, char> out(cout, " ");
   copy(v1.begin(), v1.end(), out);
   cout << endl;
   copy(v2.begin(), v2.end(), out);
   cout << endl;
 
   transform(v1.begin(), v1.end(), v2.begin(), out, plus<int>());
       // Send result to output stream
   cout << endl;
 
   transform(v1.begin(), v1.end(), v2.begin(), v1.begin(), minus<int>());
       // Store result back to v1
   copy(v1.begin(), v1.end(), out);
   cout << endl;
   return 0;
}
```

