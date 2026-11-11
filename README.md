# IDS and Bidirectional BFS

A Python notebook comparing **Iterative Deepening Search (IDS)** and **Bidirectional Breadth-First Search (BiBFS)** on the California road network.

## Notebook

[California_Road_Network_IDS_Bidirectional_BFS.ipynb](California_Road_Network_IDS_Bidirectional_BFS.ipynb) downloads the dataset, builds an undirected graph, and runs both algorithms on the same start and goal nodes. Each algorithm reports its completion status, returned path, search time, path length, and node expansions.

## How to run

1. Download or clone this repository, then open a terminal in the folder containing the notebook.
2. Install Python 3 and Jupyter Notebook:

   ```bash
   python -m pip install notebook
   ```

3. Open the notebook:

   ```bash
   python -m notebook California_Road_Network_IDS_Bidirectional_BFS.ipynb
   ```

4. Select the Python 3 kernel and run all cells from top to bottom. Internet access is required to download the dataset. The algorithm code uses only Python's standard library.

The notebook keeps the full road network in memory, so the download and graph construction may take some time and require substantial RAM.

### Change the route or search limits

The default query is from node `0` to node `5`. Edit these values in **Cell 6**, then rerun Cells 6 and 8:

```python
start_node = 0
goal_node = 5
maximum_depth = 20
time_limit = 10
```

`maximum_depth` limits IDS only. `time_limit` is the number of seconds allowed for each algorithm's search; it does not limit the dataset download or preprocessing. Start with nearby nodes and keep the depth limit small because recursive IDS can become expensive for deeper routes.

## Dataset

The notebook automatically downloads [roadNet-CA](https://snap.stanford.edu/data/roadNet-CA.html) from the Stanford SNAP collection. The dataset contains **1,965,206 nodes** and **2,766,607 undirected edges**. Nodes represent road intersections and endpoints; an edge records a connection between two node IDs. The dataset is not included in this repository.

Every edge has cost 1, so shortest paths minimize the number of edges rather than road distance or travel time. Node IDs identify locations; their numerical values do not indicate how close two locations are.

## Explanation of each notebook cell

The notebook has **eight cells: four Markdown section headings and four Python code cells**. The numbers below refer to their positions from top to bottom, not the execution numbers shown by Jupyter.

### Cell 1 — Dataset heading (Markdown)

Introduces the `DATASET` section. This cell contains a heading and does not execute Python code.

### Cell 2 — Download and preview the dataset (Python)

Uses `urlopen` from `urllib.request` to download `roadNet-CA.txt.gz` and `gzip.decompress` to unpack it. The text is split into lines and stored in `dataset_lines` for preprocessing.

The cell reads the node count from the dataset header and prints ten sample connections in `FromNodeId -> ToNodeId` form. These rows show which nodes are connected; they are not distances or coordinates.

### Cell 3 — Data preprocessing heading (Markdown)

Introduces the `DATA PREPROCESSING` section, where the downloaded edge list is converted into a graph suitable for searching.

### Cell 4 — Build and validate the graph (Python)

Builds `graph`, a dictionary mapping each node ID to a list of its neighbors. It skips blank lines and comments, rejects malformed rows and negative node IDs, and counts invalid rows in `invalid_rows`.

Both endpoints are added as valid nodes, and every connection is added in both directions. Duplicate connections are avoided, including when the dataset already contains the reverse row. Node IDs do not need to be consecutive.

The cell stops with an error if no valid connections remain. Otherwise, it prints the number of valid nodes, the number of undirected edges counted once, and the number of invalid rows skipped. The resulting `graph` is shared by both search algorithms.

### Cell 5 — IDS heading and assumptions (Markdown)

Introduces `ITERATIVE DEEPENING SEARCH (IDS) IMPLEMENTATION`. It explains the unit edge-cost assumption and why the first solution found by IDS is also a shortest path. The any-path and shortest-path times describe one measured run, rather than two separate searches.

### Cell 6 — Implement and run IDS (Python)

Defines `iterative_deepening_search`. The function checks that the start and goal nodes exist, then tries depth limits from `0` through `maximum_depth`. At each limit, a recursive depth-limited search explores possible routes.

The current path prevents cycles. A neighbor is appended before recursion and removed during backtracking. The search stops when it finds the goal, reaches its time limit, or finishes all permitted depth limits. Node expansions are counted when a node's neighbors are explored, including repeated expansions across depth iterations.

This cell also sets the route parameters and measures the IDS call with `perf_counter`. It prints the endpoints, status, configured limits, last depth limit tried, elapsed search time, and expansion count. If a path is returned, it prints the path, its length in edges, and the time to the first/shortest solution.

### Cell 7 — Bidirectional BFS heading and assumptions (Markdown)

Introduces `BIDIRECTIONAL BFS IMPLEMENTATION`. It explains that edges have equal cost and that the first returned solution is already shortest under this assumption. As with IDS, the two reported solution times come from the same run.

### Cell 8 — Implement and run Bidirectional BFS (Python)

Defines `bidirectional_bfs`, using two `deque` queues: one starts at the start node and the other at the goal. Two parent dictionaries record visited nodes and the links needed to reconstruct a path.

The function validates the endpoints and handles the case where the start equals the goal. During searching, it chooses the smaller frontier and explores its next BFS layer, stopping when a newly discovered node has already been visited from the other side. It then joins the two parent chains into a complete start-to-goal path.

The function reports whether a path was found, no path exists, the endpoints are invalid, or the time limit was reached. This cell reuses the same graph, start node, goal node, and time limit as IDS, measures the search call, and prints its status and results. The combined comparison table has been removed from the notebook; both algorithms retain their individual result summaries.

## Reading the results

- **Status:** whether the search returned a path or stopped because of invalid endpoints, an exhausted frontier, or a resource limit. Reaching a depth or time limit does not prove that no path exists.
- **Path length:** the number of edges in the returned path, calculated as `len(path) - 1`.
- **Search time:** time spent in the measured algorithm call, excluding dataset download, preprocessing, and printing.
- **Node expansions:** the number of times a node's neighbors are explored. IDS includes repeats across depth iterations; Bidirectional BFS includes both directions.

Both algorithms return a shortest path when they find a solution under the notebook's assumptions. Their any-path and shortest-path time fields refer to the same run. Repeat queries before drawing general conclusions about speed.

## Files to upload to GitHub

Keep this structure so the notebook link and all four images display correctly:

```text
IDS-and-BIBFS/
├── README.md
├── California_Road_Network_IDS_Bidirectional_BFS.ipynb
└── assets/
    ├── state_space.png
    ├── search_workflows.png
    ├── recorded_output.png
    └── route_trace.png
```

When uploading through GitHub's website, upload the `assets` folder along with the README and notebook. Keep the image filenames and folder name unchanged so the relative image links continue to work.
