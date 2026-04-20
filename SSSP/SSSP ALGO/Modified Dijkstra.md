

## 1. Core Idea

**Modified Dijkstra** usually refers to a **lazy-update heap implementation** of Dijkstra.

It is designed to solve a practical implementation issue:

- standard textbook Dijkstra often assumes we can do `DecreaseKey` efficiently;
- but many built-in priority queues do **not** support this operation directly:
  - C++ `priority_queue`
  - Python `heapq`
  - Java `PriorityQueue`

So instead of modifying an existing key in the heap, we do this:

- when a shorter distance to a vertex is found, **push a new pair** into the heap;
- keep the old outdated pair inside the heap;
- when popping from the heap, ignore the entry if it is outdated.

This technique is called **Lazy Update**.

---

## 2. What Problem It Solves

It solves the **implementation problem of priority queue update**, not the theoretical issue of negative edges.

So Modified Dijkstra helps us avoid needing a true `DecreaseKey`, while still implementing Dijkstra efficiently on **non-negative weighted graphs**.

---

## 3. Lazy Update Mechanism

Suppose the heap contains pairs of the form:

- `(distance, vertex)`

When we find a better path to `v`:

- update `D[v]`
- push `(D[v], v)` into the heap again

Later, when an old entry is popped, we check:

`if popped_distance != D[vertex], skip it`

That means:

- the heap may contain several copies of the same vertex;
- only the one matching the current best `D[v]` is valid.

---

## 4. Pseudocode

```text
for each vertex u:
    D[u] = INF
D[s] = 0

priority queue pq
pq.push((0, s))

while pq is not empty:
    (d, u) = pq.pop()

    if d != D[u]:
        continue

    for each edge (u, v, w):
        if D[u] + w < D[v]:
            D[v] = D[u] + w
            pq.push((D[v], v))
```

---

## 5. Why It Works on Non-Negative Graphs

On graphs with **all edge weights non-negative**, Modified Dijkstra is just a standard engineering implementation of Dijkstra.

The correctness still comes from the classic Dijkstra greedy property:

> when the smallest tentative-distance vertex is extracted, its shortest distance is final.

This property holds only when all edge weights are non-negative.

So on non-negative graphs:

- correctness is preserved;
- time complexity remains essentially the same.

---

## 6. Time Complexity on Non-Negative Graphs

The usual bound is:

`O((V + E) log V)`

Sometimes it is also written as:

`O(E log E)`

Since in a simple graph:

`E <= V^2 => log E = O(log V)`

these are asymptotically treated as equivalent in this context.

---

## 7. Difference from Original Dijkstra

### Original textbook Dijkstra
- conceptually uses `DecreaseKey`;
- each vertex is thought of as having one priority queue record;
- cleaner theoretically.

### Modified Dijkstra
- no real `DecreaseKey`;
- may push the same vertex many times;
- outdated entries are ignored later.

So the **algorithmic idea** is the same on non-negative graphs, but the **implementation strategy** is different.

---

## 8. Example of Lazy Update

Suppose currently:

- `D[3] = 10`, so heap contains `(10, 3)`

Later we find a shorter path:

- `D[3] = 6`

We do **not** remove `(10, 3)` from the heap.  
We simply push:

- `(6, 3)`

Now the heap may contain both:

- `(6, 3)`
- `(10, 3)`

Since the heap always pops the smaller one first:

- `(6, 3)` will be processed first;
- when `(10, 3)` is popped later, we check:
  - `10 != D[3]`
  - so it is outdated and skipped.

---

## 9. Can Modified Dijkstra Handle Negative Edges?

This is the most confusing point.

### Short answer
**Not as a standard Dijkstra guarantee.**

Lazy update does **not** magically make Dijkstra valid for general negative-weight graphs.

---

## 10. Why Ordinary Dijkstra Fails with Negative Edges

Dijkstra relies on this claim:

> once a vertex with minimum tentative distance is extracted, its answer is final.

This fails if negative edges exist.

### Example

Edges:

- `s -> a = 2`
- `s -> b = 5`
- `b -> a = -10`

Initially:

- `D[a] = 2`
- `D[b] = 5`

Dijkstra may extract `a` first and think it is done.

But later:

- `s -> b -> a = 5 + (-10) = -5`

which is better.

So with negative edges, a vertex that looks final now may still improve later.

---

## 11. Why Modified Dijkstra Sometimes Still Appears to Work

If we allow:

- repeated insertion into the heap,
- repeated extraction,
- repeated propagation after improvement,

then even if a vertex was processed earlier, a later better path can still update it and push it again.

So in some graphs with negative edges but **no reachable negative cycle**, this process may eventually produce correct shortest distances.

However, this is no longer the classical Dijkstra proof.

It behaves more like a **label-correcting algorithm**.

---

## 12. Important Distinction

### Modified Dijkstra on non-negative graphs
This is standard Dijkstra with lazy-update heap implementation.

### Repeated-relaxation heap process on graphs with negative edges
This may still work in some cases, but it is no longer classical Dijkstra in the strict theoretical sense.

So do **not** say:

> lazy update makes Dijkstra correct for negative edges.

That statement is misleading.

---

## 13. What Happens If There Is a Negative Cycle

If a negative cycle is reachable from the source, then distances can keep decreasing forever.

That means:

- a vertex gets a smaller value;
- it is pushed again;
- later it gets even smaller;
- pushed again;
- and so on.

So the algorithm may never terminate.

---

## 14. Why Complexity Becomes Bad with Negative Edges

On non-negative graphs, each vertex is effectively finalized once, so complexity is well controlled.

With negative edges:

- a vertex may be improved many times;
- each improvement may trigger another heap insertion;
- each reprocessing may relax many outgoing edges again.

Therefore the running time can become much larger than:

`O((V + E) log V)`

and in specially constructed cases can be extremely bad.

---

## 15. Comparison with Bellman-Ford

### Modified Dijkstra
- excellent for **non-negative** weighted graphs;
- uses heap efficiently;
- fast in practice;
- no standard guarantee for negative-edge graphs.

### Bellman-Ford
- designed for graphs with **negative edges**;
- can detect negative cycles;
- guaranteed correct;
- complexity: `O(VE)`

So if negative edges are truly allowed in theory, the safe classical algorithm is usually **Bellman-Ford**, not Dijkstra.

---

## 16. Conceptual Interpretation

You can think of the two algorithms like this:

### Ordinary Dijkstra
"Once popped, finalized."

### Modified Dijkstra
"Once popped, process it if it is still the newest information."

That is the essence of lazy update.

---

## 17. Small Worked Example

Graph:

- `0 -> 1 = 1`
- `0 -> 2 = 10`
- `1 -> 3 = 2`
- `2 -> 3 = -10`
- `3 -> 4 = 3`

Source: `0`

### Step 1
Initialize:

- `D[0] = 0`
- others `= INF`

Heap:

- `(0, 0)`

### Step 2
Pop `(0, 0)`:

- relax `0 -> 1`, so `D[1] = 1`
- relax `0 -> 2`, so `D[2] = 10`

Heap:

- `(1, 1), (10, 2)`

### Step 3
Pop `(1, 1)`:

- relax `1 -> 3`, so `D[3] = 3`

Heap:

- `(3, 3), (10, 2)`

### Step 4
Pop `(3, 3)`:

- relax `3 -> 4`, so `D[4] = 6`

Heap:

- `(6, 4), (10, 2)`

### Step 5
Pop `(6, 4)`:
- no outgoing edges

Heap:

- `(10, 2)`

### Step 6
Pop `(10, 2)`:

- relax `2 -> 3`
- `10 + (-10) = 0 < 3`
- update `D[3] = 0`
- push `(0, 3)`

Heap:

- `(0, 3)`

### Step 7
Pop `(0, 3)`:

- relax `3 -> 4`
- `0 + 3 = 3 < 6`
- update `D[4] = 3`
- push `(3, 4)`

Heap:

- `(3, 4)`

### Step 8
Pop `(3, 4)` and finish.

Final result:

- `D[4] = 3`

This shows why repeated reprocessing can sometimes repair earlier suboptimal values.

---
