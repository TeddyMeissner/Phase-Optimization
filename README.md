# Phase-Optimization
In material science, a phase diagram is often referred to as the “beginning of wisdom” [cite]. Quantities derived from phase diagrams go well beyond a graphical representation of thermodynamic equilibria as they play an essential role in evaluating phase transformations and microstructure evolution. The modern phase diagrams are constructed using computational methods (calculation of phase diagrams or CALPHAD). The essence of the CALPHAD methodology first lies in the fitting of a Gibbs free energy function to experimental data and quantities derived from density functional theory (DFT) calculations. Once the data is fit to a free energy function the second step is to minimize the free energy as a function of temperature and chemical composition. 

The phase diagrams and free energy functions created by the CALPHAD methodology are a key component in simulating microstructure evolutions and extracting physical properties such as strength, elasticity, etc. The typical method to study microstructure evolutions is through the phase field method, and thus a key component is materials discovery. It is important to note that most existing phase diagrams and underlying CALPHAD methods are constructed with the temperature and composition as the only variables. At the same time, many complex alloys have stress naturally arising in their microstructures, which may have strong effect on the thermodynamics of the alloy reflected in its phase diagram. Often, these components are not included or “baked” into the CALPHAD models as they can be extremely difficult to measure experimentally, and quantities derived from DFT calculations can be noisy. One can see in extrapolating the free energies into the predictions of systems not yet experimentally studied, the propagation of system specific information (interaction parameters, elasticity, etc.) can arise many issues. Thus, our research aims to theoretically study the effects of interfacial stress on phase diagrams to resolve the differences between current experimental and computational results.  

To tackle the issue of extract system specific energy, we will first examine the model developed by Cahn & Larche which seeks to minimize the free energy for a system, for coherent equilibrium between three phases ($\alpha, \beta, L$), 

$$g=z_Lg_L\left(x_L\right)+z_\alpha g_\alpha\left(x_\alpha\right)+z_\beta g_\beta\left(x_\beta\right)+g_e$$

Where $z_\alpha,z_\beta,z_L$ are the molar fractions of the $\alpha,\ \beta,\ L$ phases respectively giving the condition, $z_\alpha\ +\ z_\beta\ +\ z_L\ =\ 1$. Given a composition X, $g$ has the constraint of the preservation of chemical species, 

$z_\alpha\ +\ z_\beta\ +\ z_L\ =\ 1$
$z_L x_L+z_\alpha x_\alpha+z_\beta x_\beta=C$

In general, a system where $g_e=0$ has been well studied but cases where $g_e\ \neq 0$ is not well understood. For our research and with regards to more physical meaning, we will study the effects on phase diagrams where $g_e\ \neq0$ and depends only on the solid phases. In the case of the model discussed by Equation 1, Cahn and Larche proposed a model that had simplifying assumptions: infinite two-phase crystal, with both phases being isotropic and linearly elastic with the same elastic constants (e.g., Young’s modulus E and Poisson’s ratio $\nu$). These simplifying assumptions result in the coherency strain energy only depending on the molar fractions of the phases $z_\alpha$ and $z_\beta$ multiplied by the volume of the reference state, V, and the elastic energy, $E\epsilon^2/\left(1-\nu\right)$, which corresponds to a lattice misfit, $\epsilon$, 

$g_e=z_\alpha z_\beta VR\epsilon^2/\left(1-\nu\right)$

To date, we have analyzed the coherent equilibrium between three phases in a eutectic system. Our results have shown at the eutectic temperature $T_{eu}$ under no coherent stress, given any coherent stress the eutectic point disappears, and we no longer have three phases in equilibrium. This is important as many systems have some lattice misfits creating a coherent stress. At temperatures below $T_{eu}$ we have found the three-phase equilibrium to reappear under a certain amount of stress at a range of compositions shown in Figure 1. \\
image.png
Results for figure 1 are shown for a theoretical eutectic system using general parabolic functions for the free energies. Future works plan on analyzing peritectic systems with a similar approach and using both results to better understand real systems with coherent stress. While studying the simplified model under coherent stress gives understanding about the behavior of the changes in a phase diagram, Cahn & Larche recognized that the coherence energy in realistic alloys depends on the microstructure and proposed the more general equation, 

$$g_e=Az\left(1-z\right)f\left(x_\alpha,x_\beta,z\right)$$

where the solutions to Eq. 1 with the coherence energy given is generally unknown



