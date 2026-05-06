# Space Constraints

## Task 1: Word Builder Space Complexity

The `wordBuilder` function creates a new vector called `collection`.
Because the function uses two nested loops, it generates every possible pair of words where the indexes are different. If the input array has `N` elements, then about `N * (N - 1)` strings are created.
This simplifies to `O(N^2)`.

### Space Complexity
**O(N²)**

```cpp
vector<string> wordBuilder(vector<string>& array) {
    vector<string> collection;

    for (int i = 0; i < array.size(); i++) {
        for (int j = 0; j < array.size(); j++) {
            if (i != j) {
                collection.push_back(array[i] + array[j]);
            }
        }
    }

    return collection;
}
```
## Task 2: Reverse Array Space Complexity

The function creates a new vector called `newArray`.
It copies every element from the original array into the new vector in reverse order. If the original array contains N elements, the new vector will also contain `N` elements.

### Space Complexity
**O(N)**
```cpp
vector<int> reverse(vector<int>& array) {
    vector<int> newArray;

    for (int i = array.size() - 1; i >= 0; i--) {
        newArray.push_back(array[i]);
    }

    return newArray;
}
```

## Task 3: Reverse an Array Using O(1) Extra Space

To achieve `O(1)` extra space, the array must be reversed in place without creating another vector.
This is done by swapping the first and last elements, then moving inward until the middle is reached.

```cpp
#include <iostream>
#include <vector>
using namespace std;

vector<int> reverseInPlace(vector<int>& array) {
    int left = 0;
    int right = array.size() - 1;

    while (left < right) {
        int temp = array[left];
        array[left] = array[right];
        array[right] = temp;

        left++;
        right--;
    }

    return array;
}
```
### Space Complexity
**O(1)**

Only a few variables (`left`, `right`, and `temp`) are used, and the amount of extra memory does not grow with the size of the array.

## Task 4: Time and Space Complexity Table

| Version    | Time Complexity | Space Complexity |
| ---------- | --------------- | ---------------- |
| Version #1 | O(N)            | O(N)             |
| Version #2 | O(N)            | O(1)             |
| Version #3 | O(N)            | O(N)             |

## Version #1
```cpp
vector<int> doubleArray1(vector<int>& array) {
    vector<int> newArray;

    for (int i = 0; i < array.size(); i++) {
        newArray.push_back(array[i] * 2);
    }

    return newArray;
}
```
### Explanation
The function loops through the array once, so the time complexity is `O(N)`.
A new vector is created that stores `N` elements, so the space complexity is `O(N)`.

## Version #2
```cpp
vector<int> doubleArray2(vector<int>& array) {
    for (int i = 0; i < array.size(); i++) {
        array[i] *= 2;
    }

    return array;
}
```
### Explanation
The function loops through the array once, so the time complexity is `O(N)`.
It modifies the original array directly without creating a new vector, so the space complexity is `O(1)`.

## Version #3
```cpp
vector<int> doubleArray3(vector<int>& array, int index = 0) {
    if (index >= array.size()) {
        return array;
    }

    array[index] *= 2;

    return doubleArray3(array, index + 1);
}
```
### Explanation
The function recursively processes each element once, so the time complexity is `O(N)`.
Even though no new vector is created, each recursive call is stored on the call stack. Since there are `N` recursive calls, the space complexity is `O(N)`.
