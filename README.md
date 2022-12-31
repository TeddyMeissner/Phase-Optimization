# Introducting Interfacial Stress into Phase Diagrams
## 2-Phase under stress
Let us examine first the effects on the two phase region in a system with two components and two solid phases. Consider two solid phases, $\alpha$ and $\beta$ in equilibrium with each other. Both phases are
solid solutions of chemical species 1 and 2. Composition of the phases are described in
terms of concentrations of species 1 per phase: $x_1^\alpha$ and $x_1^\beta$ (because $x_2^\alpha = 1 - x_1^\alpha$, and the same for same for $\beta$). The two phases have an interface, which has an associated stress that adds to the energy of the system. The stress is assumed to be proportional to the amounts of the two phases described by the molar fractions $z^\alpha$ and $z^\beta$: $A z^\alpha z^\beta$.

The goal is to find the compositions and molar fractions that correspond to both solid phases in thermodynamic equilibrium. To do so we must find the minimum of the Gibbs free energy of the system according to the following optimization problem which was first introduced in \cite{cahn_simple_1984}.\

**Minimize**
$$g(z^\alpha,z^\beta,x_1^\alpha,x_1^\beta) = z^\alpha g^\alpha(x_1^\alpha) +z^\beta g^\beta(x_1^\beta) + A z^\alpha z^\beta$$
**Subject to**
$$X =  z^\alpha x_1^\alpha +z^\beta x_1^\beta$$
$$z^\alpha +z^\beta = 1$$
$$0 \leq z^\alpha,z^\beta,z^\alpha+z^\beta, x_1^\alpha,x_1^\beta,X \leq 1$$
The first equality constraint represents the conservation of of the selected composition $X$. The second equality constraint represents preservation of Mass. The inequality constraint represents the necessary conditions for the existence of a phase. To solve the generalized optimization problem we have. Solving by the method of Lagrange Multipliers,

![A = 0](https://user-images.githubusercontent.com/112519285/210150692-23702ca9-9f62-498c-ade6-c2cb9ef8c88b.png)

**Minimize**
$$l(z^\alpha,z^\beta,x_1^\alpha,x_1^\beta,\lambda) =  z^\alpha g^\alpha(x_1^\alpha) +(1-z^\alpha)g^\beta(x_1^\beta)+ A z^\alpha (1-z^\alpha) - \lambda( (z^\alpha x_1^\alpha +(1-z^\alpha)x_1^\beta - X)$$
**Subject to**
$$0 \leq z^\alpha,z^\beta,z^\alpha+z^\beta, x_1^\alpha,x_1^\beta,X \leq 1$$
Finding the partial derivatives with respect to all variables we get the following equations, 
    $$\frac{\partial l}{\partial z^\alpha} =  g^\alpha(x_1^\alpha) - g^\beta(x_1^\beta) + A(1 - 2z^\alpha) - \lambda(x_1^\alpha + x_1^\beta) = 0$$
    $$\frac{\partial l}{\partial x_1^\alpha} =  z^\alpha(\frac{d g^\alpha}{d x_1^\alpha} - \lambda) = 0$$
    $$\frac{\partial l}{\partial x_1^\beta} = (1-z^\alpha)(\frac{d g^\beta}{d x_1^\beta} - \lambda) = 0$$
    $$\frac{\partial l}{\partial \lambda} = z^\alpha x_1^\alpha +(1 - z^\alpha) x_1^\beta - X = 0$$

The following system of equations has 3 possible solutions. \
    1. $z^\alpha = 1, \implies x_1^\alpha = X$
    Minimum: $g^\alpha(X)$\
    2. $z^\alpha = 0, \implies x_1^\beta = X$
    Minimum: $g^\beta(X)$\
    3. $0< z^\alpha < 1, \implies z^\alpha = \frac{X - x_1^\beta}{x_1^\alpha - x_1^\beta}$
    Minimum: $$\frac{d g^\alpha}{d x_1^\alpha} = \frac{d g^\beta}{d x_1^\beta} = \frac{g^\alpha(x_1^\alpha) - g^\beta(x_1^\beta) + A(1 - 2z^\alpha)}{x_1^\alpha - x_1^\beta}$$
In the case where there is no stress between two solid interfaces i.e. $A = 0$, we get the popular common tangent rule, 
$$\frac{d g^\alpha}{d x_1^\alpha} = \frac{d g^\beta}{d x_1^\beta} = \frac{g^\alpha(x_1^\alpha) - g^\beta(x_1^\beta)}{x_1^\alpha - x_1^\beta}$$
which states the solution is in either one phase or lies on the common tangent between two phases. This is depicted in the phase diagram above where the molar fractions of two phases were found. To depict this, let us choose two free energy functions at a specific temperature. Free energy functions are typically convex with parabolic shape thus we will choose 2 parabolas to depict these. 
$$g^\alpha(x^\alpha) = a(x_1^\alpha - x_0^\alpha)^2 + b^\alpha, g^\beta(x^\beta) = a(x_1^\beta - x_0^\beta)^2 + b^\beta$$
For graphing purposes we choose, $a = 40, x_0^\alpha = .2, x_0^\beta = .8, b^\alpha = 10, b^\beta = 11$. The following depicts these two functions.
