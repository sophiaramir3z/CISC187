# Activity 1 - Arrays

## Explanation Video
https://sdccd.us-west-2.instructuremedia.com/embed/3ada3b3b-6528-4b9b-b5a3-c6ca7fd5939d 

## 1. Array of 100 Elements
```cpp
#include <iostream>
using namespace std;

int main() {
    int arr[100];                 // array of 100 integers
    arr[0] = 42;                  // example assignment
    cout << arr[0] << endl;       // example read
    return 0;
}
```

## 2. Size of each element in the array
```cpp
#include <iostream>
using namespace std;

int main() {
    int arr[100];

    cout << "Size of one element: " << sizeof(arr[0]) << " bytes" << endl;
    cout << "Size of int: " << sizeof(int) << " bytes" << endl;

    return 0;
}
```

## 3. Steps for operations on an array of 100 elements
1) Reading
Reading arr[i] is direct index access.
Steps: 1

2) Searching for a value not contained in the array
Worst case: you check all 100 elements and never find it.
Steps: 100 (which is N)

3) Insertion at the beginning of the array
To insert at index 0, you must shift existing elements right by one position, then place the new value.
Shifts: 100 (or more commonly described as N shifts)
Insert write: 1
Steps: 100 + 1 = 101 (which is N + 1)

4) Insertion at the end of the array
If there is space available at the end, you just write the new value to the next open slot.
Steps: 1

5) Deletion at the beginning of the array
Delete index 0, then shift remaining elements left to fill the gap.
Delete: 1
Shifts: 99 (elements 1 through 99 move left)
Steps: 1 + 99 = 100 (which is N)

6) Deletion at the end of the array
Just remove the last element (or decrement the logical size if you track size separately).
Steps: 1

## 4.Steps to find all instances of a value like “apple” (in terms of N)
If you want to count every "apple" in an array of size N, you must scan the entire array even if you find one early, because there could be more later.

Steps: N

## 5. Find the memory address of an array
```cpp
#include <iostream>
using namespace std;

int main() {
    int arr[100];

    cout << "Base address using arr:     " << arr << endl;
    cout << "Base address using &arr[0]: " << &arr[0] << endl;

    cout << "Address of arr[1]:          " << &arr[1] << endl;

    return 0;
}
```
