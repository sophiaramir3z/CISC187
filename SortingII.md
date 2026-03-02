# Sorting algorithms

## Explanation video:

## 1. Average-case time complexity of insertion sort = O(N²)
- Insertion sort processes one element at a time and inserts it into the sorted left portion
- On pass i, the key moves left through about i/2 elements on average
- Work per pass grows linearly:
  - Comparisons ≈ i/2
  - Shifts ≈ i/2
- Total work:
  - 1 + 2 + 3 + … + (N−1)
  - = N(N−1)/2
  - = Θ(N²)
- Using inversions:
  - Average inversions in random array = N(N−1)/4
  - Each shift removes one inversion
  - ⇒ Total shifts = Θ(N²)
  - Comparisons are same order
Therefore average time complexity = O(N²)

## Figure showing the operations growth
Think of a “triangle” of work. On pass i, the average key travels about halfway left, so about i/2 shifts and i/2 comparisons happen.
```
Pass i:   expected comparisons (≈ i/2) and shifts (≈ i/2)

i = 1     ■
i = 2     ■ ■
i = 3     ■ ■ ■
i = 4     ■ ■ ■ ■
...
i = N-1   ■ ■ ■ ■ ... (N-1 blocks)

Total blocks ≈ 1 + 2 + 3 + ... + (N-1) = N(N-1)/2 = Θ(N²)
Average case is about half of that triangle for movement, still Θ(N²).
```
Even though the constant changes (half the triangle instead of the full triangle), Big O ignores constants, so it is still O(N²).

## 2. Counting operations 

## a) Start at i = 1. Verify total operations = 20.
- Array: [5, 4, 3, 2, 1]
- Worst case: every comparison causes a shift
Pass breakdown:
- i = 1
  - Comparisons: 1
  - Shifts: 1
  - Total = 2

- i = 2
  - Comparisons: 2
  - Shifts: 2
  - Total = 4

- i = 3
  - Comparisons: 3
  - Shifts: 3
  - Total = 6

- i = 4
  - Comparisons: 4
  - Shifts: 4
  - Total = 8

Total operations = 2 + 4 + 6 + 8 = 20

## b) Start at different indices.
Start at i = 2
- i = 2 → total = 4
- i = 3 → total = 6
- i = 4 → total = 8
Total = 18 operations

Start at i = 3
- i = 3 → total = 6
- i = 4 → total = 8
Total = 14 operations

## c) Does it still sort the whole array?
- No
- Insertion sort assumes:
  - Left side is already sorted before each pass
- If starting at i = 2 or i = 3:
  - The first part is still unsorted
  - Later insertions use an unsorted prefix
- Result → array is not guaranteed to be fully sorted

## 3. containsX(string) analysis and improvement

## a) Time complexity in Big O
- Scans string once
- O(n)
  
## b) Modify the code to improve best and average case efficiency
```cpp
#include <string>
using namespace std;

bool containsX(const string& str) {
    for (int i = 0; i < str.length(); i++) {
        if (str[i] == 'X') {
            return true;
        }
    }
    return false;
}
```
- Stops early if found
- Best case → O(1)
- Worst case → O(n)
