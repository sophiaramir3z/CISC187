# Implementing a Stack Using a Linked List in C++
## stack.h
```cpp
#ifndef STACK_H
#define STACK_H

#include <iostream>
using namespace std;

struct Node
{
    int data;
    Node* next;
};

class Stack
{
private:
    Node* top;

public:
    Stack();

    void push(int value);
    void pop();
    int peek();
    bool isEmpty();
    void display();
};

#endif
```

## stack.cpp
```cpp
#include "stack.h"

Stack::Stack()
{
    top = nullptr;
}

void Stack::push(int value)
{
    Node* newNode = new Node;
    newNode->data = value;
    newNode->next = top;
    top = newNode;
}

void Stack::pop()
{
    if (isEmpty())
    {
        cout << "Stack Underflow" << endl;
        return;
    }

    Node* temp = top;
    top = top->next;
    delete temp;
}

int Stack::peek()
{
    if (isEmpty())
    {
        cout << "Stack is empty" << endl;
        return -1;
    }

    return top->data;
}

bool Stack::isEmpty()
{
    return top == nullptr;
}

void Stack::display()
{
    if (isEmpty())
    {
        cout << "Stack is empty" << endl;
        return;
    }

    cout << "Stack elements:" << endl;

    Node* temp = top;
    while (temp != nullptr)
    {
        cout << temp->data << endl;
        temp = temp->next;
    }
}
```

## main.cpp
```cpp
#include "stack.h"

int main()
{
    Stack s;

    s.push(10);
    s.push(20);
    s.push(30);
    s.push(40);

    s.display();
    cout << endl;

    s.pop();

    cout << "Top element: " << s.peek() << endl;
    cout << endl;

    s.display();
    cout << endl;

    s.pop();
    s.pop();
    s.pop();
    s.pop();  // tests underflow

    return 0;
}
```
## Reflection Questions
## 1. Why is a linked list efficient for stack implementation?
A linked list is efficient for implementing a stack because stack operations happen only at one end, which is the top. In a singly linked list, inserting or deleting at the front takes constant time because no shifting of elements is needed. This makes it a natural fit for stack behavior.

## 2. What is the time complexity of push and pop operations?
Both push() and pop() run in O(1) time. This is because each operation only updates a few pointers and does not require traversal of the list.

## 3. What happens if memory is not deallocated after pop?
If memory is not deallocated after pop(), the program creates a memory leak. This means removed nodes still occupy memory even though they are no longer being used. Over time, this can waste memory and reduce program performance.

## 4. Compare a stack implemented with an array versus a linked list.
A stack implemented with an array is simple and allows direct indexing, but it usually has a fixed size unless dynamic resizing is used. A linked list stack can grow and shrink dynamically, which makes it more flexible. However, linked lists use extra memory for pointers, while arrays use memory more compactly.

## Conclusion
This lab demonstrates how a stack can be implemented using a singly linked list in C++. The linked list version is dynamic, efficient for insertion and deletion at the top, and a strong example of using pointers and dynamic memory allocation safely.
