# CISC 187 Project

## Task 1: Players in Both Sports
Approach:

To achieve O(N+M) runtime, use an `unordered_set` to store the full names of players from one sport. Then iterate through the second array and check whether each player exists in the set.

Code:
```cpp
#include <iostream>
#include <vector>
#include <unordered_set>
using namespace std;

struct Player {
    string first_name;
    string last_name;
    string team;
};

vector<string> playersInBothSports(vector<Player>& basketball,
                                   vector<Player>& football) {

    unordered_set<string> basketballSet;
    vector<string> result;

    for (auto& player : basketball) {
        basketballSet.insert(player.first_name + " " + player.last_name);
    }

    for (auto& player : football) {
        string fullName = player.first_name + " " + player.last_name;

        if (basketballSet.count(fullName)) {
            result.push_back(fullName);
        }
    }

    return result;
}
```
## Time Complexity
- Building hash set: O(N)
- Checking football players: O(M)
- Overall runtime: O(N+M)

## Task 2: Find the Missing Integer
Approach: 

The numbers should contain all integers from 0 to N. The expected sum is:

<img width="123" height="66" alt="image" src="https://github.com/user-attachments/assets/0ea6ea53-59f9-4432-99a4-e29c4478774c" />
	​

Subtract the actual array sum from the expected sum.

Code:
```cpp
#include <iostream>
#include <vector>
using namespace std;

int findMissingNumber(vector<int>& nums) {

    int n = nums.size();

    int expectedSum = n * (n + 1) / 2;

    int actualSum = 0;

    for (int num : nums) {
        actualSum += num;
    }

    return expectedSum - actualSum;
}
```
## Time Complexity - O(N)

## Task 3: Maximum Stock Profit
Approach:

Track:

- Minimum price seen so far
- Maximum profit so far

At each price:

- Calculate profit if sold today
- Update best profit

Code:

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

int maxProfit(vector<int>& prices) {

    int minPrice = INT_MAX;
    int maxProfit = 0;

    for (int price : prices) {

        if (price < minPrice) {
            minPrice = price;
        }

        int profit = price - minPrice;

        if (profit > maxProfit) {
            maxProfit = profit;
        }
    }

    return maxProfit;
}
```

## Time Complexity - O(N)

## Task 4: Highest Product of Two Numbers
Approach:

Negative numbers matter because:

(−10)×(−6)=60

Track:

- Largest number
- Second largest number
- Smallest number
- Second smallest number

Then compare:

- largest1×largest2

&

- smallest1×smallest2

```cpp
#include <iostream>
#include <vector>
#include <climits>
using namespace std;

int highestProduct(vector<int>& nums) {

    int largest1 = INT_MIN;
    int largest2 = INT_MIN;

    int smallest1 = INT_MAX;
    int smallest2 = INT_MAX;

    for (int num : nums) {

        if (num > largest1) {
            largest2 = largest1;
            largest1 = num;
        }
        else if (num > largest2) {
            largest2 = num;
        }

        if (num < smallest1) {
            smallest2 = smallest1;
            smallest1 = num;
        }
        else if (num < smallest2) {
            smallest2 = num;
        }
    }

    int positiveProduct = largest1 * largest2;
    int negativeProduct = smallest1 * smallest2;

    return max(positiveProduct, negativeProduct);
}
```

## Time Complexity - O(N)

## Task 5: Sorting Temperature Readings in O(N)
Approach:

Temperatures range only from:

- 97.0 to 99.0
- One decimal place

Possible values:

- 97.0
- 97.1
- ...
- 99.0

Only 21 possible values exist, so counting sort can be used.

Code: 

```cpp
#include <iostream>
#include <vector>
using namespace std;

vector<double> sortTemperatures(vector<double>& temps) {

    vector<int> counts(21, 0);

    for (double temp : temps) {

        int index = (temp - 97.0) * 10;

        counts[index]++;
    }

    vector<double> result;

    for (int i = 0; i < counts.size(); i++) {

        double value = 97.0 + (i / 10.0);

        for (int j = 0; j < counts[i]; j++) {
            result.push_back(value);
        }
    }

    return result;
}
```

## Time Complexity - O(N)

because the number of possible temperature values is constant.

## Task 6: Longest Consecutive Sequence
Approach:

Use an unordered_set for constant lookup time.

For each number:

- Only start counting if it is the beginning of a sequence
- Expand forward while consecutive numbers exist
Code:

```cpp
#include <iostream>
#include <vector>
#include <unordered_set>
using namespace std;

int longestConsecutiveSequence(vector<int>& nums) {

    unordered_set<int> numSet(nums.begin(), nums.end());

    int longest = 0;

    for (int num : nums) {

        if (!numSet.count(num - 1)) {

            int currentNum = num;
            int currentLength = 1;

            while (numSet.count(currentNum + 1)) {
                currentNum++;
                currentLength++;
            }

            longest = max(longest, currentLength);
        }
    }

    return longest;
}
```

## Time Complexity - O(N)

since each number is processed at most once.



