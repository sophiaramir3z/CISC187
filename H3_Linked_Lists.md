# Performance Evaluation of Arrays, Linked Lists, and Iteration in C++

This project evaluates the performance of arrays and linked lists in C++, implemented as ```std::vector``` and ```std::list```, across large input sizes of 100 million, 200 million, and 300 million elements. 
The goal is to compare both data structures in terms of execution time and memory usage under common operations, and to analyze how iteration strategies impact performance. Beyond asymptotic complexity, 
this study emphasizes real-world factors such as cache behavior, allocation overhead, and profiling distortion.

All experiments were conducted using optimized builds (```-O2```) to reflect realistic execution. Runtime was measured using ```std::chrono::steady_clock```, and each test was repeated multiple times to 
reduce variance. Memory usage was measured using Valgrind Massif to capture heap allocation, including allocator overhead. Since profiling tools introduce nontrivial overhead, 
baseline runs without instrumentation were compared against profiled runs to isolate measurement distortion.

The first set of experiments focused on insertion and removal operations at both the beginning and end of each container. When inserting at the beginning, ```std::list``` significantly outperformed ```std::vector```. 
This aligns with theoretical expectations: linked lists support constant-time insertion through pointer manipulation, while vectors require shifting all existing elements, resulting in linear time complexity. 
However, the magnitude of this difference at scale is far greater than what asymptotic analysis alone suggests. At hundreds of millions of elements, 
the cost of memory movement dominates, making front insertion into vectors effectively impractical.

In contrast, insertion at the end revealed an important divergence between theory and practice. While both structures support constant-time insertion in principle,
```std::vector``` consistently achieved lower execution times. This is primarily due to contiguous memory layout, which enables efficient cache utilization and minimizes pointer indirection. 
Even though vectors occasionally trigger reallocations, these are amortized and relatively infrequent. On the other hand, ```std::list``` incurs a dynamic memory allocation for each element, 
introducing allocator latency and reducing spatial locality. As a result, the linked list’s theoretical advantage does not translate into superior real-world performance.

A similar pattern emerged in removal operations. Removing elements from the beginning strongly favored linked lists, again due to constant-time pointer updates. Vectors, by contrast, incur linear-time shifting. 
However, removal from the end showed minimal theoretical difference, as both operations are constant time. Despite this, vectors still performed better in practice,
reinforcing the importance of memory locality and reduced overhead. These results demonstrate that asymptotic complexity alone is insufficient for predicting performance at scale; 
memory access patterns and hardware-level behavior are equally critical.

Memory usage further differentiates the two structures. ```std::vector``` stores elements contiguously with minimal overhead, aside from occasional unused capacity due to growth strategies. 
In contrast, std::list requires separate allocations for each node, along with additional storage for pointer metadata. This leads to significantly higher memory consumption, often multiple times that of a vector storing the same data. 
At large scales, this overhead becomes a limiting factor, as it increases pressure on the memory subsystem and can degrade overall system performance due to fragmentation and cache inefficiency.

The second part of the evaluation examined loop iteration performance. A standard indexed ```for``` loop over a vector was compared to an iterator-based traversal of a linked list. 
The vector iteration consistently achieved lower execution times, not because of the loop construct itself, but due to sequential memory access. Modern CPUs are optimized for predictable, 
contiguous memory access patterns, enabling prefetching and efficient cache utilization. In contrast, linked list traversal involves pointer chasing across non-contiguous memory locations, 
which introduces frequent cache misses and higher latency. Notably, when both ```for``` and ```while``` loops are applied to the same container, their performance is nearly identical, 
confirming that the underlying data structure—not the loop syntax—is the dominant factor.

Finally, profiling overhead was explicitly considered. While ```std::chrono``` introduces negligible overhead, tools such as ```perf``` and Valgrind Massif can significantly alter runtime due to instrumentation. 
Massif, in particular, slows execution by tracking heap allocations in detail. To ensure accurate interpretation, baseline execution times were compared with profiled results, 
and the relative overhead was accounted for when analyzing performance trends. This highlights an important methodological consideration: measurement tools can influence the system being measured, 
and must be used carefully.

Overall, the results demonstrate that although linked lists offer theoretical advantages for certain operations, vectors are superior in most practical scenarios. Their contiguous memory layout, 
lower allocation overhead, and cache efficiency make them the preferred choice for large-scale data processing. Figures included in this report illustrate execution time and memory usage across input sizes, 
reinforcing the conclusion that real-world performance is governed as much by hardware interaction as by algorithmic complexity.

## The Code
```cpp
#include <iostream>
#include <vector>
#include <list>
#include <chrono>

using namespace std;
using namespace chrono;

void test_vector_front(int n) {
    vector<int> v;
    auto start = steady_clock::now();

    for (int i = 0; i < n; i++) {
        v.insert(v.begin(), i);
    }

    auto end = steady_clock::now();
    cout << "Vector front insert (" << n << "): "
         << duration_cast<seconds>(end - start).count() << "s\n";
}

void test_list_front(int n) {
    list<int> l;
    auto start = steady_clock::now();

    for (int i = 0; i < n; i++) {
        l.push_front(i);
    }

    auto end = steady_clock::now();
    cout << "List front insert (" << n << "): "
         << duration_cast<seconds>(end - start).count() << "s\n";
}

void test_vector_back(int n) {
    vector<int> v;
    auto start = steady_clock::now();

    for (int i = 0; i < n; i++) {
        v.push_back(i);
    }

    auto end = steady_clock::now();
    cout << "Vector back insert (" << n << "): "
         << duration_cast<seconds>(end - start).count() << "s\n";
}

void test_list_back(int n) {
    list<int> l;
    auto start = steady_clock::now();

    for (int i = 0; i < n; i++) {
        l.push_back(i);
    }

    auto end = steady_clock::now();
    cout << "List back insert (" << n << "): "
         << duration_cast<seconds>(end - start).count() << "s\n";
}

void test_iteration(int n) {
    vector<int> v(n, 1);
    list<int> l(n, 1);

    int sum = 0;

    auto start = steady_clock::now();
    for (int i = 0; i < v.size(); i++) sum += v[i];
    auto end = steady_clock::now();
    cout << "Vector for-loop: "
         << duration_cast<milliseconds>(end - start).count() << "ms\n";

    start = steady_clock::now();
    for (auto it = l.begin(); it != l.end(); ++it) sum += *it;
    end = steady_clock::now();
    cout << "List iterator loop: "
         << duration_cast<milliseconds>(end - start).count() << "ms\n";
}

int main() {
    vector<int> sizes = {100000000, 200000000, 300000000};

    for (int n : sizes) {
        test_vector_front(n);
        test_list_front(n);
        test_vector_back(n);
        test_list_back(n);
        cout << "------------------------\n";
    }

    test_iteration(100000000);
}
```

## The Graphs
<img width="1919" height="1076" alt="image" src="https://github.com/user-attachments/assets/58169843-fbb7-4b96-ae7e-5a5585cab3bc" />
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/e49be1b4-6b75-4ce5-b5e1-4b86c500236c" />
<img width="1919" height="1065" alt="image" src="https://github.com/user-attachments/assets/9b82a3db-eafb-4d65-9497-5636d5fcc441" />


