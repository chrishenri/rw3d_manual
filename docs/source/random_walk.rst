Random-Walk Particle-Tracking
=====

.. _randomwalk:

Background
------------

RW3D is randoam walk particle tracking code simulating the fate and transport of solute in porous media. 

The transport of a conservative solute is described by the advection-dispersion equation (ADE):

.. math:: 
	:label: eq_ade
	
	\begin{aligned}
	\phi\frac{\partial c}{\partial t} = \nabla \cdot (\phi \mathbf{D} \nabla c) + \nabla \cdot (\mathbf{q} c)
	\end{aligned}
	
	
where :math:`c` :math:`[g.m^{-3}]` is the solute concentration, :math:`\phi` is the effective porosity and :math:`\mathbf{D}` is the dispersion tensor given by: :cite:t:`Ajami14`

.. math::  
	:label: eq_disp_tensor
	
	\begin{aligned}
	D_{ij}=(\alpha_T|v|+D_m)\delta_{ij} + (\alpha_L - \alpha_T)v_i v_j/|v|
	\end{aligned}

:math:`\textbf{v}` is pore water velocity, :math:`\mathbf{D_m}` is the molecular diffusivity, :math:`\delta_{ij}` is the Dirac delta function, and :math:`\alpha_L` and :math:`\alpha_T` are longitudinal and transverse dispersivities, respectively. 


Mathematical model
----------------

   
Advantages
----------------



Limitations
----------------
