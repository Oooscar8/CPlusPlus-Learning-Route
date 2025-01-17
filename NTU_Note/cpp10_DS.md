[toc]

# Data Structure and Algorithm

---

> https://www3.ntu.edu.sg/home/ehchua/programming/cpp/DataStructureAlgorithm.html#zz-3.



## Searching

- Linear Search: See "[Linear Search](https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp1_Basics.html#LinearSearch)".
- Recursive Binary Search for sorted list
- Binary Tree Search



### Linear Search

Simple!

```c++
// Search the array for the given key
// If found, return array index [0, size-1]; otherwise, return size
int linearSearch(const int a[], int size, int key) {
   for (int i = 0; i < size; ++i) {
      if (a[i] == key) return i;
   }
   return size;
}
```



### Binary Search

A binary search (or half-interval search) is applicable only to a **sorted array**.

```c++
// Search the array for the given key
// If found, return array index; otherwise, return -1
int binarySearch(const int a[], int size, int key) {
   // Call recursive helper function
   return binarySearch(a, 0, size-1, key);
}
 
// Recursive helper function for binarySearch
int binarySearch(const int a[], int iLeft, int iRight, int key) {
   // Test for empty list
   if (iLeft > iRight) return -1;
 
   // Compare with middle element
   int mid = (iRight + iLeft) / 2;  // truncate
   if (key == a[mid]) {
      return mid;
   } else if (key < a[mid]) {
      // Recursively search the lower half
      binarySearch(a, iLeft, mid - 1, key);
   } else {
      // Recursively search the upper half
      binarySearch(a, mid + 1, iRight, key);
   }
}
```



## Sorting

- Insertion Sort: See "[Insertion Sort](https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp1_Basics.html#InsertionSort)".
- Selection Sort: See "[Selection Sort](https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp1_Basics.html#SelectionSort)".
- Bubble Sort: See "[Bubble Sort](https://www3.ntu.edu.sg/home/ehchua/programming/cpp/cp1_Basics.html#BubbleSort)"
- Merge Sort (Recursive Top-Down or Interactive Bottom-up)
- Quick Sort (Recursive)
- Bucket Sort
- Heap Sort
- Binary Tree Sort



### Insertion sort

For each element, compare with all previous elements and insert it at the correct position by shifting the other elements. For example,

```
{8,4,5,3,2,9,4,1}
{8} {4,5,3,2,9,4,1}
{4,8} {5,3,2,9,4,1}
{4,5,8} {3,2,9,4,1}
{3,4,5,8} {2,9,4,1}
{2,3,4,5,8} {9,4,1}
{2,3,4,5,8,9} {4,1}
{2,3,4,4,5,8,9} {1}
{1,2,3,4,4,5,8,9}
```

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

Insertion sort is not efficient, with complexity of $O(n^2)$.



### Selection sort

Pass thru the list. Select the smallest element and swap with the head of the list.

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

Selection sort is not efficient, with complexity of $O(n^2)$.



### Bubble sort

Pass thru the list, compare two adjacent items and swap them if they are in the wrong order. **Repeat the pass until no swaps are needed**.

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
            temp = a[i];
            a[i] = a[i+1];
            a[i+1] = temp;
            done = false;   // swap detected, one more pass
         }
      }
   }
}
```

Bubble sort is not efficient, with complexity of $O(n^2)$.



### Merge Sort

* **Recursive** Top-Down Merge Sort

1. Recursively divide the list into 2 sublists.
2. When the sublists contain 1 element (a list of 1 element is sorted), merge two sublists in the right order. Unwind the merging recursively.

```c++
// Function to merge two sorted subarrays into a single sorted array
// arr[left..mid] and arr[mid+1..right] are the subarrays to be merged
void merge(std::vector<int>& arr, int left, int mid, int right) {
    // Calculate sizes of the two subarrays to be merged
    int n1 = mid - left + 1;  // Size of left subarray
    int n2 = right - mid;     // Size of right subarray
    
    // Create temporary arrays to store the two halves
    // This extra space is necessary to perform the merge
    std::vector<int> L(n1);
    std::vector<int> R(n2);
    
    // Copy data to temporary arrays L[] and R[]
    // This allows us to modify the original array while merging
    for(int i = 0; i < n1; i++)
        L[i] = arr[left + i];
    for(int j = 0; j < n2; j++)
        R[j] = arr[mid + 1 + j];
    
    // Initialize indices for merging
    int i = 0;    // Initial index of left subarray
    int j = 0;    // Initial index of right subarray
    int k = left; // Initial index of merged array
    
    // Merge the temporary arrays back into arr[left..right]
    // Compare elements from both subarrays and place the smaller one first
    while(i < n1 && j < n2) {
        if(L[i] <= R[j]) {
            arr[k] = L[i];
            i++;
        } else {
            arr[k] = R[j];
            j++;
        }
        k++;
    }
    
    // Copy remaining elements of L[], if any
    // If there are any elements left in the left subarray
    while(i < n1) {
        arr[k] = L[i];
        i++;
        k++;
    }
    
    // Copy remaining elements of R[], if any
    // If there are any elements left in the right subarray
    while(j < n2) {
        arr[k] = R[j];
        j++;
        k++;
    }
}

// Recursive function to sort an array using merge sort
// left is the left index and right is the right index of the subarray to be sorted
void mergeSortRecursive(std::vector<int>& arr, int left, int right) {
    // Base case: if left >= right, subarray has 0 or 1 element (already sorted)
    if(left < right) {
        // Find the middle point to divide array into two halves
        // Using (left + right)/2 might cause integer overflow
        int mid = left + (right - left) / 2;
        
        // Sort first and second halves recursively
        mergeSortRecursive(arr, left, mid);      // Sort left half
        mergeSortRecursive(arr, mid + 1, right); // Sort right half
        
        // Merge the sorted halves
        merge(arr, left, mid, right);
    }
}
```

* **Iterative** Bottom-up Merge Sort

1. Treat the list as sublists of length 1.
2. Interactively merge a pair of sublists bottom-up, until there is only one sublist.

```c++
// The merge function remains similar to the recursive version
// It combines two sorted subarrays into a single sorted array
// arr[left..mid] and arr[mid+1..right] are the subarrays to be merged
void merge(std::vector<int>& arr, int left, int mid, int right) {
    int n1 = mid - left + 1;
    int n2 = right - mid;
    
    std::vector<int> L(n1);
    std::vector<int> R(n2);
    
    for(int i = 0; i < n1; i++)
        L[i] = arr[left + i];
    for(int j = 0; j < n2; j++)
        R[j] = arr[mid + 1 + j];
    
    int i = 0, j = 0, k = left;
    
    while(i < n1 && j < n2) {
        if(L[i] <= R[j]) {
            arr[k] = L[i];
            i++;
        } else {
            arr[k] = R[j];
            j++;
        }
        k++;
    }
    
    while(i < n1) {
        arr[k] = L[i];
        i++;
        k++;
    }
    
    while(j < n2) {
        arr[k] = R[j];
        j++;
        k++;
    }
}

// Iterative merge sort implementation using a bottom-up approach
void mergeSortIterative(std::vector<int>& arr) {
    int n = arr.size();
    
    // Start merging subarrays of size 1, then 2, 4, 8, and so on
    // The 'width' variable represents the size of subarrays being merged
    for(int width = 1; width < n; width = width * 2) {
        // For each pair of subarrays of size 'width'
        // left marks the start of the first subarray
        for(int left = 0; left < n; left = left + 2 * width) {
            // Calculate the middle point and right boundary
            int mid = std::min(left + width - 1, n - 1);
            int right = std::min(left + 2 * width - 1, n - 1);
            
            // Merge the two subarrays if they exist
            if(left < mid && mid < right) {
                merge(arr, left, mid, right);
            }
        }
    }
}
```

The worst-case and average-case **time complexity is O(n log n)**. The best-case is typically O(n log n). However, merge sort requires a **space complexity of O(n)** for carrying out the merge-sorting.



### Quick Sort

The Core Idea:

1. Choose a 'pivot' element from the array
2. Partition the array around this pivot (elements smaller than pivot go to the left, larger to the right)
3. Recursively apply the same process to the subarrays

For example, consider the array [10, 7, 8, 9, 1, 5]:

```
Initial array: [10, 7, 8, 9, 1, 5]  (pivot = 5)
After partition: [1, | 5 | 10, 7, 8, 9]

[10, 7, 8, 9] (pivot = 9)
After partition: [7, 8, | 9 | 10]
```

```c++
// This function takes the last element as pivot, places the pivot 
// at its correct position, and places smaller elements to the left 
// and greater elements to the right
int partition(std::vector<int>& arr, int low, int high) {
    // Select the rightmost element as pivot
    int pivot = arr[high];
    
    // Index of smaller element
    int i = low - 1;
    
    // Compare each element with pivot
    for(int j = low; j < high; j++) {
        // If current element is smaller than pivot
        if(arr[j] <= pivot) {
            i++;    // Increment index of smaller element
            std::swap(arr[i], arr[j]);
        }
    }
    
    // Place pivot in its final position
    std::swap(arr[i + 1], arr[high]);
    return i + 1;
}

// The main QuickSort function
void quickSort(std::vector<int>& arr, int low, int high) {
    if(low < high) {
        // Find the partition index
        int pi = partition(arr, low, high);
        
        // Separately sort elements before and after partition
        quickSort(arr, low, pi - 1);
        quickSort(arr, pi + 1, high);
    }
}
```

The **worst-case time complexity is $O(n^2)$**. The **average-case and best-case is O(n log n)**. In-place sorting can be achieved without additional space requirement.



### Bucket Sort

Bucket sort is a distribution sort, and is a cousin of radix sort.

The core idea:

1. Set up buckets, initially empty.
2. Scatter: place each element into an appropriate bucket.
3. Sort each non-empty bucket.
4. Gather: Gather elements from buckets and put back to the original array.

* Radix Sort

```c++
void bucketSort(int a[], int size) {
   // find maximum to decide on the number of significant digits
   int max = a[0];
   for (int i = 1; i < size; ++i) {
      if (a[i] > max) max = a[i];
   }
 
   // Decide on the max radix (1000, 100, 10, 1, etc)
   int radix = 1;
   while (max > 10) {
      radix *= 10;
      max /= 10;
   }
 
   // copy the array into a vector
   vector<int> list(size);
   for (int i = 0; i < size; ++i) {
      list[i] = a[i];
   }
   bucketSort(list, radix);
}
 
// Sort the given array of size on the particular radix (1, 10, 100, etc)
// Assume elements are non-negative integers
// radix shall be more than 0
void bucketSort(vector<int> & list, int radix) {
   if (list.size() > 1 && radix > 0) {  // Sort if more than 1 elements
      vector<int> buckets[NUM_BUCKETS];  // 10 buckets
 
      // Distribute elements into buckets
      for (int i = 0; i < list.size(); ++i) {
         int bucketIndex = list[i] / radix % 10;
         buckets[bucketIndex].push_back(list[i]);
      }
 
      // Recursively sort the non-empty bucket
      for (int bi = 0; bi < NUM_BUCKETS; ++bi) {
         if (buckets[bi].size() > 0) {
            bucketSort(buckets[bi], radix / 10);
         }
      }
 
      // Gather all the buckets into list and return
      list.resize(0);  // remove all elements
      for (int bi = 0; bi < NUM_BUCKETS; ++bi) {
         for (int j = 0; j < buckets[bi].size(); ++j) {
            list.push_back((buckets[bi])[j]);
         }
      }
   }
}
```

For example:

Consider the initial array: {28,104,25,593,22,129,4,11,129,4,111,20,9}

```
To sort: {28,104,25,593,22,129,4,11,129,4,111,20,9}
radix=100: {28,25,22,4,11,4,20,9} {104,129,129,111} {} {} {} {593} {} {} {} {}

To sort: {28,25,22,4,11,4,20,9}
radix=10: {4,4,9} {11} {28,25,22,20} {} {} {} {} {} {} {}
To sort: {4,4,9}
radix=1: {} {} {} {} {4,4} {} {} {} {} {9}
Sorted: {4,4,9}
To sort: {28,25,22,20}
radix=1: {20} {} {22} {} {} {25} {} {} {28} {}
Sorted: {20,22,25,28}
Sorted: {4,4,9,11,20,22,25,28}

To sort: {104,129,129,111}
radix=10: {104} {111} {129,129} {} {} {} {} {} {} {}
To sort: {129,129}
radix=1: {} {} {} {} {} {} {} {} {} {129,129}
Sorted: {129,129}
Sorted: {104,111,129,129}
Sorted: {4,4,9,11,20,22,25,28,104,111,129,129,593}
```

> [!NOTE]
>
> If the maximum number of the array is M, then the time complexity is $O(n × log_{10}(M))$​.
>
> $log_{10}(M)$ is the number of digits in number M.



##  Data Structures

Many applications require dynamic data structures, that can grow and shrink during execution. The commonly used data structures include:

- List: Single linked list, double linked list, etc.
- Queue: FIFO queue, priority queue, etc.
- Stack: LIFO queue
- Tree:
- Map or Associative Array:



### Single Linked List

![image-20250115010202036](https://gitee.com/OooAlex/study_note/raw/master/img/202501150102557.png)

```c++
template <typename T> class List;  // Forward reference
 
template <typename T>
class Node {
private:
   T data;
   Node * nextPtr;
public:
   Node (T d) : data(d), nextPtr(0) { }; // Constructor
   T getData() const { return data; };   // Public getter for data
   Node * getNextPtr() const { return nextPtr; } // Public getter for nextPtr
 
friend class List<T>;  // Make List class a friend to access private data
};

// Forward Reference
template <typename T>
std::ostream & operator<<(std::ostream & os, const List<T> & lst);
 
template <typename T>
class List {
private:
   Node<T> * frontPtr;  // First node
   Node<T> * backPtr;   // Last node
public:
   List();   // Constructor
   ~List();  // Destructor
   void pushFront(const T & value);
   void pushBack(const T & value);
   bool popFront(T & value);
   bool popBack(T & value);
   bool isEmpty() const;
 
friend std::ostream & operator<< <>(std::ostream & os, const List<T> & lst);
      // Overload the stream insertion operator to print the list
};
```



### Double Linked List

![image-20250115135851740](https://gitee.com/OooAlex/study_note/raw/master/img/202501151358064.png)

```c++
template <typename T> class DoubleLinkedList; // Forward reference
 
template <typename T>
class DoubleLinkedNode {
private:
   T data;
   DoubleLinkedNode * nextPtr;
   DoubleLinkedNode * prevPtr;
public:
   DoubleLinkedNode (T d) : data(d), nextPtr(0), prevPtr(0) { };
   T getData() const { return data; };
   DoubleLinkedNode * getNextPtr() const { return nextPtr; }
   DoubleLinkedNode * getPrevPtr() const { return prevPtr; }
 
friend class DoubleLinkedList<T>;
   // Make DoubleLinkedList class a friend to access private data
};

// Forward Reference
template <typename T>
std::ostream & operator<<(std::ostream & os,
      const DoubleLinkedList<T> & lst);
 
template <typename T>
class DoubleLinkedList {
private:
   DoubleLinkedNode<T> * frontPtr;
   DoubleLinkedNode<T> * backPtr;
public:
   DoubleLinkedList();   // Constructor
   ~DoubleLinkedList();  // Destructor
   void pushFront(const T & value);
   void pushBack(const T & value);
   bool popFront(T & value);
   bool popBack(T & value);
   bool isEmpty() const;
 
friend std::ostream & operator<< <>(std::ostream & os,
      const DoubleLinkedList<T> & lst);
      // Overload the stream insertion operator to print the list
};
```



### Stack (LIFO Queue)

![image-20250115140320455](https://gitee.com/OooAlex/study_note/raw/master/img/202501151403734.png)

```c++
// Forward Reference
template <typename T>
class Stack;
template <typename T>
std::ostream & operator<<(std::ostream & os, const Stack<T> & s);
 
template <typename T>
class Stack {
private:
   T * data;      // Array
   int tos;       // Top of stack, start at index -1
   int capacity;  // capacity of the array
   int increment; // each subsequent increment size
public:
   explicit Stack(int capacity = 10, int increment = 10);
   ~Stack();  // Destructor
   void push(const T & value);
   bool pop(T & value);
   bool isEmpty() const;
 
friend std::ostream & operator<< <>(std::ostream & os, const Stack<T> & s);
      // Overload the stream insertion operator to print the list
};
```

We use an array to store the data in the stack and use `tos` as the index of the array to get the data.



### Tree

A Binary Tree:

![image-20250115141313469](https://gitee.com/OooAlex/study_note/raw/master/img/202501151413745.png)

* Depth-First Search (DFS)

Start at the root and explore as far as possible along each branch before backtracking. There are 3 types of depth-first search:

1. Pre-order: visit the root, traverse the left subtree, then the right subtree. E.g., 6 -> 5 -> 4 -> 10 -> 7 -> 9 ->15.
2. In-order: traverse the left subtree, visit the root, then the right subtree. E.g., 4 -> 5 -> 6 -> 7 -> 9 ->10 -> 15.
3. Post-order: traverse the left subtree, the right subtree, then visit the root. E.g, 4 -> 5 -> 9 -> 7 -> 15 -> 10 -> 6.

* Breadth-First Search (BFS)

Begin at the root, visit all its child nodes. Then for each of the child nodes visited, visit their child nodes in turn. E.g., 6 -> 5 -> 10 -> 4 -> 7 -> 15 -> 9.

* Binary Search Tree

```c++
template <typename T> class BinaryTree; // Forward reference
 
template <typename T>
class Node {
private:
   T data;
   Node * rightPtr;
   Node * leftPtr;
public:
   Node (T d) : data(d), rightPtr(0), leftPtr(0) { };
   T getData() const { return data; };
   Node * getRightPtr() const { return rightPtr; }
   Node * getLeftPtr() const  { return leftPtr;  }
 
friend class BinaryTree<T>;
   // Make BinaryTree class a friend to access private data
};

// Forward Reference
template <typename T>
std::ostream & operator<<(std::ostream & os, const BinaryTree<T> & lst);
 
template <typename T>
class BinaryTree {
private:
   Node<T> * rootPtr;
 
   // private helper functions
   void insertNode(Node<T> * & ptr, const T & value);
   void preOrderSubTree(const Node<T> * ptr, std::ostream & os = std::cout) const;
   void inOrderSubTree(const Node<T> * ptr, std::ostream & os = std::cout) const;
   void postOrderSubTree(const Node<T> * ptr, std::ostream & os = std::cout) const;
   void removeSubTree(Node<T> * & ptr);
public:
   BinaryTree();   // Constructor
   ~BinaryTree();  // Destructor
   void insert(const T & value);
   bool isEmpty() const;
   void preOrderTraversal(std::ostream & os = std::cout) const;
   void inOrderTraversal(std::ostream & os = std::cout) const;
   void postOrderTraversal(std::ostream & os = std::cout) const;
   void breadthFirstTraversal(std::ostream & os = std::cout) const;
 
friend std::ostream & operator<< <>(std::ostream & os, const BinaryTree<T> & lst);
      // Overload the stream insertion operator to print the list
};
```

> [!NOTE]
>
> Breadth-First Search: need a FIFO queue to keep the child nodes.
>
> ```c++
> // Breadth First Search (BFS)
> template <typename T>
> void BinaryTree<T>::breadthFirstTraversal(std::ostream & os) const {
>    std::queue<Node<T> * > q;
>    if (!isEmpty()) q.push(rootPtr);
>  
>    os << "{ ";
>    Node<T> * currentPtr;
>    while (currentPtr = q.front()) {
>       std::cout << currentPtr->data << ' ';
>       if (currentPtr->leftPtr) q.push(currentPtr->leftPtr);
>       if (currentPtr->rightPtr) q.push(currentPtr->rightPtr);
>       q.pop();  // remove this node
>    }
>    os << '}' << std::endl;
> }
> ```

