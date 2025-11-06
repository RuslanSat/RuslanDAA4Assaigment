# Smart City Scheduling — Graph Algorithms Report
# Author - "RUSLAN SATVALDIEV"

## Overview
This project combines two major topics of the course within a unified practical scenario related to smart-city and smart-campus task scheduling:

1. **Strongly Connected Components (SCC) & Condensation**
2. **Topological Ordering of DAGs**
3. **Shortest and Longest Paths in DAGs**

The system processes directed task-dependency graphs, detects cycles, compresses strongly connected components into a condensation DAG, and computes optimal execution orders and critical paths.  
All datasets and algorithms are implemented according to the requirements of Assignment 4.

---

## 1. Dataset Summary
A total of **9 datasets** were created (small/medium/large), covering both cyclic and acyclic structures with varying densities.

| Dataset   | Nodes | Edges | Type    | SCCs | Largest SCC | Critical Path |
|-----------|-------|--------|----------|------|--------------|----------------|
| small_1   | 8     | 12     | Cyclic  | 5    | 4            | 9              |
| small_2   | 6     | 8      | DAG     | 6    | 1            | 13             |
| small_3   | 10    | 15     | Cyclic  | 2    | 9            | 0              |
| medium_1  | 15    | 27     | Cyclic  | 4    | 12           | 11             |
| medium_2  | 12    | 18     | Cyclic  | 4    | 9            | 15             |
| medium_3  | 20    | 36     | Cyclic  | 5    | 16           | 16             |
| large_1   | 30    | 61     | Cyclic  | 5    | 26           | 10             |
| large_2   | 25    | 40     | Cyclic  | 7    | 19           | 19             |
| large_3   | 50    | 101    | Cyclic  | 13   | 38           | 22             |

**Weight Model:** edge-based weights from 1 to 10.

All datasets follow the required structure under `/data`.

---

## 2. Implemented Algorithms

### ✅ 2.1 Strongly Connected Components (Kosaraju)
- Input: directed dependency graph in JSON format.
- Output: SCCs (each listed as a set of vertices) and their sizes.
- The SCCs are compressed into a **condensation graph**, which is a DAG.

**Performance Example:**

| Dataset   | Time (ns) | DFS Visits | DFS Edges |
|-----------|-----------|-------------|------------|
| small_1   | 61,400    | 16          | 24         |
| large_3   | 420,800   | 100         | 202        |

SCC performance follows **O(V + E)**. Larger SCCs increase DFS traversal time.

---

### ✅ 2.2 Topological Sort (Kahn)
Applied to the condensation DAG to produce:

- topological order of SCC components  
- valid scheduling order of original tasks

| Dataset   | Time (ns) | Pushes | Pops |
|-----------|-----------|--------|-------|
| small_1   | 23,900    | 5      | 5     |
| large_3   | 61,800    | 13     | 13    |

Main bottleneck: computing in-degrees for larger graphs.

---

### ✅ 2.3 Shortest and Longest Paths in DAGs
The project uses **edge-based weights** (documented choice).

Implemented:

- Single-source shortest paths
- Longest path (critical path) using DP over topo order
- Path reconstruction

| Dataset   | Time (ns) | Relaxations |
|-----------|-----------|-------------|
| small_1   | 38,400    | 3           |
| large_3   | 44,400    | 10          |

Performance matches expected **O(V + E)** behavior.

---

## 3. Analysis

### ✅ SCC
- Essential for handling cyclic dependencies.
- Condensation simplifies later algorithms.
- More cycles → more SCCs → higher DFS cost.

### ✅ Topological Sort
- Linear time after condensation.
- Sensitive to graph density due to in-degree calculations.

### ✅ DAG Shortest/Longest Paths
- Scales linearly.
- Critical Path length indicates minimum feasible total duration.

---

## 4. Conclusions
- SCC detection is necessary before any scheduling due to cyclic dependencies in city-service tasks.
- Condensed graphs enable efficient planning and eliminate cycles.
- Topological sorting provides a safe processing order for tasks.
- Critical Path highlights bottlenecks in smart-city scheduling.
- Dynamic-programming methods on DAGs are efficient for both route planning and timing estimation.

---

## 5. Practical Recommendations
- Always begin with SCC detection.
- Perform all scheduling on the condensation DAG.
- Use Critical Path to identify timing bottlenecks.
- Run DAG shortest-path algorithms only after SCC compression.
- Use different densities and structures when generating test graphs.

---

## 6. Project Structure
DAA4/
├── data/ # 9 generated datasets
├── src/
│ ├── main/java/org/example/
│ │ ├── model/ # Graph structures
│ │ ├── scc/ # SCC (Kosaraju)
│ │ ├── topo/ # Condensation + Kahn topological sort
│ │ ├── dagsp/ # Shortest & longest paths in DAG
│ │ ├── metrics/ # Performance metrics
│ │ └── util/ # Dataset generator
│ └── test/java/ # JUnit tests
├── pom.xml
└── README.md

---

## 7. Build & Run
# How To Run
Just run the 'main' file
