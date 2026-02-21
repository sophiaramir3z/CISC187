# Sorting algorithms

## Video Explanation: https://sdccd.us-west-2.instructuremedia.com/embed/12bbb477-a596-461d-a80b-99f173e2d34f

## Task 1

Algorithm takes 4N + 16 steps:
Big O ignores constants and lower order terms. The dominant term is 4N, which grows linearly with N, so the time complexity is O(N).

## Task 2

Algorithm takes 2N² steps:
Big O ignores constants. The dominant term is N², so the time complexity is O(N²).

## Task 3

double_then_sum(array)
```cpp
def double_then_sum(array) 
	doubled_array = []

	array.each do |number| 
		doubled_array << number *= 2
	end

	sum = 0

	doubled_array.each do |number| 
		sum += number
	end
	return sum 
end
```
This does two full passes over the data. The first each loops through N elements to build doubled_array. 
The second each loops through N elements to sum them. That is N + N steps in terms of loops, which is linear overall. 
Time complexity is O(N).

## Task 4

multiple_cases(array)
```cpp
def multiple_cases(array) 
	array.each do |string|
		puts string.upcase 
		puts string.downcase 
		puts string.capitalize
	end 
end
```
This loops through N strings once. Inside the loop it does three constant count operations per string. Big O treats that as a constant multiplier, so the time complexity is O(N).
## Task 5

every_other(array)
```cpp
def every_other(array) 
	array.each_with_index do |number, index|
		if index.even?
			array.each do |other_number|
            	puts number + other_number
			end 
		end
	end 
end
```
The outer loop runs N times. About half the indices are even, so the inner loop runs about N/2 times, and each time it loops through N elements. 
That is roughly (N/2) · N = N²/2 total inner work, which is quadratic growth. Big O drops the 1/2 constant, so the time complexity is O(N²).
