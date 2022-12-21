### Pressure-Driven-Pipe-Flow

study case : Hagen-Poiseuille Flow


Physical modeling-based scenario:
                  constant pressure gradient (inside pipe)
                    <<<<<<<<<<------------
                        wall: u=0, v=0
   p    +-----------------------------------------------+   p
   e    |  -->      -->       -->        -->      -->   |   e
   r    |                                               |   r
   i    |  -->      -->       -->        -->      -->   |   i
   o    |                                               |   o
   d    |  -->      -->       -->        -->      -->   |   d
   i    +-----------------------------------------------+   i
   c                    wall: u=0, v=0                      c
-> A rectangular domain (think of a slice from a pipe with
   circular crossection alongside the longitudal axis)
-> The left and right edge are connected by periodicity,
   representing an infinitely long domain in x axis
-> Top and bottom edge represent wall boundary conditions
-> A constant pressure gradient (in x direction) acts on the
   entire domain
--------

Expected Output: 
The attained Hagen-Poisseuille profile is indeed independent
of the initial profile and just depends on the strength of
the pressure gradient. After certain amount of time, the 
parabola-based velocity profile will be generated  
        +-----------------------------------------------+
        |   ->       ->       ->       ->       ->      |
        |   --->     --->     --->     --->     --->    |
        |   ---->    ---->    ---->    ---->    ---->   |
        |   --->     --->     --->     --->     --->    |
        |   ->       ->       ->       ->       ->      |
        +-----------------------------------------------+

Computational strategy:
It is not necessarily crucial to compute the velocity profile in y
direction, since it will be approximately to zero throughout the computational domain.
The main focus on the computation of axisymmetrical flow (in the u-momentum direction).
The u-momentum equation is given as follows:
$    ∂u/∂t + u ∂u/∂x + v ∂u/∂y = - ∂p/∂x + ν ∇²u
                |     |             |       |
                |     ↓             ↓       |
                |    = 0        constant    |
                |                           |
                ↓                           ↓ $
        central differences         five-point stencil
0. Instantiate the u-solution field with ones except for
   the top and bottom boundary
1. Compute convection by periodic central difference
    u ∂u/∂x ≈ u[i, j] ⋅ (u[i, (j+1)] − u[i, (j−1)]) / (2 dx)
2. Compute diffusion by periodic five-point stencil
    ν ∇²u ≈ ν (
        + u[i, (j+1)]
        + u[(i+1), j]
        + u[i, (j−1)]
        + u[(i−1), j]
        − 4 ⋅ u[i, j]
        ) / (dx²)
3. Advance to next step by explicit Euler step
    u ← u + dt ⋅ (− ∂p/∂x + ν ∇²u − u ∂u/∂x)
4. Enfore the wall boundary condition by setting the u velocity
   at the top and bottom boundary to zero
5. Repeat from (1.) until a steady state is reached.
(the pressure gradient is prescribed to be constant value inside the domain (except at inlet and outlet boundary).

Necessary notes on numerical scheme and stability criteria:
1. An diffusion treatment using explicit FTCS (Forward-Time & Central-Difference Scheme) whereas 
   an convection treatment which is explicit FTBS (Forward-Time & Backward Scheme).
  
2. a central difference approximation for the
   convection term which is conditionally stable iff the diffusive
   transport is dominant (i.e., we suggest to not selecting the kinematic
   viscosity value too low).

3. issue on numerical stability is mathematically prescribed CFL (Courant-Friedrich-Lewy) condition:
    (ν dt) / (dx²) ≤ 1/2
3. Stability criteria 
