## Problem 1

1. Replace each edge weight $w(i, j)$ with $w'(i, j) = -\log(w(i, j))$.

2. Use the **Bellman-Ford algorithm** to detect negative cycles in the transformed graph in $O(V \cdot E)$ time.

```python
def find_opportunity_cycle(V, A, w):
    import math
    from collections import defaultdict

    transformed_w = {(u, v): -math.log(w[u, v]) for (u, v) in A}

    distance = {v: float('inf') for v in V}
    parent = {v: None for v in V}
    start = next(iter(V))
    distance[start] = 0

    for _ in range(len(V) - 1):
        for u, v in A:
            if distance[u] + transformed_w[(u, v)] < distance[v]:
                distance[v] = distance[u] + transformed_w[(u, v)]
                parent[v] = u

    for u, v in A:
        if distance[u] + transformed_w[(u, v)] < distance[v]:
            # Negative cycle detected, reconstruct cycle
            cycle = []
            visited = set()
            current = v
            while current not in visited:
                visited.add(current)
                current = parent[current]
            cycle_start = current
            cycle = [cycle_start]
            current = parent[cycle_start]
            while current != cycle_start:
                cycle.append(current)
                current = parent[current]
            cycle.append(cycle_start)
            cycle.reverse()
            return cycle

    return "No opportunity cycle exists"
```

## Problem 2

### Construct the bipartite graph $G$

- Define $A = \{1, 2, \dots, n\}$ as the set of row indices.
- Define $B = \{1, 2, \dots, n\}$ as the set of column indices.
- Define $E$ such that $(i, j) \in E$ if $P_{i,j} > 0$.

### Verify Hall’s condition

For any subset $S \subseteq A$, we need to show $|N(S)| \geq |S|$:

- The row sums of $P$ imply that $\sum_{j \in N(S)} \sum_{i \in S} P_{i,j} \geq |S|$, as each row of $P$ sums to 1.
- Each $j \in N(S)$ is connected to $i \in S$ only if $P_{i,j} > 0$, ensuring $|N(S)| \geq \sum_{j \in N(S)} \sum_{i \in S} P_{i,j}$.
- Therefore, $|N(S)| \geq |S|$.

### Step 3: Existence of a perfect matching

By Hall's theorem, there exists a perfect matching in $G$. This perfect matching corresponds to a permutation matrix $Q$ where:

- $Q_{i,j} = 1$ if and only if $(i, j) \in E$.
- Since $(i, j) \in E$ only if $P_{i,j} > 0$, the property $\{(i, j) \mid Q_{i,j} = 1\} \subseteq \{(i, j) \mid P_{i,j} > 0\}$ holds.

### Conclusion

Using Hall's marriage theorem, we have shown that for any doubly stochastic matrix $P$, there exists a permutation matrix $Q$ such that $\{(i, j) \mid Q_{i,j} = 1\} \subseteq \{(i, j) \mid P_{i,j} > 0\}$.
