# Adaptive Sorting Strategy

## Part A: Adaptive Sorting Selection

## Threshold definition
I measure how sorted the array is using two values:
- Breaks: number of times A[i] > A[i+1]
- Inversion rate (sampled): percentage of out-of-order random pairs
Thresholds:
- Best / nearly sorted: breaks ≤ 3 or inversion rate ≤ 0.10
- Worst case: inversion rate ≥ 0.90
- Otherwise: average case
This avoids doing a full O(n²) inversion count and still gives a good estimate of order.

## Adaptive decision
- If the array is best or nearly sorted → use insertion sort
- Otherwise → use selection sort
Insertion sort becomes O(n) when the array is already sorted or almost sorted.
Selection sort is always O(n²), so it is used for more disordered input where insertion sort loses its advantage.
```cpp
#include <iostream>
#include <vector>
#include <random>
#include <string>
using namespace std;

int countBreaks(const vector<int>& a) {
    int breaks = 0;
    for (int i = 0; i < (int)a.size() - 1; i++) {
        if (a[i] > a[i + 1]) breaks++;
    }
    return breaks;
}

double sampledInversionRate(const vector<int>& a, int samples, int seed = 42) {
    mt19937 rng(seed);
    uniform_int_distribution<int> dist(0, (int)a.size() - 1);

    int inv = 0;
    for (int s = 0; s < samples; s++) {
        int i = dist(rng);
        int j = dist(rng);
        if (i == j) { s--; continue; }
        if (i > j) swap(i, j);
        if (a[i] > a[j]) inv++;
    }
    return (double)inv / (double)samples;
}

string classifyCase(const vector<int>& a) {
    int breaks = countBreaks(a);
    double invRate = sampledInversionRate(a, 400);

    if (breaks <= 3 || invRate <= 0.10) return "BEST_OR_NEAR_BEST";
    if (invRate >= 0.90) return "WORST";
    return "AVERAGE";
}

void selectionSort(vector<int>& a) {
    int n = (int)a.size();
    for (int i = 0; i < n - 1; i++) {
        int minIdx = i;
        for (int j = i + 1; j < n; j++) {
            if (a[j] < a[minIdx]) minIdx = j;
        }
        swap(a[i], a[minIdx]);
    }
}

void insertionSort(vector<int>& a) {
    int n = (int)a.size();
    for (int i = 1; i < n; i++) {
        int key = a[i];
        int j = i - 1;
        while (j >= 0 && a[j] > key) {
            a[j + 1] = a[j];
            j--;
        }
        a[j + 1] = key;
    }
}

vector<int> makeArray50() {
    vector<int> a(50);
    mt19937 rng(123);
    uniform_int_distribution<int> dist(-1000, 1000);
    for (int i = 0; i < 50; i++) a[i] = dist(rng);
    return a;
}

void printArray(const vector<int>& a) {
    for (int i = 0; i < (int)a.size(); i++) {
        cout << a[i] << (i + 1 == (int)a.size() ? "\n" : " ");
    }
}

int main() {
    vector<int> a = makeArray50();

    cout << "Original array:\n";
    printArray(a);

    string cls = classifyCase(a);
    cout << "Detected case: " << cls << "\n";

    if (cls == "BEST_OR_NEAR_BEST") {
        cout << "Choosing insertion sort.\n";
        insertionSort(a);
    } else {
        cout << "Choosing selection sort.\n";
        selectionSort(a);
    }

    cout << "Sorted array:\n";
    printArray(a);
    return 0;
}
```

## Part B: Case Classification Without Sorting
```cpp
#include <iostream>
#include <vector>
#include <random>
using namespace std;

int countBreaks(const vector<int>& a) {
    int breaks = 0;
    for (int i = 0; i < (int)a.size() - 1; i++) {
        if (a[i] > a[i + 1]) breaks++;
    }
    return breaks;
}

double sampledInversionRate(const vector<int>& a, int samples, int seed = 99) {
    mt19937 rng(seed);
    uniform_int_distribution<int> dist(0, (int)a.size() - 1);

    int inv = 0;
    for (int s = 0; s < samples; s++) {
        int i = dist(rng);
        int j = dist(rng);
        if (i == j) { s--; continue; }
        if (i > j) swap(i, j);
        if (a[i] > a[j]) inv++;
    }
    return (double)inv / (double)samples;
}

int main() {
    vector<int> a(50);
    cout << "Enter 50 integers:\n";
    for (int i = 0; i < 50; i++) cin >> a[i];

    int breaks = countBreaks(a);
    double invRate = sampledInversionRate(a, 400);

    bool worst = (invRate >= 0.90);
    if (worst) cout << "Worst Case\n";
    else cout << "Average Case\n";

    return 0;
}
```

## Part C: Documentation and Explanation

## Threshold definition used
I used adjacent breaks and a sampled inversion rate to estimate how ordered the array is.
Small values mean the array is nearly sorted.
Very large inversion rate means the array is close to reverse order.
## Why the program selects one algorithm over the other
Insertion sort is very fast when the array is already sorted because it performs very few shifts.
Selection sort always performs the same number of comparisons, so it is more consistent for random or highly disordered input.
## How input order affects time complexity
- Selection sort:
- Best = Average = Worst = O(n²)
- Input order does not change its performance.

- Insertion sort:
- Best = O(n) when already sorted
- Average = O(n²)
- Worst = O(n²) when reversed
So insertion sort benefits from nearly sorted data, while selection sort does not.
