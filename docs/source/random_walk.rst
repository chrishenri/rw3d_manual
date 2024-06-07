.. _randomwalk:

Random-Walk Particle-Tracking
=====

Background
------------

RW3D is random-walk particle-tracking (RWPT) code simulating the fate and transport of solute in porous media. 
The RWPT method simulates the movement of a solute mass by tracking a large number of particles. Each particle is moved through the porous medium based on the velocity field, which is derived from the solution of the flow equation. 
This simulates advection, or the transport of particles by the flow of fluid. In addition, a random displacement is added to each particle’s movement to simulate the spreading of the plume by dispersion and diffusion.

Typically, the transport of a conservative solute solved by RW3D is described by the advection-dispersion equation (ADE):

.. math:: 
	:label: ade
	
	\begin{aligned}
	\phi\frac{\partial c}{\partial t} = \nabla \cdot (\phi \mathbf{D} \nabla c) + \nabla \cdot (\mathbf{q} c)
	\end{aligned}
	
where :math:`c` :math:`[g.m^{-3}]` is the solute concentration, :math:`\phi` is the effective porosity and :math:`\mathbf{D}` is the dispersion tensor given by: 

.. math::  
	:label: disp_tensor
	
	\begin{aligned}
	D_{ij}=(\alpha_T|v|+D_m)\delta_{ij} + (\alpha_L - \alpha_T)v_i v_j/|v|
	\end{aligned}

:math:`\textbf{v}` is pore water velocity, :math:`\mathbf{D_m}` is the molecular diffusivity, :math:`\delta_{ij}` is the Dirac delta function, and :math:`\alpha_L` and :math:`\alpha_T` are longitudinal and transverse dispersivities, respectively. 


Mathematical model
----------------

In brief, the Random Walk Particle Tracking (RWPT) method solves the ADE by dividing the solute mass into numerous representative particles. 
The change over time of a particle’s position is influenced by two factors: a drift term, which corresponds to the advective movement, and a superimposed Brownian motion, which accounts for dispersion. 
The particle’s displacement is expressed in its conventional form as per the Itô–Taylor integration scheme, as described by :cite:t:`gardiner1985handbook`: 

.. math::
	:label: rwpt

	\begin{aligned}
    \mathbf{x}_p(t+\Delta t) = \mathbf{x}_p(t) + \mathbf{A}(\mathbf{x}_p,t) \Delta t + \mathbf{B}(\mathbf{x}_p,t) \cdot \mathbf{\xi}(t)\sqrt{\Delta t},
	\end{aligned}

where :math:`\mathbf{x}_p` is the particle location, :math:`\Delta t` is the time step of the particles jump and :math:`\mathbf{\xi}` is a vector of independent, normally distributed random variables with zero mean and unit variance. 

.. math::  
	:label: eqA

	\begin{aligned}
    \mathbf{A} = \mathbf{u}(\mathbf{x}_p) + \nabla \cdot \mathbf{D}(\mathbf{x}_p) + \frac{1}{\theta(\mathbf{x}_p)} \mathbf{D}(\mathbf{x}_p)  \cdot \nabla \theta(\mathbf{x}_p). 
	\end{aligned}
	
The displacement matrix relates to the dispersion tensor as:

.. math::  
	:label: eqB
	
	\begin{aligned}
    2\mathbf{D} = \mathbf{B} \cdot \mathbf{B}^T.
	\end{aligned}
	
For more details, see :cite:t:`Salamon06`, who provide a clear presentation of the RWPT method.


Advantages
----------------

One of the key advantages of the RWPT method is that it avoids the need to solve the transport equation directly. 
As a result, it virtually eliminates numerical dispersion and artificial oscillations, which are common issues in commonly used Eulerian models. 
Moreover, this method is generally considered to be computationally efficient - especially for models with a large number of cells and strong heterogeneities - compared to more traditional methods such as Eulerian, mixed Eulerian–Lagrangian, or total variation diminishing (TVD) schemes.

Overall, due to its computational efficiency and the absence of numerical dispersion, the Lagrangian transport method is becoming a valuable tool for modelling complex, high-resolution transport problems.

Limitations
----------------

One of the main limitation of the RWPT method is the random fluctuations of computed concentrations due to subsampling. 
This error can be reduced by increasing the number of particles, since the statistical fluctuation is inversely related to the square root of the number of particles in a cell. 
Increasing the number of particles can greatly increase the computational cost. This problem can be tackled by applying post-processed smoothing techniques such as the Kernel Density Estimator (KDE) to limit the number of particles without compromising the solution :cite:t:`Fernandez11`. 
An option to use the KDE technique to smooth the recording particle temporal arrivals (breakthrough curve) is proposed in RW3D. 


