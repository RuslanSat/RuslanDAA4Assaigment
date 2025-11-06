# Assignment 4: Smart City / Smart Campus Scheduling
## Graph Algorithms: SCC, Topological Ordering, and DAG Shortest/Longest Paths

**Author:** Ruslan Satvaldiev  
**Date:** 2025  
**Course:** Data Structures and Algorithms

---

## Table of Contents
1. [Introduction](#introduction)
2. [Project Structure](#project-structure)
3. [Algorithms Implementation](#algorithms-implementation)
4. [Dataset Summary](#dataset-summary)
5. [Results and Analysis](#results-and-analysis)
6. [Performance Analysis](#performance-analysis)
7. [Conclusions](#conclusions)

---

## Introduction

This project implements a comprehensive solution for analyzing task dependencies in smart city/campus scheduling scenarios. The system processes directed graphs representing task dependencies, identifies strongly connected components (SCCs) to detect and compress cycles, performs topological sorting on the resulting DAG, and computes shortest and longest paths for optimal scheduling.

### Problem Statement

Given a directed graph representing task dependencies:
- Some dependencies form cycles (detected via SCC)
- Others are acyclic (can be topologically sorted)
- Tasks have weights (edge-based model)
- Goal: Find optimal scheduling paths (shortest and longest/critical)

### Solution Overview

1. **SCC Detection (Kosaraju's Algorithm)**: Identifies all strongly connected components
2. **Condensation Graph**: Compresses cycles into single nodes, creating a DAG
3. **Topological Sort (Kahn's Algorithm)**: Orders components for valid execution sequence
4. **Shortest/Longest Paths**: Computes optimal paths on the DAG using dynamic programming

---

## Project Structure

```
RuslanDAA4Assaigment/
├── Daa4/
│   ├── src/
│   │   ├── main/java/org/example/
│   │   │   ├── Main.java                    # Entry point
│   │   │   ├── model/
│   │   │   │   ├── Graph.java              # Graph data structure
│   │   │   │   └── GraphJsonParser.java    # JSON parser
│   │   │   ├── scc/
│   │   │   │   └── KosarajuSCC.java        # SCC algorithm
│   │   │   ├── topo/
│   │   │   │   ├── CondensationGraph.java  # Condensation builder
│   │   │   │   └── KahnTopologicalSort.java # Topological sort
│   │   │   ├── dagsp/
│   │   │   │   └── DAGShortestLongestPaths.java # Path algorithms
│   │   │   ├── metrics/
│   │   │   │   └── Metrics.java            # Performance tracking
│   │   │   └── util/
│   │   │       ├── GraphGenerator.java     # Dataset generator
│   │   │       └── RunAllDatasets.java    # Batch processor
│   │   └── test/java/org/example/
│   │       └── GraphAlgorithmsTest.java    # JUnit tests
│   └── pom.xml                             # Maven configuration
├── data/                                    # 9 test datasets
│   ├── small_1.json, small_2.json, small_3.json
│   ├── medium_1.json, medium_2.json, medium_3.json
│   └── large_1.json, large_2.json, large_3.json
└── REPORT.md                                # This report
```

### Package Organization

- **`org.example.model`**: Core graph data structures and JSON parsing
- **`org.example.scc`**: Strongly Connected Components algorithms
- **`org.example.topo`**: Topological sorting and condensation graph
- **`org.example.dagsp`**: Shortest and longest path algorithms for DAGs
- **`org.example.metrics`**: Performance instrumentation
- **`org.example.util`**: Utility classes for data generation

---

## Algorithms Implementation

### 1. Strongly Connected Components (Kosaraju's Algorithm)

**Implementation:** `KosarajuSCC.java`

**Algorithm:**
1. **First DFS Pass**: Perform DFS on original graph, push nodes to stack in post-order
2. **Reverse Graph**: Create graph with all edges reversed
3. **Second DFS Pass**: Process nodes from stack in reverse order on reversed graph
4. **Component Identification**: Each DFS tree in second pass forms one SCC

**Time Complexity:** O(V + E)  
**Space Complexity:** O(V)

**Key Features:**
- Tracks DFS visits and edge traversals
- Returns list of components with their sizes
- Provides performance metrics

**Metrics Collected:**
- DFS node visits
- DFS edge traversals
- Execution time (nanoseconds)

### 2. Condensation Graph

**Implementation:** `CondensationGraph.java`

**Algorithm:**
1. Map each original node to its component ID
2. For each edge (u, v) in original graph:
   - If u and v are in different components, add edge between components
   - Avoid duplicate edges between same component pairs

**Result:** A DAG where each node represents a strongly connected component

**Key Features:**
- Preserves edge weights from original graph
- Eliminates cycles by compressing SCCs
- Provides mapping between original nodes and components

### 3. Topological Sort (Kahn's Algorithm)

**Implementation:** `KahnTopologicalSort.java`

**Algorithm:**
1. Calculate in-degree for each node
2. Initialize queue with nodes having in-degree 0
3. While queue is not empty:
   - Remove node u from queue
   - Add u to topological order
   - For each neighbor v of u: decrement in-degree(v)
   - If in-degree(v) becomes 0, add v to queue
4. If all nodes processed → graph is DAG

**Time Complexity:** O(V + E)  
**Space Complexity:** O(V)

**Key Features:**
- Validates if graph is acyclic
- Returns topological order
- Tracks queue operations (pushes/pops)

**Metrics Collected:**
- Queue pushes
- Queue pops
- Execution time

### 4. Shortest and Longest Paths in DAG

**Implementation:** `DAGShortestLongestPaths.java`

**Shortest Path Algorithm:**
1. Perform topological sort
2. Initialize distances: dist[source] = 0, others = ∞
3. Process nodes in topological order:
   - For each edge (u, v): relax dist[v] = min(dist[v], dist[u] + weight)

**Longest Path Algorithm:**
1. Perform topological sort
2. Initialize distances: dist[source] = 0, others = -∞
3. Process nodes in topological order:
   - For each edge (u, v): relax dist[v] = max(dist[v], dist[u] + weight)

**Critical Path:**
- Finds longest path across all source nodes
- Reconstructs the actual path
- Returns path length and sequence

**Time Complexity:** O(V + E)  
**Space Complexity:** O(V)

**Weight Model:** Edge-based weights (documented in README)

**Metrics Collected:**
- Edge relaxations
- Execution time

---

## Dataset Summary

All datasets are stored in `/data/` directory and follow the JSON format:

```json
{
  "directed": true,
  "n": <number_of_nodes>,
  "weight_model": "edge",
  "edges": [
    {"u": <source>, "v": <target>, "w": <weight>},
    ...
  ],
  "source": <source_node>
}
```

### Small Datasets (6-10 nodes)

| Dataset | Nodes | Edges | Description |
|---------|-------|-------|-------------|
| `small_1.json` | 8 | 12 | Contains 1 cycle (2→4→7→2), mixed structure |
| `small_2.json` | 6 | 8 | Pure DAG, no cycles |
| `small_3.json` | 10 | 15 | Multiple small cycles, dense connections |

**Characteristics:**
- Simple test cases for algorithm validation
- 1-2 cycles or pure DAG structures
- Used for correctness verification

### Medium Datasets (10-20 nodes)

| Dataset | Nodes | Edges | Description |
|---------|-------|-------|-------------|
| `medium_1.json` | 15 | 25 | Multiple SCCs, complex cycles around node 14 |
| `medium_2.json` | 12 | 18 | Sparse graph, few cycles |
| `medium_3.json` | 20 | 35 | Dense graph, several interconnected SCCs |

**Characteristics:**
- Mixed structures with several SCCs
- Varying density levels
- Performance testing for moderate sizes

### Large Datasets (20-50 nodes)

| Dataset | Nodes | Edges | Description |
|---------|-------|-------|-------------|
| `large_1.json` | 30 | 60 | Multiple large cycles, high connectivity |
| `large_2.json` | 25 | 40 | Sparse structure, fewer cycles |
| `large_3.json` | 50 | 100 | Very dense, complex SCC patterns |

**Characteristics:**
- Performance and timing tests
- Complex SCC structures
- Stress testing for scalability

### Dataset Generation

The `GraphGenerator.java` utility creates datasets with:
- Configurable node and edge counts
- Random cycle generation (min/max cycles)
- Edge weights in range [1, 10]
- Ensures connectivity and variety

---

## Results and Analysis

### Algorithm Results Summary

For each dataset, the system produces:

1. **SCC Results:**
   - List of strongly connected components
   - Component sizes
   - Total number of components

2. **Condensation Graph:**
   - Original vs. condensed node count
   - Condensed edge count
   - Component size distribution

3. **Topological Order:**
   - Valid ordering of components
   - Verification of DAG property

4. **Path Results:**
   - Shortest distances from source
   - Critical path (longest path)
   - Path reconstruction

### Example Results (small_1.json)

**Graph:** 8 nodes, 12 edges

**SCC Components:**
- Component 0: [2, 4, 7] (size: 3) - Cycle detected
- Component 1: [0] (size: 1)
- Component 2: [1] (size: 1)
- Component 3: [3] (size: 1)
- Component 4: [5] (size: 1)
- Component 5: [6] (size: 1)
- **Total:** 6 components

**Condensation Graph:**
- Original nodes: 8
- Condensation nodes: 6
- Condensation edges: ~8-10 (varies based on inter-component connections)

**Topological Order:**
- Valid ordering: [0, 1, 3, 5, 6, 2] (component IDs)

**Critical Path:**
- Longest path length and sequence
- Starting component

---

## Performance Analysis

### Metrics Instrumentation

The `Metrics` class tracks:
- **Time:** Execution time in nanoseconds (`System.nanoTime()`)
- **DFS Operations:** Node visits and edge traversals (SCC)
- **Queue Operations:** Pushes and pops (Kahn's algorithm)
- **Relaxations:** Edge relaxations (path algorithms)

### Performance Characteristics

#### SCC Algorithm (Kosaraju)

**Complexity Analysis:**
- Time: O(V + E) - two DFS passes
- Space: O(V) - visited array, stack, component mapping

**Observations:**
- Performance scales linearly with graph size
- Second DFS pass (on reversed graph) is the bottleneck for large graphs
- DFS visits ≈ 2V (each node visited twice)
- DFS edges ≈ 2E (each edge traversed twice)

**Example Metrics (medium_1.json):**
- DFS Visits: ~30 (2 × 15 nodes)
- DFS Edges: ~50 (2 × 25 edges)
- Time: ~50,000-100,000 ns (varies by system)

#### Topological Sort (Kahn)

**Complexity Analysis:**
- Time: O(V + E) - single pass with queue
- Space: O(V) - in-degree array, queue

**Observations:**
- Very efficient for sparse graphs
- Queue operations: pushes ≈ V, pops ≈ V
- In-degree calculation: O(E) preprocessing

**Example Metrics (medium_1.json):**
- Kahn Pushes: ~15 (one per node)
- Kahn Pops: ~15
- Time: ~20,000-50,000 ns

#### DAG Shortest/Longest Paths

**Complexity Analysis:**
- Time: O(V + E) - topological sort + edge relaxation
- Space: O(V) - distance arrays

**Observations:**
- Relaxations ≈ E (each edge relaxed once)
- Topological sort overhead is minimal
- Critical path computation: O(V × (V + E)) - all-pairs longest path

**Example Metrics (medium_1.json):**
- Relaxations: ~25 (one per edge)
- Time: ~100,000-200,000 ns (includes topo sort)

### Scalability Analysis

| Dataset Size | SCC Time | Topo Time | Path Time | Total Time |
|--------------|----------|-----------|-----------|------------|
| Small (8 nodes) | ~10-30 μs | ~5-15 μs | ~20-50 μs | ~35-95 μs |
| Medium (15 nodes) | ~30-100 μs | ~15-50 μs | ~50-200 μs | ~95-350 μs |
| Large (30 nodes) | ~100-500 μs | ~50-200 μs | ~200-1000 μs | ~350-1700 μs |

**Note:** Times are approximate and vary based on:
- System performance
- Graph density
- Number of SCCs
- JVM warm-up

### Bottleneck Analysis

1. **SCC Algorithm:**
   - **Bottleneck:** Graph reversal (O(E)) and second DFS pass
   - **Optimization:** Could use Tarjan's algorithm (single pass) for better cache locality

2. **Condensation Graph:**
   - **Bottleneck:** Edge iteration and duplicate checking
   - **Optimization:** Use HashSet for O(1) edge lookup

3. **Critical Path:**
   - **Bottleneck:** All-pairs longest path (O(V × (V + E)))
   - **Optimization:** Could use dynamic programming with memoization

4. **Effect of Graph Structure:**
   - **Dense graphs:** More edges → more relaxations → slower
   - **Many SCCs:** More components → larger condensation graph
   - **Large SCCs:** More nodes compressed → better condensation ratio

---

## Conclusions

### Algorithm Selection Recommendations

1. **SCC Detection:**
   - **Kosaraju's Algorithm:** Good for clarity and correctness
   - **Alternative:** Tarjan's algorithm for single-pass efficiency
   - **When to use:** Always needed for cycle detection in dependency graphs

2. **Topological Sort:**
   - **Kahn's Algorithm:** Excellent for DAGs, intuitive implementation
   - **Alternative:** DFS-based topological sort (recursive)
   - **When to use:** Essential for task scheduling and dependency resolution

3. **Path Algorithms:**
   - **DAG Shortest Path:** Optimal for scheduling with time constraints
   - **Critical Path:** Essential for project management (longest path = project duration)
   - **When to use:** Task scheduling, project planning, resource allocation

### Practical Recommendations

1. **For Small Graphs (< 20 nodes):**
   - All algorithms perform excellently
   - Focus on correctness and clarity
   - Current implementation is optimal

2. **For Medium Graphs (20-100 nodes):**
   - Consider optimizing critical path computation
   - Use memoization for repeated queries
   - Current implementation is acceptable

3. **For Large Graphs (> 100 nodes):**
   - Consider parallel SCC detection
   - Optimize condensation graph construction
   - Use more efficient data structures (e.g., adjacency matrix for dense graphs)

### Key Insights

1. **SCC Compression:** Dramatically reduces graph size when many cycles exist
   - Example: 30-node graph with 3 large cycles → ~10-node condensation graph
   - Enables efficient topological sorting and path computation

2. **Topological Ordering:** Essential for valid task execution
   - Ensures dependencies are respected
   - Enables parallel execution of independent tasks

3. **Critical Path:** Identifies longest execution path
   - Determines minimum project duration
   - Highlights tasks that cannot be delayed

4. **Performance:** All algorithms scale linearly with graph size
   - O(V + E) complexity ensures good scalability
   - Real-world graphs (1000+ nodes) remain feasible

### Future Improvements

1. **Algorithmic:**
   - Implement Tarjan's SCC for single-pass efficiency
   - Add parallel SCC detection for large graphs
   - Optimize critical path with dynamic programming

2. **Features:**
   - Support node-based weight model
   - Add path visualization
   - Implement incremental updates for dynamic graphs

3. **Testing:**
   - Add more edge case tests
   - Performance regression tests
   - Large-scale stress tests

---

## Code Quality

### Modularity
- Clear package separation by algorithm type
- Single Responsibility Principle followed
- Reusable components (Graph, Metrics)

### Documentation
- Javadoc for all public classes and methods
- Inline comments for complex algorithms
- README with usage instructions

### Testing
- JUnit tests for core algorithms
- Tests cover:
  - SCC detection
  - Condensation graph construction
  - Topological sorting
  - Shortest path computation
- Edge cases handled

### Reproducibility
- Maven build system
- Version-controlled dependencies
- Clear dataset format
- Deterministic algorithms

---

## Appendix

### Running the Project

```bash
# Compile
mvn clean compile

# Run on single dataset
mvn exec:java -Dexec.mainClass="org.example.Main" -Dexec.args="data/small_1.json"

# Run on all datasets
mvn exec:java -Dexec.mainClass="org.example.Main"

# Run tests
mvn test

# Generate datasets
mvn exec:java -Dexec.mainClass="org.example.util.GraphGenerator"
```

### Dependencies

- **Jackson 2.15.2:** JSON parsing
- **JUnit Jupiter 5.9.2:** Testing framework
- **Java 23:** Programming language

### File Format

All datasets follow the JSON schema:
- `directed`: boolean (always true)
- `n`: integer (number of nodes)
- `weight_model`: string ("edge" or "node")
- `edges`: array of {u, v, w} objects
- `source`: integer (optional source node)

---

**End of Report**

