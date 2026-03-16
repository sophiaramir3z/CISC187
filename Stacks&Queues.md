# Stacks and Queues
# 1. Stack operations on S[1..6]
Assume the stack starts empty, so:
- S.top = 0
- array S[1..6] = [_, _, _, _, _, _]
## Initial state
- top = 0
- S = [_, _, _, _, _, _]
## PUSH(S, 4)
- top = 1
- S = [4, _, _, _, _, _]
- Stack contents: bottom 4 top
## PUSH(S, 1)
- top = 2
- S = [4, 1, _, _, _, _]
- Stack contents: 4, 1
## PUSH(S, 3)
- top = 3
- S = [4, 1, 3, _, _, _]
- Stack contents: 4, 1, 3
## POP(S)
- returned value: 3
- top = 2
- array still physically looks like: S = [4, 1, 3, _, _, _]
- Logical stack contents: 4, 1
- The 3 is no longer part of the stack even though it still remains in the array cell.
## PUSH(S, 8)
- top = 3
- S = [4, 1, 8, _, _, _]
- Stack contents: 4, 1, 8
## POP(S)
- returned value: 8
- top = 2
- array still physically looks like: S = [4, 1, 8, _, _, _]
- Logical stack contents: 4, 1
## Final stack state
- top = 2
- logical contents: 4, 1
- array: S = [4, 1, 8, _, _, _]

# 2. Queue operations on Q[1..6]
Assume the queue starts empty, so:
- Q.head = 1
- Q.tail = 1
- Q[1..6] = [_, _, _, _, _, _]
- This is a circular queue.
## Initial state
- head = 1
- tail = 1
- Q = [_, _, _, _, _, _]
- Queue is empty.
## ENQUEUE(Q, 4)
- Store 4 at Q[tail] = Q[1], then move tail.
- head = 1
- tail = 2
- Q = [4, _, _, _, _, _]
- Logical queue: 4
## ENQUEUE(Q, 1)
- Store 1 at Q[2], then move tail.
- head = 1
- tail = 3
- Q = [4, 1, _, _, _, _]
- Logical queue: 4, 1
## ENQUEUE(Q, 3)
- Store 3 at Q[3], then move tail.
- head = 1
- tail = 4
- Q = [4, 1, 3, _, _, _]
- Logical queue: 4, 1, 3
## DEQUEUE(Q)
- Remove element at Q[head] = Q[1], then move head.
- returned value: 4
- head = 2
- tail = 4
- Q = [4, 1, 3, _, _, _]
- Logical queue: 1, 3
## ENQUEUE(Q, 8)
- Store 8 at Q[4], then move tail.
- head = 2
- tail = 5
- Q = [4, 1, 3, 8, _, _]
- Logical queue: 1, 3, 8
## DEQUEUE(Q)
- Remove element at Q[2], then move head.
- returned value: 1
- head = 3
- tail = 5
- Q = [4, 1, 3, 8, _, _]
- Logical queue: 3, 8
## Final queue state
- head = 3
- tail = 5
- logical contents: 3, 8
- array: Q = [4, 1, 3, 8, _, _]

# 3. Rewrite ENQUEUE and DEQUEUE to detect overflow and underflow
To detect errors in a circular queue, one position is left unused. That means:
- the queue is empty when head == tail
- the queue is full when advancing tail would make it equal to head
```
ENQUEUE(Q, x) with overflow check
ENQUEUE(Q, x)
    next = Q.tail + 1
    if next > Q.length
        next = 1

    if next == Q.head
        error "overflow"

    Q[Q.tail] = x
    Q.tail = next
```
DEQUEUE(Q) with underflow check
```
DEQUEUE(Q)
    if Q.head == Q.tail
        error "underflow"

    x = Q[Q.head]

    if Q.head == Q.length
        Q.head = 1
    else
        Q.head = Q.head + 1

    return x
```
Both operations still take O(1) time.

# 4. O(1) procedures for a deque implemented by an array
A deque allows insertion and deletion at both the front and the rear. We can implement it using a circular array with:
- D[1..n]
- D.head
- D.tail
  As with the circular queue, one array cell is left unused to detect overflow.
- empty when D.head == D.tail
- full when moving tail forward would make it equal to head

## INSERT-FRONT(D, x)
```
INSERT-FRONT(D, x)
    newHead = D.head - 1
    if newHead < 1
        newHead = D.length

    if newHead == D.tail
        error "overflow"

    D.head = newHead
    D[D.head] = x
```
## INSERT-REAR(D, x)
```
INSERT-REAR(D, x)
    next = D.tail + 1
    if next > D.length
        next = 1

    if next == D.head
        error "overflow"

    D[D.tail] = x
    D.tail = next
```
## DELETE-FRONT(D)
```
DELETE-FRONT(D)
    if D.head == D.tail
        error "underflow"

    x = D[D.head]

    if D.head == D.length
        D.head = 1
    else
        D.head = D.head + 1

    return x
```
## DELETE-REAR(D)
```
DELETE-REAR(D)
    if D.head == D.tail
        error "underflow"

    if D.tail == 1
        D.tail = D.length
    else
        D.tail = D.tail - 1

    return D[D.tail]
```
# Summary

For the stack, the sequence of operations leaves the logical contents as 4, 1, with top = 2. For the queue, the sequence leaves the logical contents as 3, 8, with head = 3 and tail = 5. 
Queue overflow and underflow can be detected by checking whether the circular structure is full or empty before insertion or deletion. 
A deque can also be implemented in O(1) time per operation using a circular array and four procedures: insert front, insert rear, delete front, and delete rear.
