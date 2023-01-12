# Simulated Annealing applied to Travelling Salesperson Problem

For this problem, I wanted to find the optimal route between some major cities in South Africa, and wanted to try out if the simulated annealing algo would be a good place to start as well as what is the kind of the impact the hyper-parameters have. 

## Introduction

Certain problems provide a unique problem space in that they appear rather trivial to understand but prove to be very complex to solve optimally. The travelling salesperson problem (TSP) is an example of this type of problem that falls within the domain of various optimization fields of study including combinatorial optimization, theoretical computer science and operations reseaerch. In this study, a version of the problem is replicated with a focus on using simulated annealing to find an optimal solution. In trying to solve this problem, several algorithms were invented and several others tested to find their limitations, flaws or calibre. It is consequently also applicable in many other domains such as the design of microchips, DNA sequencing and astronomy. 

The basic premise of the problem is as follows:
Given a number of cities that a travelling salesperson has to visit to conduct their sales, what is the most optimal manner in which they can plan their route so as to visit all the cities once and arrive back to the starting point while travelling the least distance necessary.

For problems in computer science which do not have an exact solution, it is useful to classify into how complex their solution might be, to gauge best approach and the type of resources or algorithms that may potentially help. The key distinction is made on two variables: time (number of iterations required) and computer memory.

Some common definitions of relevant complexity classes are provided below, to better understand TSP:
- P: decision problems that are solvable by a deterministic Turing Machine in polynomial time. A problem in P can be solved in $n^k$ time for some constant k where n is the size of the input. These problems are tractable.
- NP: decision problems that are solvable by a *non-deterministic* Turing Machine in polynomial time. P is a subset of NP. NP problems can be verified by a Turing Machine in polynomial time.
- NP hard: is a set of problems that are at least as hard as the hardest problems in NP. A problem P is considered to be NP hard if for every problem L in NP there is a polynomial time reduction from L to P. NP hard problems do not have to be in NP nor do they have to be decidable. It takes incredibly long to verify the solution to an NP hard problem.

TSP is a NP-hard problem, where there is no guarantee that the optimal that one finds is indeed the optimal solution. Given the age of this problem, decades of research have helped in finding certain boundaries for solutions, as well as determining the optimal solutions up to a particular size. 

To gain some context on how the problem scales, it's useful to refer to the concept of Hamiltonian cycles in graph theory. A Hamiltonian path is a is defined as a undirected or directed graph that visits each node only once - and consequently, a Hamiltonian cylce is a closed loop through such a graph. In a graph with n nodes, the total number of possible Hamiltonian cycles (going in 1 direction) are given by, e.g. for 10 cities:
$$
HC = \frac{(n-1)!}{2} = \frac{9!}{2} = 181 440
$$
which is of course an incredibly large set to check through, especially as n is increased.

## Terminology
For this study, the following terminology is used:

- Cities: a set of coordiantes on a map denoting the nodes that need to be visited. (For simplicity - these can also be random points on a cartesian plane.)
- Route: a path through all the cities arriving back at the starting city.

## Goal of this study:

Implement the simulated annealing algorithm onto a Travelling Salesperson Problem and observe the impact of changing cooling schemes. 

# Simulated annealing
In a number of use cases for NP hard problems it became evident that finding the optimal solution may prove to be too complex or even impossible. However, approximations could be made that were near optimal if not optimal that proved to be fairly robust solutions. As a consequence of this exploration, a concept that is observed in thermodynamics and material sciences was used to draw inspiration for developing the Simulated Annealing (SA) algorithm.

## Background
In engineering, heat treatment of steel refers to time and temperature controlled processes that relieve residual stresses and modify the material properties such as hardness, ductility and toughness. Annealing is one such process where a material is heated to a certain temperature (in relation to the material's critical temperature), held at this point for the carbon to diffuse throughout the material and subsequently allowed to cool. The rate of the cooling is responsible for the end-state of the material depending on the extent of machining required to be done on it. Large crystals represent a state of low energy and result from very slow cooling, whereas fast cooling or quenching results in crystals with high energy, resulting in imperfections. 

## Equation's origin
From the laws of thermodynamics, for a substance in a state of equilibrium at an ambient temperature T, the probability of an
increase in energy of magnitude dE is given by:

$$
P(\delta E) = e^(\frac{-\delta E}{kT})
$$
where
- E is energy
- k is Boltzman's Constant ($1.380649 × 10^-23$ J/K)
- T is temperature

A mapping of the metropolis algorithm onto a combinatorial optimization problem:

| Thermodynamic simulation | Combinatorial optimization |
| - | - |
|System states | Feasible solutions|
|Energy | Cost|
|Change of state | Neighborhood move|
|Temperature | Control parameter|
|Frozen state | Heuristic solution|

## Key Points
In the equivalent implementation of this in simulated annealing, kT is simplified to a single constant t, as there is no relevance of k (Boltzman's Constant) in Monte Carlo simulations. 

The fundamental aspect to SA is to accept non-improving moves according to the equation above, in a sense, very similar to hill descent where every now and again an uphill move is allowed.
The magnitude of the increase in the temperature will control the probability of the move being accepted. Therefore, small move are more likely to be accepted as opposed to large moves. And, of course, as the temperature tends to 0, the liklihood of accepting any moves will reduce to 0 as well.

## Pseudocode

Begin \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Initialise ($s_0$) \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$s_c$ = $s_0$ \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Repeat \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;For _its_ = 1, _nrep_ do \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Begin \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Select s $\in$ $N(s_c)$ \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$\delta$ = $f(s)$ – $f(s_c)$ \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If $\delta$ < 0 then $s_c$ = s \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;else \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;r = U[0,1) \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;if $e^(\frac{-\delta}{t})$ > r then $s_c$ = s \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;End \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;t = $\alpha$(t) \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Until stopping condition = ‘true’ \
End

<Add comments from class notes on parameters for SA>


# Implementation

## mlrose
In this study, it was decided to use the python library _mlrose_ to implement the simulated annealing algorithm to TSP. mlrose is a Python package for applying some of the most common randomized optimization and search algorithms to a range of different optimization problems, over both discrete- and continuous-valued parameter spaces. It was developed by Georgia Tech's OMSCS/OMSA. 


## Map
Initially a 2-D square grid of 100 x 100 is chosen as the "map". This map is then populated with a different number of cities based on the list city_sizes.
The city sizes of 5, 10 and 20 have been chosen for this study. 

To demonstrate the same concept on a real-world map, the map of South Africa has been chosen with 10 cities across different provinces.

## Decay Schedule
The decay schedule which is analogous to the rate of cooling, can theoretically be picked as any sequence that decreases as the number of iterations tends to infinity. 
In the implementation of mlrose, 3 decay schedules are implemented by default which were used in the study as detailed below. The main variable for control in the equations below turned out to be $r$ which gives the rate of decay as per the different equations. 3 different values for each decay schedule were utilised to create a coarse grid for each case.

An important note to keep in mind is that, ideally, it is not ideal to pick decay schedules that are too fast as it may then get stuck in a local minima. Conversely, picking schedules that decay really slowly may lead to larger number of iterations. 

### Arithmetic Decay
Schedule for arithmetically decaying the simulated annealing temperature parameter T according to the formula:

$$
T(t) = \max(T_{0} - rt, T_{min})
$$
where:
- $T_{0}$ is the initial temperature (at time t = 0)
- $r$ is the rate of arithmetic decay
- $T_{min}$ is the minimum temperature value. 

The r values used with the arithmetic decay schedule are:
- 0.0001
- 0.001
- 0.01

### Geometric Decay
Schedule for geometrically decaying the simulated annealing temperature parameter T according to the formula:

$$
T(t) = \max(T_{0} \times r^{t}, T_{min})
$$
where:

- $T_{0}$ is the initial temperature (at time t = 0)
- $r$ is the rate of geometric decay
- $T_{min}$ is the minimum temperature value.

The r values used with the geometric decay schedule are:
- 0.99
- 0.95
- 0.90

### Exponential Decay
Schedule for exponentially decaying the simulated annealing temperature parameter T according to the formula:

$$
T(t) = \max(T_{0} e^{-rt}, T_{min})
$$
where:
- $T_{0}$ is the initial temperature (at time t = 0)
- $r$ is the rate of exponential decay
- $T_{min}$ is the minimum temperature value.

The r values used with the exponential decay schedule are:
- 0.005
- 0.01
- 0.05

## Results table format

The results table has been compiled in a format so as to store all the relevant information for each run in the format shown below. Note, the first column is omitted later on and is added below just for clarity.

|  | Nodes | Map | NumIters | DecaySchedule | ProcessingTime | FinalDistance | FitnessCurve | Route |
| - | - | - | - | - | - | - | - | - |
| Meaning | This is simply the number of the cities | List of coordinates for the cities on the map | Number of iterations taken to arrive at solution | Which decaying schedule was used | Time taken for the SA algo to run | Final "optimal" distance found | List of distances found at each iteration (used to plot) | List of coordinates for the cities on the map in sequence to arrive at "optimal" solution |
| DataTypes | int | list((float, float), ) | int | str | float | float | list(float) | list((float, float), ) |


# References

[1] APPLEGATE, D. L., BIXBY, R. E., CHVATAL, V., & COOK, W. J. (2011). The Traveling Salesman Problem: a Computational Study. Princeton, Princeton University Press. https://doi.org/10.1515/9781400841103. \
[2] https://towardsdatascience.com/solving-the-travelling-salesman-problem-for-germany-using-networkx-in-python-2b181efd7b07 \
[3] https://simplemaps.com/data/za-cities \
[4] https://brilliant.org/wiki/complexity-classes/ \
[5] https://en.wikipedia.org/wiki/Hamiltonian_path \
[6] https://en.wikipedia.org/wiki/Travelling_salesman_problem#As_a_graph_problem 
[7] Hayes, G. (2019). mlrose: Machine Learning, Randomized Optimization and SEarch package for Python. https://github.com/gkhayes/mlrose. Accessed: 02 August 2022. \
[8] https://en.wikipedia.org/wiki/Boltzmann_constant \
[9] Dowsland, K.A., Thompson, J.M. (2012). Simulated Annealing. In: Rozenberg, G., Bäck, T., Kok, J.N. (eds) Handbook of Natural Computing. Springer, Berlin, Heidelberg. https://doi.org/10.1007/978-3-540-92910-9_49 

