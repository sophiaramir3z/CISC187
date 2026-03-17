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
