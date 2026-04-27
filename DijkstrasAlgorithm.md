## Why Dijkstra’s Algorithm Fails on Negative Weights

Dijkstra works because it assumes that once you reach a node with the smallest distance, that distance is final. This only makes sense if edge weights are non-negative. If there’s a negative edge, a shorter path might show up later, but Dijkstra won’t go back and fix it.

### Example

```
S → A (2)
S → B (5)
A → C (2)
B → C (-10)
```

Start at **S**

* A = 2, B = 5

Dijkstra picks **A first (2)**

* From A → C = 4

Now C = 4

Next it picks **C (4)** and finalizes it

Then it visits **B (5)**

* From B → C = 5 + (-10) = -5 (better path!)

But C is already finalized, so Dijkstra ignores this update.

### Bottom line

Dijkstra fails because it locks in answers too early. Negative edges break the idea that paths only get longer as you go.
