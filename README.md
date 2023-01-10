# Phase-Optimization
This code looks to further the construction of modern phase diagrams which are typically constructed using computational methods (calculation of phase diagrams or CALPHAD). he essence of the CALPHAD methodology first lies in the fitting of a Gibbs free energy function to experimental data and quantities derived from density functional theory (DFT) calculations. Once the data is fit to a free energy function the second step is to minimize the free energy as a function of temperature and chemical composition. The phase diagrams and free energy functions created by the CALPHAD methodology are a key component in simulating microstructure evolutions and extracting physical properties such as strength, elasticity, etc. The typical method to study microstructure evolutions is through the phase field method, and thus a key component is materials discovery. It is important to note that most existing phase diagrams and underlying CALPHAD methods are constructed with the temperature and composition as the only variables. At the same time, many complex alloys have stress naturally arising in their microstructures, which may have strong effect on the thermodynamics of the alloy reflected in its phase diagram. Often, these components are not included or “baked” into the CALPHAD models as they can be extremely difficult to measure experimentally, and quantities derived from DFT calculations can be noisy. One can see in extrapolating the free energies into the predictions of systems not yet experimentally studied, the propagation of system specific information (interaction parameters, elasticity, etc.) can arise many issues. Thus, our research aims to theoretically study the effects of interfacial stress on phase diagrams to resolve the differences between current experimental and computational results.  

## Formulation

To tackle the issue of extract system specific energy, we will first examine the model developed by Cahn & Larche which seeks to minimize the free energy for a system, for coherent equilibrium between three phases ($\alpha, \beta, L$), 

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

