# Graph Theory

Brief Introduction

`Graph Theory` is the mathematical theory of the properties and
applications of graphs (networks).

The goal of this series is to gain an understanding of how to apply
graph theory to real world applications.

A graph theory problem might be: Given the constraints above, how many different sets of clothing can I make by choosing an article from each category?

The canonical graph theory example is a social network of friends.

This enables interesting questions such as: how many friends does person X have? Or how many degrees of separation are there between person X and person Y?

## Types of Graphs

### Undirected Graph

An `undirected` graph is a graph in which edges have no orientation. The edge (u, v) is identical to the edge (v, u). - Wiki

In the graph above, the nodes could represent cities and an edge could represent a bidirectional road.

### Directed Graph (digraph)

A `directed graph` or `digraph` is a graph in which edges have orientations. For example, the edge (u, v) is the edge from node `u` to node `v`.

In the graph above, the nodes could represent people and edge (u, v) could represent that person `u` bought person `v` a gift.

### Weighted Graphs

Many graphs can have edges that contain a certain weight to represent an
arbitrary value such as cost, distance, quantity, etc...

`Note:` I will usually denote an edge of such a graph as a triplet (u,
v, w) and specify whether the graph is directed or undirected.

### Special Graphs

A `tree` is an `undirected graph with no cycles`. Equivalently, it is a connected graph with `N` nodes and `N-1` edges.

A `rooted tree` is a tree with `a designated root node` where every edge either points away from or towards the root node. When edges point away from the root the graph is called an `arborescence (out-tree)` and `anti-arborescence (in-tree)` otherwise.

### Directed Acyclic Graphs (DAGs)

DAGs are `directed graphs with no cycles`. These graphs play an important role in representing structures with dependencies. Several efficient algorithms exist to operates on DAGs.

Cool fact: All out-trees are DAGs but not all DAGs are out-trees.

### Bipartite Graph

A `bipartite graph` is one whose vertices can be split into two independent groups `U`, `V` such that every edge connects betweens `U` and `V`.

Other definitions exist such as: The graph is two colourable or there is no odd length cycle.

### Complete Graphs

A `complete graph` is one where there is a unique edge between every pair
of nodes. A complete graph with `n` vertices is denoted as the graph
`Kn`.

All edges connected and worse case possible graph.

## Representing Graphs

Now just the type of graph but what type of data structure are we representing our graph with.

### Adjacency Matrix

An `adjacency matrix` `m` is a very simple way to represent a graph. The idea is that the cell `m[i][j]` represents the edge weight of going from node `i` to node `j`.

Note: It is often assumed that the edge of going from a node to itself has a cost of zero.

##### Pros

* Space efficient for representing dense graphs
* edge weight lookup is O(1)
* Simplest graph representation

##### Cons

* Requires O(V^2) space
* Iterating over all edges takes O(V^2) time

---

### Adjacency List

An `adjacency list` is a way to represent a graph as a map from nodes to lists of edges.

```
A -> [(B,4),(C,1)]
A -> [(C,6)]
A -> [(A,4),(B,1),(D,2)]
D -> []
```

###### Pros

* Space efficient for representing sparse graphs
* Iterating over all edges is efficient

###### Cons

* Less space efficient for denser graphs.
* Edge weight lookup is O(E)
* Slightly more complex graph representation

---

### Edge List

An `edge list` is a way to represent a graph simply as an unordered list of edges. Assume the notation for any triplet `(u,v,w)` means: `the cost from node u to node v is w`

```
[(C,A,4), (A,C,1),
 (B,C,6), (A,B,4),
 (C,B,1), (C,D,2)]
```

This representation is seldomly used because of its lack of structure. However it is conceptually simple and practical in a handful of algorithms.

###### Pros

* Space efficient for representing sparse graphs
* Iterating over all edges is efficient
* Very simple structure

###### Cons

* Less space efficient for denser graphs.
* Edge weight lookup is O(E)

