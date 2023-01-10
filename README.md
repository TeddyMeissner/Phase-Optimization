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




