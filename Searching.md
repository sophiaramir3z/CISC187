# Activity 2 Searching

## Video Explanation: 
https://sdccd.us-west-2.instructuremedia.com/embed/6a8885a5-a1e9-4d58-8869-befc72a8a9e5

## 1. Linear search steps for finding 8 (ordered array)
Array: [2, 4, 6, 8, 10, 12, 13]

A linear search compares from the beginning until it finds 8.

Comparisons: 2 (1), 4 (2), 6 (3), 8 (4)

Answer: 4 steps (comparisons)

## 2. Binary search steps for the same example

Binary search checks the middle element first.

Indices 0 to 6, middle m = floor((0 + 6) / 2) = 3, A[3] = 8, found immediately.

Answer: 1 step (comparison)

## 3. Maximum steps for binary search on an array of size 100,000

Using the book’s rule: worst case steps = ceil(log2(N))

log2(100000) ≈ 16.6096, ceil(...) = 17

Answer: 17 steps (comparisons)

## C++ program: linear vs binary search on 100,000 elements, with step counting
```cpp
#include <iostream>
#include <vector>

int linearSearch(const std::vector<int>& a, int key, long long& comparisons) {
    comparisons = 0;
    for (int i = 0; i < static_cast<int>(a.size()); i++) {
        comparisons++;
        if (a[i] == key) return i;

        // Optional early stop because data is sorted
        if (a[i] > key) return -1;
    }
    return -1;
}

int binarySearch(const std::vector<int>& a, int key, long long& comparisons) {
    comparisons = 0;
    int left = 0;
    int right = static_cast<int>(a.size()) - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        comparisons++;
        if (a[mid] == key) return mid;

        comparisons++;
        if (a[mid] < key) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return -1;
}

int main() {
    const int N = 100000;

    // Sorted dataset: 1..100000
    std::vector<int> data;
    data.reserve(N);
    for (int i = 1; i <= N; i++) data.push_back(i);

    int key;
    std::cout << "Enter an integer key to search for (1 to 100000): ";
    std::cin >> key;

    long long linComps = 0;
    long long binComps = 0;

    int linIndex = linearSearch(data, key, linComps);
    int binIndex = binarySearch(data, key, binComps);

    std::cout << "\nResults\n";
    std::cout << "Linear search index: " << linIndex << "\n";
    std::cout << "Linear search comparisons: " << linComps << "\n\n";

    std::cout << "Binary search index: " << binIndex << "\n";
    std::cout << "Binary search comparisons: " << binComps << "\n";

    return 0;
}
```

## Big O analysis and justification

Linear search checks elements one by one until it finds the key (or reaches the end). In the worst case, the key is last or not present, so it performs about N comparisons. 
That is why linear search is O(N).Binary search repeatedly halves the search range. After 1 comparison you eliminate about half the array, 
after 2 comparisons you eliminate about three quarters, and so on. The number of times you can halve N until it becomes 1 is log2(N). 
That is why binary search is O(log N), and for N = 100000 the worst case is about 17 iterations by the book’s rounding rule.

## 5. Randomized search without repetition
Pseudocode

RandomizedSearchNoRepeat(vector V, key):

Let n = size(V)

Create vector idx of length n

For i from 0 to n-1:
idx[i] = i

Shuffle idx uniformly at random (Fisher-Yates):
For i from n-1 down to 1:
j = random integer in [0, i]
swap idx[i] and idx[j]

comparisons = 0

For k from 0 to n-1:
comparisons = comparisons + 1
if V[idx[k]] == key:
return (idx[k], comparisons)

return (-1, comparisons)

This guarantees no repetition because each index appears exactly once in idx after shuffling.

## Time complexity (Big O)

Best case: O(1) because the first randomly chosen index could match the key.

Average case: O(N) because on average you examine a constant fraction of the array before finding the key (roughly N/2 comparisons if the key exists and its position is equally likely in the random order). If the key is not present, you examine all N elements.

Worst case: O(N) because the key might be the last examined element, or not present.

## C++ implementation (only <vector>, <random>, <iostream>)
```cpp
#include <vector>
#include <random>
#include <iostream>

int randomizedSearchNoRepeat(const std::vector<int>& v, int key, long long& comparisons) {
    const int n = static_cast<int>(v.size());

    std::vector<int> idx;
    idx.reserve(n);
    for (int i = 0; i < n; i++) idx.push_back(i);

    std::random_device rd;
    std::mt19937 gen(rd());

    // Fisher-Yates shuffle, implemented manually (no extra headers)
    for (int i = n - 1; i >= 1; i--) {
        std::uniform_int_distribution<int> dist(0, i);
        int j = dist(gen);

        int temp = idx[i];
        idx[i] = idx[j];
        idx[j] = temp;
    }

    comparisons = 0;
    for (int k = 0; k < n; k++) {
        comparisons++;
        if (v[idx[k]] == key) return idx[k];
    }

    return -1;
}

int main() {
    const int N = 100000;

    std::vector<int> data;
    data.reserve(N);
    for (int i = 1; i <= N; i++) data.push_back(i); // distinct elements

    int key;
    std::cout << "Enter key to search for: ";
    std::cin >> key;

    long long comps = 0;
    int pos = randomizedSearchNoRepeat(data, key, comps);

    std::cout << "Randomized search index: " << pos << "\n";
    std::cout << "Randomized search comparisons: " << comps << "\n";

    return 0;
}
```

## Comparison: randomized vs linear vs binary
In time complexity, randomized search behaves like linear search in the big picture. Even though it feels different, it still may need to examine many elements, so average and worst case are O(N). 
Binary search is fundamentally faster at scale because it cuts the search space in half each step, giving O(log N).

In data requirements, linear search works on any array, sorted or unsorted. Randomized search also works on any array and does not require sorting, 
but it has extra overhead because it must generate a random order of indices to avoid repetition. Binary search requires the data to be sorted, and that requirement is the price you pay for speed.

In practical efficiency, linear search is usually better than randomized search if you are already scanning anyway, because linear search has almost zero setup cost and has good cache locality. 
Randomized search is typically worse in practice because it destroys locality and adds shuffle cost, so you usually only choose it for special scenarios like wanting to avoid predictable access patterns,
doing probabilistic probing, or mixing with other randomized strategies. Binary search is usually the best choice for repeated lookups on sorted data, especially when you will search many times and sorting cost
is amortized across all those searches.

