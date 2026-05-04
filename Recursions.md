# Activity 12 - Recursions

## Task 1
The base case is:
```cpp
if (low > high) {
    return;
}


// This stops the function once low becomes greater than high.

#include <iostream>
using namespace std;

void printEveryOther(int low, int high) {
    if (low > high) {
        return;
    }

    cout << low << endl;
    printEveryOther(low + 2, high);
}
```
## Task 2
If we run factorial(10), the function will return:
```cpp
10 * 8 * 6 * 4 * 2 * factorial(0)

//But the base case only stops when n == 1. Since the function reaches 0, then -2, then -4, and keeps going, it never reaches the base case.
//So the function causes infinite recursion and eventually a stack overflow.

int factorial(int n) {
    if (n == 1) {
        return 1;
    }

    return n * factorial(n - 2);
}
```
## Task 3
The base case should stop when high == low.
```cpp
#include <iostream>
using namespace std;

int sum(int low, int high) {
    if (high == low) {
        return low;
    }

    return high + sum(low, high - 1);
}
```
Example:
```cpp
sum(1, 10)
```
returns:
```cpp
55
```
## Task 4
To print all numbers inside a nested array, we need recursion because the array can contain other arrays inside it.
Since C++ arrays cannot naturally hold both integers and arrays like Ruby or Python, we can use variant and vector.
```cpp
#include <iostream>
#include <vector>
#include <variant>
using namespace std;

struct NestedArray;

using Element = variant<int, vector<NestedArray>>;

struct NestedArray {
    Element value;
};

void printNumbers(const vector<NestedArray>& array) {
    for (const NestedArray& item : array) {
        if (holds_alternative<int>(item.value)) {
            cout << get<int>(item.value) << endl;
        } else {
            printNumbers(get<vector<NestedArray>>(item.value));
        }
    }
}
```
Full Example:
```cpp
#include <iostream>
#include <vector>
#include <variant>
using namespace std;

struct NestedArray;

using Element = variant<int, vector<NestedArray>>;

struct NestedArray {
    Element value;
};

void printNumbers(const vector<NestedArray>& array) {
    for (const NestedArray& item : array) {
        if (holds_alternative<int>(item.value)) {
            cout << get<int>(item.value) << endl;
        } else {
            printNumbers(get<vector<NestedArray>>(item.value));
        }
    }
}

int main() {
    vector<NestedArray> array = {
        {1},
        {2},
        {3},
        {vector<NestedArray>{{4}, {5}, {6}}},
        {7},
        {vector<NestedArray>{
            {8},
            {vector<NestedArray>{
                {9}, {10}, {11},
                {vector<NestedArray>{{12}, {13}, {14}}}
            }}
        }},
        {vector<NestedArray>{
            {15}, {16}, {17}, {18}, {19},
            {vector<NestedArray>{
                {20}, {21}, {22},
                {vector<NestedArray>{
                    {23}, {24}, {25},
                    {vector<NestedArray>{{26}, {27}, {29}}}
                }},
                {30}, {31}
            }},
            {32}
        }},
        {33}
    };

    printNumbers(array);

    return 0;
}
```
