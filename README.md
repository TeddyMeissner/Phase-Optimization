# Phase-Optimization
This code looks to further the construction of modern phase diagrams which are typically constructed using computational methods (calculation of phase diagrams or CALPHAD). The essence of the CALPHAD methodology first lies in the fitting of a Gibbs free energy function to experimental data and quantities derived from density functional theory (DFT) calculations. Once the data is fit to a free energy function the second step is to minimize the free energy as a function of temperature and chemical composition. The phase diagrams and free energy functions created by the CALPHAD methodology are a key component in simulating microstructure evolutions and extracting physical properties such as strength, elasticity, etc. The typical method to study microstructure evolutions is through the phase field method, and thus a key component is materials discovery. It is important to note that most existing phase diagrams and underlying CALPHAD methods are constructed with the temperature and composition as the only variables. At the same time, many complex alloys have stress naturally arising in their microstructures, which may have strong effect on the thermodynamics of the alloy reflected in its phase diagram. Often, these components are not included or “baked” into the CALPHAD models as they can be extremely difficult to measure experimentally, and quantities derived from DFT calculations can be noisy. One can see in extrapolating the free energies into the predictions of systems not yet experimentally studied, the propagation of system specific information (interaction parameters, elasticity, etc.) can arise many issues. Thus, our research aims to theoretically study the effects of interfacial stress on phase diagrams to resolve the differences between current experimental and computational results. For a more detailed description and references refer to the [writeup](Breif%20writeup.pdf). 

## Formulation

To tackle the issue of extract system specific energy, we will first examine the model developed by [Cahn & Larche](https://www.sciencedirect.com/science/article/pii/0001616084901731) which seeks to minimize the free energy for a system, for coherent equilibrium between three phases ( $\alpha$, $\beta$, $L$), 

$$g=z_Lg_L\left(x_L\right)+z_\alpha g_\alpha\left(x_\alpha\right)+z_\beta g_\beta\left(x_\beta\right)+g_e$$

Where $z_\alpha,z_\beta,z_L$ are the molar fractions of the $\alpha,\ \beta,\ L$ phases respectively. Given a composition $X$, $g$ has the constraints of the preservation of molar fractions and chemical species,
$$z_L x_L+z_\alpha x_\alpha+z_\beta x_\beta=X$$
$$z_\alpha\ +z_\beta\ + z_L = 1$$
In general, a system where $g_e=0$ has been well studied but cases where $g_e\ \neq 0$ is not well understood. For our research and with regards to more physical meaning, we will study the effects on phase diagrams where $g_e\ \neq0$ and depends only on the solid phases. In the case of the model discussed by Equation 1, Cahn and Larche proposed a model that had simplifying assumptions: infinite two-phase crystal, with both phases being isotropic and linearly elastic with the same elastic constants (e.g., Young’s modulus E and Poisson’s ratio $\nu$). These simplifying assumptions result in the coherency strain energy only depending on the molar fractions of the phases $z_\alpha$ and $z_\beta$ multiplied by the volume of the reference state, V, and the elastic energy, $E\epsilon^2/\left(1-\nu\right)$, which corresponds to a lattice misfit, $\epsilon$, 
$$g_e=z_\alpha z_\beta VR\epsilon^2/\left(1-\nu\right)$$
For simplification the code provided sets $A = VR\epsilon^2/\left(1-\nu\right)$ as these are assumed constants giving $g_e = A z_\alpha z_\beta$

## Using the Code
To run this project, 

```
git clone https://github.com/TeddyMeissner/Phase-Optimization.git
```
From here the core code can be found in the file Binary 3-Phase.ipynb. The three_phase class takes in three symbolic free energy functions and finds the correct phases at a given composition and temperature. The main functionality is to plot the free energy functions along with their common tangents in feasible areas at a given temperature and to return the overall phase diagram. An example of using the code may be, 

```python
x_a, x_b, x_L,T = sy.symbols('x_α, x_β, x_L,T')

x_a0,x_L0,x_b0 = .2,.5,.8
b_a,b_L,b_b = 10,12,11
a = 40

g_α = a*(x_a-x_a0)**2 + b_a
g_β = a*(x_b-x_b0)**2 + b_b
g_L = a*(x_L-x_L0)**2 + b_L - T

T_grid = np.concatenate((np.linspace(1,2.5,10),np.linspace(1.45,1.55,10)))

binary_A_0 = three_phase(g_α,g_β,g_L,A=0)
binary_A_2 = three_phase(g_α,g_β,g_L,A=2)

binary_A_0.plot_diagram(T_grid,title = 'Binary Phase Diagram, A = 0')
binary_A_2.plot_diagram(T_grid,title = 'Binary Phase Diagram, A = 2')

binary_A_0.plot_specific_temp(T_grid[2],x_lim = [.15,.85],y_lim = [9,12])
binary_A_2.plot_specific_temp(T_grid[2],x_lim = [.15,.85],y_lim = [9,12])
```
<p float="left">
  <img src="Images/Binary%20Phase%20Diagram,%20A%20=%200.png" width="45%" /> 
<img src="Images/Binary%20Phase%20Diagram,%20A%20=%202.png" width="45%" /> 
</p>

<p float="left">
  <img src="Images/temp_1_17,A%20=0.png" width="45%" /> 
<img src="Images/temp_1_17,A%20=2.png" width="45%" /> 
</p>

## Methods
We are looking to solve:  
**Minimize**
$$g(z^\alpha,z^\beta,x^\alpha,x^\beta,x^L) = z^Lg^L(x^L) + z^\alpha g^\alpha(x^\alpha) +z^\beta g^\beta(x^\beta) + A z^\alpha z^\beta$$
**Subject to**
$$X = z^Lx_1^L + z^\alpha x^\alpha +z^\beta x^\beta$$
$$z^\alpha + z^\beta + z^L = 1$$
$$0 \leq z^L,z^\alpha,z^\beta, x^L, x^\alpha,x^\beta,X \leq 1$$
The first equality constraint represents the conservation of of the selected composition $X$. The second equality constraint represents preservation of Mass. The inequality constraint represents the necessary conditions for the existence of a phase. To solve the generalized optimization problem by the method of Lagrange multipliers we set the problem up as, 

**Minimize**
$$l(z^\alpha,z^\beta,x_1^\alpha,x_1^\beta,x_1^L,\lambda) = (1-z^\alpha-z^\beta)g^L(x_1^L) + z^\alpha g^\alpha(x_1^\alpha) +z^\beta g^\beta(x_1^\beta)+ A z^\alpha z^\beta $$
$$- \lambda( (1-z^\alpha-z^\beta)x_1^L + z^\alpha x_1^\alpha +z^\beta x_1^\beta - X)$$
**Subject to**
$$0 \leq z^\alpha,z^\beta,z^\alpha+z^\beta, x_1^L, x_1^\alpha,x_1^\beta,X \leq 1$$

Where the seven possible solutions are (refer to the [writeup](Breif%20writeup.pdf) for further clarification), 

1. $g^L(X)$
   * $z^\alpha,z^\beta = 0$
2. $g^\alpha(X)$ 
    * $z^L,z^\beta = 0$
3. $g^\beta(X)$
   * $z^L,z^\alpha = 0$
4. $(1-z^\beta)g^L(x^L) + z^\beta g^\beta(x^\beta)$
   * $z^\alpha = 0$
   * Where, $z^\beta,x^L,x^\beta$ are found by the equations,
$$\frac{d g^L}{d x_1^L} = \frac{d g^\beta}{d x_1^\beta} = \frac{g^\beta(x^\beta) - g^L(x^L)}{x^\beta - x^L},z^\beta = \frac{X - x^L}{x^\beta - x^L}$$
5. $(1-z^\alpha)g^L(x^L) + z^\alpha g^\alpha(x^\alpha)$
   * $z^\beta = 0$
   * Where, $z^\alpha,x^L,x^\alpha$ are found by the equations,
$$\frac{d g^L}{d x_1^L} = \frac{d g^\alpha}{d x_1^\alpha} = \frac{g^\alpha(x^\alpha) - g^L(x^L)}{x^\alpha - x^L},z^\alpha = \frac{X - x^L}{x^\alpha - x^L}$$
6. $z^\alpha g^\alpha(x^\alpha) + z^\beta g^\beta(x^\beta) + A z^\alpha z^\beta$
   * $z^L = 0$
   * Where $z^\alpha,z^\beta,x^\alpha,x^\beta$ are found by, 
$$\frac{d g^\alpha}{d x_1^\alpha} = \frac{d g^\beta}{d x_1^\beta} = \frac{g^\alpha(x_1^\alpha)-g^\beta(x_1^\beta) + A (1 - 2 z^\alpha)}{x_1^\alpha - x_1^\beta},z^\alpha = \frac{X - x^\beta}{x^\alpha - x^\beta}$$
7. $(1-z^\alpha - z^\beta)g^L(x^L) + z^\alpha g^\alpha(x^\alpha) + z^\beta g^\beta(x^\beta) + A z^\alpha z^\beta$
   * Where $z^\alpha,z^\beta,x^L,x^\alpha,x^\beta$ are found by, 
$$\frac{d g^L}{d x_1^L} = \frac{d g^\alpha}{d x_1^\alpha} = \frac{d g^\beta}{d x_1^\beta} = \frac{g^\alpha(x_1^\alpha)-g^L(x_1^L) + A z^\beta}{x_1^\alpha - x_1^L} = \frac{g^\beta(x_1^\beta)-g^L(x_1^L) + A z^\alpha}{x_1^\beta - x_1^L}$$
$$z^\alpha = \frac{X - x^L}{x^\alpha - x^L} + z^\beta(\frac{x^L - x^\beta}{x^\alpha - x^\beta})$$

The code provided uses these solutions to:
1. Start with a single temperature
2. Find the solutions for a variable $X$ which are referred to as "common tangents"
3. Find all of the points where the common tangents intersect with eachother and the free energy functions (these are referred to in the code as "important points")
4. Finds the phase at each important point and plots it
5. Repeat until all wanted temperatures are accounted for 

## Findings
In a binary three-phase, the eutectic point (that which three phases in equilibrium) becomes a three phase region under stress. At temperatures above the Eutectic point with no-stress, there are no changes in the phase diagram under any amounts of stress. This is due to the fact that the stress is between the $\alpha$ and $\beta$ phases which do not exist above the Eutectic temperature. Below the Eutectic point, we see a disappearance of the $\alpha + \beta$ phase, and a growth from a Eutectic point to a three-phase region. 
### Figures

The figures below are using the following free energy functions: 
$$g^\alpha(x^\alpha) = a(x_1^\alpha - x_0^\alpha)^2 + b^\alpha, g^\beta(x^\beta) = a(x_1^\beta - x_0^\beta)^2 + b^\beta ,g^L(x^\beta) = a(x_1^L - x_0^L)^2 + b^L - T$$
Where, $a = 40, x_0^\alpha = .2, x_0^\beta = .8, x_0^L = .5 b^\alpha = 10, b^\beta = 11, b^L = 12$.
<p float="left">
  <img src="Images/A%20=%200.png" width="45%" /> 
<img src="Images/A%20=%202.png" width="45%" /> 
</p>
<p float="left">
  <img src="Images/A%20=%203.png" width="45%" /> 
<img src="Images/A%20=%2010.png" width="45%" /> 
</p>





