# Maximum Clique with Frank–Wolfe Methods

Implementation and experimental analysis of **Frank–Wolfe optimization methods for the Maximum Clique Problem**, developed as part of the **Optimization for Data Science** course at the University of Padua.

The project compares three Frank–Wolfe variants, three step-size strategies, and two regularized objective functions on three graph instances from the **DIMACS10 benchmark collection**.

---

## Overview

The **Maximum Clique Problem (MCP)** consists of finding the largest subset of vertices in a graph such that every pair of vertices in the subset is connected by an edge.

In this project, the problem is approached through a continuous optimization formulation over the probability simplex. Given the adjacency matrix \(A\), the main objective is

$$
\max_{x \in \Delta_n} x^\top A x,
$$

where

$$
\Delta_n =
\left\{
x \in \mathbb{R}^n :
x_i \geq 0,\;
\sum_i x_i = 1
\right\}.
$$

The resulting optimization problem is solved using different variants of the Frank–Wolfe algorithm and their corresponding update rules.

---

## Methods

Three Frank–Wolfe variants are implemented:

### Standard Frank–Wolfe

The classical Frank–Wolfe algorithm, using the linear optimization oracle over the probability simplex.

### Away-Step Frank–Wolfe

Extends the standard method by maintaining an active set and allowing the algorithm to move away from an active vertex.

### Pairwise Frank–Wolfe

Transfers weight directly from an away vertex to the Frank–Wolfe vertex, also using the active-set representation.

---

## Objective Functions

The experiments consider two regularization approaches.

### 2-norm regularization

The quadratic objective is augmented with a 2-norm regularization term:

$$
f(x)
=
x^\top A x
+
\frac{\alpha_2}{2}\|x\|_2^2.
$$

### Exponential regularization

The second objective uses an exponential regularization term:

$$
f(x)
=
x^\top A x
+
\alpha_2
\sum_i
\left(e^{-5x_i}-1\right).
$$

For this objective, an exact analytical line-search solution is not generally available, so a numerical line-search procedure is used.

---

## Step-Size Strategies

Each algorithm is tested using three different step-size strategies:

* **Diminishing step size**
* **Armijo rule**
* **Numerical line search**

The step-size computation is adapted to the corresponding Frank–Wolfe variant and respects the maximum feasible step along the selected direction.

---

## Initialization

Initialization differs between the standard algorithm and the two variants.

For **standard Frank–Wolfe**, a **single-vertex initialization** is used. During the experiments, random-weight initialization did not produce converged solutions satisfying the required local-maximum condition.

For **away-step and pairwise Frank–Wolfe**, **random simplex initialization** is used.

This distinction is part of the experimental setup and is therefore kept fixed when comparing the methods.

---

## Experimental Setup

The experiments are performed on three DIMACS10 graph instances:

| Dataset      | Known maximum clique |
| ------------ | -------------------: |
| `hamming8-4` |                   16 |
| `brook400-2` |                   29 |
| `C250-9`     |                   44 |

For each graph, the following combinations are tested:

| Component      | Configurations                   |
| -------------- | -------------------------------- |
| FW variant     | Standard, Away-step, Pairwise    |
| Step size      | Diminishing, Armijo, Line search |
| Regularization | 2-norm, Exponential              |

This results in a systematic comparison of the different algorithmic configurations.

The experiments evaluate quantities such as:

* objective value;
* clique size;
* Frank–Wolfe gap;
* number of iterations;
* computational time;
* convergence behaviour;
* local-maximum condition.

---

## Repository Structure

```text
.
├── Data/
│   ├── hamming8-4.mtx
│   ├── brook400-2.mtx
│   └── C250-9.mtx
│
├── Notebooks/
│   └── experiments.ipynb
│
├── Results/
│   ├── raw/
│   ├── processed/
│   └── figures/
│
├── src/
│   └── frank_wolfe.py
│
├── .gitignore
├── LICENSE
├── README.md
└── requirements.txt
```

The exact contents of `src/` and `Notebooks/` may depend on the final organization of the implementation.

---

## Installation

Clone the repository:

```bash
git clone <REPOSITORY-URL>
cd <REPOSITORY-NAME>
```

Create a virtual environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Install the required packages:

```bash
pip install -r requirements.txt
```

---

## Data

The graph instances are stored as **Matrix Market (`.mtx`) adjacency matrices**.

They can be loaded using:

```python
from scipy.io import mmread
import numpy as np


def adj_matrix(dataset):
    file = "Data/" + dataset + ".mtx"
    A = mmread(file).astype(np.float64)
    return A.toarray()
```

For example:

```python
A = adj_matrix("C250-9")
```

The datasets originate from the **DIMACS10 benchmark collection**.

Official DIMACS10 resources:

* [DIMACS10](https://sites.cc.gatech.edu/dimacs10/)
* [DIMACS10 Downloads](https://sites.cc.gatech.edu/dimacs10/downloads.shtml)

---

## Running the Experiments

The main optimization routine supports the following configuration:

```python
frank_wolfe(
    x0=None,
    A=A,
    fw_variant="standard",
    reg_method="2norm",
    step_strat="diminishing",
    tol=1e-4,
    support_tol=1e-7
)
```

### Main parameters

| Parameter     | Description                             |
| ------------- | --------------------------------------- |
| `x0`          | Initial point                           |
| `A`           | Adjacency matrix                        |
| `fw_variant`  | `standard`, `awaysteps`, or `pairwise`  |
| `reg_method`  | Regularization method                   |
| `step_strat`  | Step-size strategy                      |
| `tol`         | Convergence tolerance                   |
| `support_tol` | Threshold used to determine the support |

The experimental notebook can be used to run the different combinations and collect the corresponding results.

---

## Results

The experimental results are organized according to:

1. **Graph instance**
2. **Frank–Wolfe variant**
3. **Regularization method**
4. **Step-size strategy**

This allows the convergence behaviour and computational performance of the different configurations to be compared under consistent experimental conditions.

Plots and processed results can be stored in:

```text
Results/
├── raw/
├── processed/
└── figures/
```

---

## Reproducibility

To reproduce the experiments:

1. Use the graph instances contained in `Data/`.
2. Use the same initialization strategy for each FW variant.
3. Keep the convergence tolerances fixed.
4. Keep the same stopping criteria and maximum number of iterations.
5. Use the same random seed when random initialization is involved.
6. Run the complete experimental configuration grid.

---

## References

The theoretical and algorithmic foundations of this project are based primarily on the following works provided as part of the course:

1. **Hungerford, J. T., & Rinaldi, F.**
   *A General Regularized Continuous Formulation for the Maximum Clique Problem.*

   This paper provides the continuous optimization formulation of the Maximum Clique Problem and motivates the use of regularization terms in the objective function.

2. **Jaggi, M.**
   *Revisiting Frank-Wolfe: Projection-Free Sparse Convex Optimization.*
   Proceedings of the 30th International Conference on Machine Learning (ICML), 2013.

   This paper provides the theoretical foundation for the Frank–Wolfe method and its projection-free optimization framework.

3. **Lacoste-Julien, S., & Jaggi, M.**
   *On the Global Linear Convergence of Frank-Wolfe Optimization Variants.*

   This work studies convergence properties of Frank–Wolfe variants, including away-step and pairwise methods, providing the theoretical background for the algorithmic variants considered in this project.

4. **Bomze, I. M., Rinaldi, F., & Zeffiro, D.**
   *Frank–Wolfe and Friends: A Journey into Projection-Free First-Order Optimization Methods.*

   This work provides a broader overview of Frank–Wolfe methods and their variants, including their algorithmic structure and convergence properties.

### Benchmark data

The experiments use graph instances from the **10th DIMACS Implementation Challenge (DIMACS10)** benchmark collection.

* [DIMACS10](https://sites.cc.gatech.edu/dimacs10/)
* [DIMACS10 Downloads]([https://sites.cc.gatech.edu/dimacs10/downloads.shtml](https://networkrepository.com/dimacs10.php))

---

## Authors

**Nicolò Borga**
**Matteo Randa**
**Andrea Senatore**

University of Padua
Optimization for Data Science
