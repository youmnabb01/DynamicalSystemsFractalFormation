# DLA Cluster Simulation
This repository contains a Python implementation of the Diffusion Limited Aggregation (DLA) model, which simulates the formation of clusters through random particle diffusion. The simulation runs on a square grid, where particles randomly walk and stick together when they encounter other particles. The simulation can create both static cluster images and animated GIFs of the process.
# Features
- **Random Walk Simulation**: Simulates the random walk of particles within a square grid.
- **DLA Model**: Particles stick to the cluster when they encounter it, forming a growing structure.
- **Visualization**: Generates images of the cluster's evolution and saves them as a GIF.
- **Edge Handling**: Handles particle movements near the edge of the simulation area.
- **Customizable Parameters**: Adjust the radius of the cluster, whether or not to generate a GIF, and other parameters.
# Requirements
- Python 3.x
- NumPy
- Matplotlib
- ImageIO (for GIF creation)
- Numba (optional for acceleration)

# Acknowledgements
- Contributors: Youmna Abboud, Francesco Guido Vinzoni, Catalina IJspeert
# Functions
The simulation models the process of particle diffusion and aggregation to form fractal-like clusters. This is an overview of the main functions used.

## 1. **randomAtRadius**

### **Inputs:**
- `radius` (int): The radius within which the random point should be generated.
- `seedX` (int): The x-coordinate of the seed (starting point).
- `seedY` (int): The y-coordinate of the seed (starting point).

### **Description:**
This function generates a random point that lies exactly at a given distance (`radius`) from the seed point `(seedX, seedY)`. It uses trigonometric formulas to calculate the x and y coordinates of the point:
- A random angle `theta` is generated.
- The x-coordinate is computed using `cos(theta)` and the y-coordinate using `sin(theta)`.

### **Output:**
- Returns the random point's coordinates `(x, y)` which lie at the specified distance `radius` from the seed point.

---

## 2. **checkAround**

### **Inputs:**
- `Location` (array): The initial coordinates of random walkers.
- `squareSize` (int): The size of the field of view (FOV).
- `Matrix` (2D array): A matrix where values represent different areas:
  - `0` = area where random walkers are generated.
  - `1` = fractal (the cluster).
  - `2` = region outside the circle.

### **Description:**
This function checks the neighboring pixels around a given random walker's location:
- If the walker is at the edge of the FOV, the walker is disregarded.
- If not, the function checks whether there are other random walkers in the neighboring pixels (up, down, left, right).
- If no other walkers are adjacent, the walker continues its random movement.

### **Output:**
- `Location`: Updated array of random walkers' positions.
- `foundFriend`: Boolean array indicating if a neighboring random walker was found.
- `nearEdge`: Boolean array indicating if a walker is near the FOV boundary.
- `exitCircle`: Boolean array indicating if the walker has reached the radius where aggregation occurs.

---

## 3. **DLAcluster** 

### **Inputs:**
- `radius` (int): The radius for the cluster.
- `needGif` (bool): Flag to indicate whether to generate a GIF of the process.

### **Description:**
This is the main function that simulates the DLA process:
- It initializes a matrix with the given radius and places the seed at the center.
- A particle is added to the system and moved randomly using the `randomAtRadius` and `checkAround` functions.
- The particle "sticks" to the cluster if it encounters the fractal. This process continues until the cluster formation is completed.

### **Process:**
- Initializes the matrix with a circle and places the seed at the center.
- Generates a new particle and moves it randomly until it finds a neighbor in the cluster (fractal).
- If `needGif` is `True`, it saves the progress every 500 particles and generates a GIF.

### **Output:**
- Returns the total number of particles added to the cluster and the final matrix representing the fractal.

---

## Example: Running the Simulation

```python
import time
import numpy as np
import matplotlib.pyplot as plt

# Running the DLA cluster simulation
start = time.time()
mass, matrix = DLAcluster(90, True)

# Running the simulation for different radii and fitting the log-log data
radiusArray = np.arange(10, 80, 5)
mass = []

for i in radiusArray:
    massValue, matrix = DLAcluster(i, False)  # Adjust radius and GIF flag
    mass.append(massValue)

# Logarithmic fitting for mass vs radius
logRadius = np.log(radiusArray)
logMass = np.log(mass)
fitLog = np.polyfit(logRadius, logMass, 1)
fitLogFunc = np.poly1d(fitLog)

print("Log fit parameters: slope =", fitLog[0], ", intercept =", fitLog[1])
print("Fitting function: e^", fitLog[1], " * r^", fitLog[0])

print('Elapsed time:', time.time() - start)
