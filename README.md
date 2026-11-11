# IDS and Bidirectional BFS

A Python notebook comparing **Iterative Deepening Search (IDS)** and **Bidirectional Breadth-First Search (BiBFS)** on the California road network.

## Notebook

[California_Road_Network_IDS_Bidirectional_BFS.ipynb](California_Road_Network_IDS_Bidirectional_BFS.ipynb) downloads the dataset, builds an undirected graph, implements both algorithms, and compares search time, path length, and node expansions.

## How to run

1. Install Python 3 and Jupyter Notebook:
   ```bash
   python -m pip install notebook
   ```
2. Open the notebook:
   ```bash
   python -m notebook California_Road_Network_IDS_Bidirectional_BFS.ipynb
   ```
3. Run all cells in order. Internet access is required to download the dataset. The algorithm code uses only Python's standard library.

The default query is from node `0` to node `5`, with an IDS maximum depth of `20` and a time limit of `10` seconds per algorithm. Edit these values in the IDS cell to try another route. Start with nearby nodes because IDS can become expensive for deeper routes.

## Dataset

The notebook automatically downloads [roadNet-CA](https://snap.stanford.edu/data/roadNet-CA.html) from the Stanford SNAP collection. The dataset is not included in this repository.

Every edge has cost 1, so shortest paths minimize the number of edges rather than road distance. Both algorithms return a shortest path when they find a solution; the notebook's any-path and shortest-path timing columns refer to the same run. Timings vary by machine and query.
