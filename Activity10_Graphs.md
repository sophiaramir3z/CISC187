# Activity 10 - Graphs

## Task 1: Theoretical Graph

I created an undirected graph with 6 vertices:

A, B, C, D, E, F

The edges are:

A-B  
A-C  
B-D  
B-E  
C-F  
E-F  

The graph looks like this:
<img width="2048" height="1011" alt="image" src="https://github.com/user-attachments/assets/999b7833-1690-4252-b9f5-ae89759cdeab" />


## Task 2: C++ Implementation of BFS and DFS

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <stack>
using namespace std;

class Graph {
private:
    int vertices;
    vector<vector<int>> adjList;

public:
    Graph(int v) {
        vertices = v;
        adjList.resize(v);
    }

    void addEdge(int u, int v) {
        adjList[u].push_back(v);
        adjList[v].push_back(u);
    }

    void BFS(int start) {
        vector<bool> visited(vertices, false);
        queue<int> q;

        visited[start] = true;
        q.push(start);

        cout << "BFS Traversal: ";

        while (!q.empty()) {
            int current = q.front();
            q.pop();

            cout << char(current + 'A') << " ";

            for (int neighbor : adjList[current]) {
                if (!visited[neighbor]) {
                    visited[neighbor] = true;
                    q.push(neighbor);
                }
            }
        }

        cout << endl;
    }

    void DFS(int start) {
        vector<bool> visited(vertices, false);
        stack<int> s;

        s.push(start);

        cout << "DFS Traversal: ";

        while (!s.empty()) {
            int current = s.top();
            s.pop();

            if (!visited[current]) {
                visited[current] = true;
                cout << char(current + 'A') << " ";

                for (int neighbor : adjList[current]) {
                    if (!visited[neighbor]) {
                        s.push(neighbor);
                    }
                }
            }
        }

        cout << endl;
    }
};

int main() {
    Graph g(6);

    g.addEdge(0, 1); // A-B
    g.addEdge(0, 2); // A-C
    g.addEdge(1, 3); // B-D
    g.addEdge(1, 4); // B-E
    g.addEdge(2, 5); // C-F
    g.addEdge(4, 5); // E-F

    g.BFS(0);
    g.DFS(0);

    return 0;
}
```
## Task 3: Big O Comparison

Breadth-first search and depth-first search both visit every vertex and edge once when using an adjacency list.

BFS time complexity: O(V + E)
DFS time complexity: O(V + E)

V means the number of vertices.
E means the number of edges.

BFS uses a queue and explores the graph level by level. It is useful for finding the shortest path in an unweighted graph.

DFS uses a stack and explores as far as possible down one path before backtracking. It is useful for checking connected components, detecting cycles, and exploring all possible paths.

Both algorithms have the same Big O time complexity, but they search the graph in different ways.
