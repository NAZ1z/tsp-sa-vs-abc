# Simulated Annealing vs Artificial Bee Colony on the TSP

A comparative study of two metaheuristic algorithms, Simulated Annealing (SA) and Artificial Bee Colony (ABC), applied to the Traveling Salesman Problem. Both algorithms are evaluated on two TSPLIB benchmark instances under the same experimental conditions, comparing solution quality (effectiveness) and runtime (efficiency).

Developed as a course project for Metaheuristics and Optimization.

## Problem

The Traveling Salesman Problem asks for the shortest closed tour that visits every city exactly once and returns to the starting city. The number of possible tours grows factorially with the number of cities, which makes exact methods impractical for larger instances and motivates the use of metaheuristics.

**Objective:** minimize the total tour length

```
D = sum of d(c_i, c_i+1) for i = 1..n-1  +  d(c_n, c_1)
```

where `d` is the Euclidean distance between two cities.

**Solution encoding:** a permutation of city indices. For example, `[3, 1, 5, 2, 4]` visits city 3, then 1, 5, 2, 4 and returns to city 3. The same encoding is used by both algorithms.

## Algorithms

### Simulated Annealing (single-solution)

| Parameter | Value |
|---|---|
| Initial temperature | 1000 |
| Cooling rate | 0.995 (geometric) |
| Neighborhood | Swap two random cities |
| Acceptance | Always accept improvements; accept worse moves with probability `exp(-delta / T)` |

### Artificial Bee Colony (population-based)

| Parameter | Value |
|---|---|
| Food sources (population size) | 20 |
| Abandonment limit | 50 trials |
| Neighborhood | Swap two random cities |
| Phases | Employed bees, onlooker bees (fitness-proportional selection, fitness = 1 / distance), scout bees |

## Experimental Setup

| Setting | Value |
|---|---|
| Datasets | `berlin52` (52 cities), `eil101` (101 cities) from TSPLIB |
| Stopping rule | 500 iterations |
| Independent runs | 20 per algorithm per dataset |
| Metrics | Best distance, average distance, average runtime |
| Language | Python 3 (standard library only) |

## Results

Results from 20 independent runs per configuration:

| Dataset | Algorithm | Best Distance | Average Distance | Average Time (s) |
|---|---|---:|---:|---:|
| berlin52 | SA | 15,664.32 | 17,906.29 | 0.012 |
| berlin52 | ABC | **12,590.25** | **13,219.62** | 0.584 |
| eil101 | SA | 2,841.40 | 3,010.97 | 0.021 |
| eil101 | ABC | **1,415.81** | **1,548.73** | 1.058 |

Runtimes depend on hardware, and individual results vary between runs because both algorithms are stochastic.

### Key Findings

- **Solution quality:** ABC produced shorter tours on both instances. Its best tour was 19.6% shorter on berlin52 and 50.2% shorter on eil101.
- **Runtime:** SA was roughly 50 times faster, since it evaluates one neighbor per iteration while ABC evaluates about 40 (employed and onlooker phases).
- **Exploration vs exploitation:** SA starts exploratory at high temperature and shifts toward exploitation as it cools. ABC keeps a population of solutions and injects new random solutions through scout bees, which gives it stronger exploration and helped it find shorter tours.
- **Scaling:** the quality gap widened on the larger instance (eil101), where SA's short cooling schedule left it far from convergence.

### Limitations

With a budget of 500 iterations, both algorithms remain well above the known optimal tour lengths for these instances (7,542 for berlin52 and 629 for eil101). Possible improvements include a 2-opt neighborhood, a longer or adaptive cooling schedule for SA, and comparing the algorithms under an equal number of objective-function evaluations instead of equal iterations.

## Project Structure

```
tsp-sa-vs-abc/
├── notebooks/
│   ├── tsp_sa.ipynb     # Simulated Annealing implementation and experiments
│   └── tsp_abc.ipynb    # Artificial Bee Colony implementation and experiments
├── data/
│   ├── berlin52.tsp     # TSPLIB instance, 52 cities
│   └── eil101.tsp       # TSPLIB instance, 101 cities
└── README.md
```

## How to Run

### Locally with Jupyter

```bash
git clone https://github.com/NAZ1z/tsp-sa-vs-abc.git
cd tsp-sa-vs-abc/notebooks
jupyter notebook
```

Open either notebook and run all cells. The notebooks read the datasets from the `data` folder automatically.

### Google Colab

1. Open the notebook in Google Colab.
2. Upload `berlin52.tsp` and `eil101.tsp` from the `data` folder to the Colab session.
3. Run all cells.

No external libraries are required.

## Output

For each dataset, each notebook prints:

- The best distance from each of the 20 runs
- Best distance overall
- Average distance
- Average runtime per run

## Datasets

Both instances come from TSPLIB, a standard library of benchmark instances for the Traveling Salesman Problem, and use 2D Euclidean coordinates.
