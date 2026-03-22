# Arrays Honors Contract

## Link to Video: http://tiny.cc/H2Vid
## Link to Slides: http://tiny.cc/DataAnalyst 
# a. Approach
## Input
The input is a sequence of monthly sales values stored in an array or vector. Each value represents the sales for one month. For the given dataset, the input would be:
- Jan = 100
- Feb = 113
- Mar = 110
- Apr = 85
- May = 81
- Jun = 101
- Jul = 94
- Aug = 106
- Sep = 105
- Oct = 102
- Nov = 86
- Dec = 63

## Desired Output
The output should identify the consecutive stretch of months with the highest total sales. Specifically, the program should return:
- The starting month of the best segment
- The ending month of the best segment
- The maximum total sales across that segment

For this dataset, since every month is positive, the best segment is the entire year:
- Start month: Jan
- End month: Dec
- Maximum total: 1146 million USD

## Plain Language Strategy
To solve this, I would scan through the sales values from left to right while keeping track of two things: the best segment found so far, and the current segment I am building. 
At each month, I decide whether it is better to start a new segment at that month or continue the previous segment.

If adding the current month to the running total makes the total better than starting fresh, I continue the segment. Otherwise, I start a new segment from the current month. As I move through the array, 
I compare the current running total with the best total seen so far. If the current total is larger, I update the best result.

This is the standard idea behind finding the maximum sum subarray, also called Kadane’s algorithm, but I extend it to also keep track of where the segment starts and ends.

## Handling Ties
If two different segments have the same maximum total, I need a tie breaking rule. A simple and consistent rule is to keep the earliest segment. That means if I already found a segment with the same total, 
I do not replace it with a later one.

Another reasonable tie rule could be to choose the shortest segment or longest segment, but for this solution I will use the earliest occurrence because it is simple and predictable.

## Handling All Negative Values
If all sales values are negative, the algorithm should still work correctly. In that case, the best segment is the single month with the least negative value,
since any larger segment would only make the total worse. For example, if the values were -5, -2, -9, the answer should be the month with -2.

## Handling Multiple Peak Segments
If there are multiple peak segments with equal maximum total, the program will return the first one it finds, based on the tie rule above. This keeps the output deterministic and avoids ambiguity.

# b. Implementation in C++
```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;

int main() {
    int n;
    cout << "Enter number of months: ";
    cin >> n;

    if (n <= 0) {
        cout << "Invalid input. Number of months must be at least 1." << endl;
        return 1;
    }

    vector<string> monthNames(n);
    vector<long long> sales(n);

    cout << "Enter month names and sales values:" << endl;
    for (int i = 0; i < n; i++) {
        cin >> monthNames[i] >> sales[i];
    }

    long long bestSum = sales[0];
    long long currentSum = sales[0];

    int bestStart = 0;
    int bestEnd = 0;
    int currentStart = 0;

    for (int i = 1; i < n; i++) {
        if (currentSum + sales[i] < sales[i]) {
            currentSum = sales[i];
            currentStart = i;
        } else {
            currentSum += sales[i];
        }

        if (currentSum > bestSum) {
            bestSum = currentSum;
            bestStart = currentStart;
            bestEnd = i;
        }
    }

    cout << endl;
    cout << "Highest sales segment:" << endl;
    cout << "Start month: " << monthNames[bestStart] << endl;
    cout << "End month: " << monthNames[bestEnd] << endl;
    cout << "Maximum total sales: " << bestSum << " million USD" << endl;

    return 0;
}
```
## Example Input for the Given Dataset
```
12
Jan 100
Feb 113
Mar 110
Apr 85
May 81
Jun 101
Jul 94
Aug 106
Sep 105
Oct 102
Nov 86
Dec 63
```

## Example Output
```
Highest sales segment:
Start month: Jan
End month: Dec
Maximum total sales: 1146 million USD
```

# c. Pseudocode and Complexity
## Pseudocode
```
START

DECLARE n
PRINT "Enter number of months:"
READ n

IF n <= 0 THEN
    PRINT "Invalid input. Number of months must be at least 1."
    STOP
END IF

DECLARE monthNames as array of size n
DECLARE sales as array of size n

PRINT "Enter month names and sales values:"

FOR i = 0 TO n - 1
    READ monthNames[i], sales[i]
END FOR

DECLARE bestSum
DECLARE currentSum
DECLARE bestStart
DECLARE bestEnd
DECLARE currentStart

SET bestSum = sales[0]
SET currentSum = sales[0]
SET bestStart = 0
SET bestEnd = 0
SET currentStart = 0

FOR i = 1 TO n - 1
    IF currentSum + sales[i] < sales[i] THEN
        SET currentSum = sales[i]
        SET currentStart = i
    ELSE
        SET currentSum = currentSum + sales[i]
    END IF

    IF currentSum > bestSum THEN
        SET bestSum = currentSum
        SET bestStart = currentStart
        SET bestEnd = i
    END IF
END FOR

PRINT "Highest sales segment:"
PRINT "Start month:", monthNames[bestStart]
PRINT "End month:", monthNames[bestEnd]
PRINT "Maximum total sales:", bestSum

END
```

## Complexity Analysis
The algorithm runs in O(N) time because it only scans through the array once. Each month is processed exactly one time, and each step does only constant work.

The space complexity is O(N) if we store the month names and sales values in arrays or vectors. If we only cared about the numeric result and not the month labels, 
the algorithm itself could be done in O(1) extra space.

# d. Limitations
This approach is efficient, but it has some limitations.

First, it is sensitive to outliers. If one month has an unusually large sales value, that month can dominate the result and heavily influence which segment is selected.

Second, the tie breaking rule is somewhat arbitrary. In this solution, I keep the earliest segment when totals are equal, but another business situation might prefer the longest segment, 
the shortest segment, or the most recent one.

Third, the algorithm assumes that the goal is simply to maximize the sum of consecutive months. It does not consider seasonality, trends, or business context. For example, in real sales data, 
a company may care more about holiday peaks, quarter performance, or year over year growth than just the raw maximum segment.

Fourth, this method identifies only one best segment. If management wanted all strong peak windows, or wanted several local peaks instead of just the single maximum one, this algorithm would not be enough
by itself.

Finally, it does not smooth noise. If the sales data is very volatile, a different method such as moving averages, seasonal decomposition, or forecasting models might be more appropriate.

A different method would be preferable when the company wants prediction instead of historical analysis, when seasonality matters, or when the goal is to identify multiple important sales periods instead of 
only one maximum segment.

## Result for the Given Dataset
For the provided 2023 data, every monthly sales value is positive. That means adding more months always increases the total, so the best consecutive segment is the entire year.

- Start month: Jan
- End month: Dec
- Maximum total sales: 1146 million USD

