# LEETCODE-Graphs-1579
Your implementation of the Union-Find data structure and the solution to the problem looks solid. The provided code performs the necessary operations for finding the maximum number of edges that can be removed while still maintaining the graph's connectivity for both Alice and Bob. Here’s a detailed walkthrough:

### Union-Find Class
The `UnionFind` class is designed to perform union-find operations with path compression and union by rank. Here's a breakdown of its components:

1. **Constructor**:
   ```java
   public UnionFind(int n) {
     count = n;
     id = new int[n];
     rank = new int[n];
     for (int i = 0; i < n; ++i)
       id[i] = i;
   }
   ```
   - Initializes the `id` array where each element is its own root.
   - Initializes the `rank` array to keep track of the tree's depth.
   - Sets the initial count of disjoint sets.

2. **Union by Rank**:
   ```java
   public boolean unionByRank(int u, int v) {
     final int i = find(u);
     final int j = find(v);
     if (i == j)
       return false;
     if (rank[i] < rank[j]) {
       id[i] = j;
     } else if (rank[i] > rank[j]) {
       id[j] = i;
     } else {
       id[i] = j;
       ++rank[j];
     }
     --count;
     return true;
   }
   ```
   - Finds the roots of `u` and `v`.
   - If they are already in the same set, it returns `false`.
   - Otherwise, it unites the sets by linking the root of the smaller tree to the root of the larger tree (or arbitrarily if they are of equal rank) and decreases the count of disjoint sets.

3. **Find with Path Compression**:
   ```java
   private int find(int u) {
     return id[u] == u ? u : (id[u] = find(id[u]));
   }
   ```
   - Recursively finds the root of `u` and compresses the path by making the nodes point directly to the root.

4. **Get Count**:
   ```java
   public int getCount() {
     return count;
   }
   ```

### Solution Class
The `Solution` class uses two instances of `UnionFind`, one for Alice and one for Bob, to determine the maximum number of removable edges:

1. **Sorting Edges**:
   ```java
   Arrays.sort(edges, (a, b) -> b[0] - a[0]);
   ```
   - Sorts edges so that type 3 edges (shared by both Alice and Bob) are processed first.

2. **Processing Edges**:
   ```java
   for (int[] edge : edges) {
     final int type = edge[0];
     final int u = edge[1] - 1;
     final int v = edge[2] - 1;
     switch (type) {
       case 3:
         if (alice.unionByRank(u, v) | bob.unionByRank(u, v))
           ++requiredEdges;
         break;
       case 2:
         if (bob.unionByRank(u, v))
           ++requiredEdges;
         break;
       case 1:
         if (alice.unionByRank(u, v))
           ++requiredEdges;
         break;
     }
   }
   ```

3. **Result Calculation**:
   ```java
   return alice.getCount() == 1 && bob.getCount() == 1 ? edges.length - requiredEdges : -1;
   ```
   - Checks if both Alice's and Bob's graphs are fully connected.
   - Returns the number of removable edges if both graphs are connected, otherwise returns `-1`.

### Potential Improvements
- Adding `break` statements after cases `2` and `1` to ensure that each case is properly terminated:
  ```java
  case 2:
    if (bob.unionByRank(u, v))
      ++requiredEdges;
    break;
  case 1:
    if (alice.unionByRank(u, v))
      ++requiredEdges;
    break;
  ```

This ensures that the `switch` statement does not fall through unintentionally.
