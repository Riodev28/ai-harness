# Data structures and algorithms

Consult this when you choose how to hold, index or process data. Start from the access pattern: what the code does most often, how much data there is, and how fast it grows. Pick the structure that makes the dominant operation cheap, and state its complexity in the design note.

## Choosing a structure

| Dominant need | Use | Cost |
|---|---|---|
| Ordered sequence, access by index, append | Dynamic array (`List`, `ArrayList`, `list`) | O(1) index and amortized append; O(n) insert in the middle |
| Membership test, dedup | Hash set | O(1) average |
| Lookup by key | Hash map | O(1) average |
| Keys kept sorted, range queries, floor/ceiling | Balanced tree map or set (`TreeMap`, `SortedDict`) | O(log n) |
| FIFO work, BFS | Queue or deque (`ArrayDeque`, `collections.deque`) | O(1) at both ends |
| LIFO, undo, DFS, parsing | Stack (a deque) | O(1) |
| Always taking the min or max next: scheduling, top-k, Dijkstra | Binary heap / priority queue | O(log n) push and pop, O(1) peek |
| Top-k out of n items | A size-k heap | O(n log k) |
| Insertion order plus O(1) lookup | Linked hash map | O(1) |
| LRU cache | Hash map + doubly linked list (`LinkedHashMap` with access order) | O(1) get and put |
| Prefix search, autocomplete | Trie | O(length of key) |
| Connectivity, grouping, cycle detection | Union-find with path compression | ≈ O(1) amortized |
| Relationships, dependencies | Graph as an adjacency list. Use a topological sort for ordering and BFS/DFS for traversal | O(V + E) |
| Fixed-size recent window, rate counters | Ring buffer | O(1) |
| Probabilistic membership at huge scale | Bloom filter (it can return false positives, never false negatives) | O(k) |
| Approximate count of distinct items | HyperLogLog | O(1) |

## Rules

- **Collection-wide operations run in O(n) or O(n log n).** When a scan sits inside another scan, build a map or set first. A nested loop over two large collections is the classic hidden O(n²).
- **Keep data structures immutable at API boundaries.** Return read-only views or copies, and accept the narrowest interface the code needs (`Iterable` or `Collection` rather than `ArrayList`).
- **Bound memory.** Stream or paginate large datasets instead of loading them whole. Caches have a size and an eviction policy.
- **Choose by measurement once the obvious choice is in place.** Profile before micro-optimizing, and write down any optimization that makes the code less clear.
- **Use the standard library.** Write a custom structure only when no library type fits, and put tests around it.

## The database is a data structure too

- Index the columns used in `WHERE`, `JOIN` and `ORDER BY`. Composite indexes follow the leftmost-prefix rule. Check query plans with `EXPLAIN`.
- Normalize for writes and integrity, and denormalize deliberately for read paths you have measured.
- Paginate with keysets (`WHERE id > :last ORDER BY id LIMIT n`) rather than large `OFFSET`s.
- Pick column types that encode invariants: `NOT NULL`, `UNIQUE`, foreign keys, check constraints, and decimal types for money (never floats).
