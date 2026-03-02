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

## 2. Counting operations for insertion sort with N = 5 in descending order

## a) Start at i = 1. Verify total operations = 20.
## b) Start the algorithm at i = 2, then start at i = 3. Count operations again.
## c) For (b), does the algorithm still sort the entire array?


## 3. containsX(string) analysis and improvement

## (a) Time complexity in Big O

## (b) Modify the code to improve best and average case efficiency
