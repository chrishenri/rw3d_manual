.. _processes:

Simulated processes
=====

Transport
----------------

RW3D solve the typical transport processes that are: advection, dispersion, and diffusion. This is done by solving the RWPT equation defined in the chapter :ref:`randomwalk`. 


Reactions
----------------

RW3D solves a range of reactions, which are described below. We refer to the related reference for details about the method for solving such reactions using particle tracking techniques.  

First-order decay networks
`````````````
The transport equations governing the behavior of network reactions is given by a set of advective-dispersive equations coupled with first-order reactions:

.. math:: 
	:label: firstorder
	
	\begin{aligned}
	\frac{\partial (\theta c_i)}{\partial t} + \nabla\cdot({\theta \mathbf{u} c_i}) - \nabla \cdot \left(\theta\mathbf{D}\cdot\nabla c_i \right) = \sum_{j=1}^{n_s} y_{ij}k{j}\theta c_j 
	\end{aligned}

where the ith-equation represents the mass balance of the ith species, :math:`n_s` is the number of the species involved, :math:`\theta [-]` is the porosity of the media, :math:`q [L T^{–1}]` is the Darcy velocity vector, and :math:`D [L^{2} T^{–1}]` is the dispersion tensor. 
For any given species i, :math:`c_i [M L^{–3}]` is the concentration in the liquid phase, :math:`k_i [T^{–1}]` is the first-order contaminant destruction rate constant, and :math:`y_{ij} [M M^{–1}]` is the effective yield coefficient for any reactant or product pair. 
These coefficients are defined as the ratio of mass of species i generated to the amount of mass of species j consumed.

RW3D solves this network by estimating the probability for a particle at a given state (i.e., species) at a given time to turn into another species after a given time step. The derivation, validation and application of the method is presented in :cite:t:`Henri2014`.

Bimolecular reaction networks
`````````````
RW3D is solving few types of bimolecular reactions. The reactive transport of such systems is given by: 

.. math:: 
	:label: aderx

    \begin{aligned}
    \frac{\partial (\theta c_i)}{\partial t} = - \nabla\cdot({\theta \mathbf{u} c_i}) + \nabla \cdot \left(\theta\mathbf{D}\cdot\nabla c_i \right) + r(c_A,c_B)
    \end{aligned}

where :math:`c_i` (:math:`i=A,B`) :math:`[M L^{-3}`, units given for 3 dimensions] is the solute concentration of each species :math:`i`, :math:`\theta [L^2 L^{-2}]` is the water content, :math:`\mathbf{u}` is the pore water velocity :math:`[L T^{-1}]` and :math:`r(c_A, c_B)` is the total rate of product creation via reaction and source. 
For instance, for a :math:`A + B \to C`, this reaction term is :math:`r(c_A, c_B) = -k_f c_A c_B`, where :math:`k_f [L^{2}M^{-1}T^{-1}]` is the reaction rate coefficient. 

For the moment, RW3D is solving the following reactions: 

- 2 products: :math:`A + B \to C + D`
- 1 product: :math:`A + B \to C`
- 0 product: :math:`A + B \to 0`

The particle-based method used here simulates bimolecular reactions through probabilistic rules of particle collisions and transformation, as described by :cite:t:`Benson2008`.

Linear Sorption
`````````````

Linear instantaneous sorption, i.e., retardation, is simply solved by scaling the advective flux: 

.. math:: 
	:label: ade
	
	\begin{aligned}
    R_i \frac{\partial (\theta c_i)}{\partial t} = - \nabla\cdot({\theta \mathbf{u} c_i}) + \nabla \cdot \left(\theta\mathbf{D}\cdot\nabla c_i \right)
    \end{aligned}
	
where :math:`c` :math:`[g.m^{-3}]` is the solute concentration, :math:`\phi` is the effective porosity, :math:`\mathbf{D}` is the dispersion tensor, and :math:`R_i` is the i-th species specific retardation factor.  


Multirate Mass Transfer
----------------

Parameters of the multirate mass transfer model are species specific. 
In a general form (i.e, associated to a multispecies reactive system), the multirate mass transfer model is given by:  
.. math:: 

    \begin{equation}
    \sum_{k=0}^{N_{im}}\phi_{k}{R}_{ik}\frac{\partial c_{ik}}{\partial t} - \mathscr{L}(c_{i0})
    = \sum_{j=1}^{N_s} \sum_{k=0}^{N_{im}} y_{ij}k_{jk}\phi_{k} c_{jk},  \qquad\forall\, i=1,2,\cdots,N_s ,
    \end{equation}

.. math:: 

    \begin{multline}
    R_{ik}\frac{\partial c_{ik}}{\partial t}=\alpha^{\prime}_{ik} \left(c_{i0}-c_{ik}\right)+ \displaystyle\sum_{j=1}^{N_s}y_{ij}k_{jk} c_{jk},  
    \\ \qquad\forall\, k=1,2,\cdots,N_{im}, \qquad \forall\, i=1,2,\cdots,N_s. 
    \end{multline}

The left-hand-side of these equations form the standard multirate mass transfer model :cite:p:`haggerty95` that describes advective-dispersive transport with rate-limited mass transfer between a mobile domain and any number of immobile domains for each species. These immobile domains can represent a wide variety of common field site conditions that exits in almost all porous media and over multiple scales.

In these equations, the variable :math:`c_{i0} \left[M\, L^{-3}\right]` is the concentration of the *i*-th species in the mobile domain (denoted always by the subscript index :math:`k=0`), :math:`c_{ik} \left[M\, L^{-3}\right]`, for :math:`k=1,...,N_{im}`, is the concentration of the i-th species in the k-th immobile domain, :math:`N_s` is the number of species, :math:`N_{im}` is the number of immobile domains, :math:`\phi_0 \left(dimensionless\right)` is the porosity of the media in the mobile domain, :math:`\phi_{k} \left(dimensionless\right)` for :math:`k=1,...,N_{im}` is the porosity of the media in the *k*-th immobile domain,  :math:`R_{i0}\,\left(dimensionless\right)` is the retardation factor of the *i*-th species in the mobile domain, and :math:`R_{ik} \left(dimensionless\right)` is the retardation factor of the *i*-th species in the *k*-th immobile domain :math:`(k=1,...,N_{im})`. 
Sorption is considered in local equilibrium (linear isotherm), and :math:`\mathscr{L}(c)` is the mechanical operator of the mobile concentrations defined by

.. math:: 

	\mathscr{L}(c) = \nabla \cdot (\phi_0\mathbf{D}\nabla c) - \nabla\cdot\left(\mathbf{q}c\right),

where :math:`\textbf{q} \left[L\, T^{-1}\right]` is the Darcy velocity vector, and :math:`\mathbf{D}` is the dispersion tensor :math:`\left[L^{2}\, T^{-1}\right]`. The first equation (\ref{eq:governGene}) is actually the mass balance associated with any of the species involved in the network reaction system, and equation (\ref{eq:governImmo}) describes the mass transfer of the *i*-th species between the mobile domain and the *k*-th immobile domain. 
%This mass transfer process is characterized by the apparent mass transfer coefficient :math:`\alpha_{ik} [T^{-1}]`, which is defined as :math:`\alpha_{ik}=\alpha^\prime_k/R_{ik}`, where  :math:`\alpha^\prime_k` is the first-order mass transfer rate coefficient between the mobile domain :math:`(k=0)` and the *k*-th immobile domain :math:`(k=1,...,N_{im})`.

The right-hand-side of equation (\ref{eq:governGene}) represents the destruction and production of the different species driven by first-order kinetic reactions, where :math:`k{}_{i\ell} \left[T^{-1}\right]` is the first-order contaminant destruction rate constant associated with the $i$ species and :math:`\ell` domain, :math:`y{}_{ij} \left[M\, M^{-1}\right]` is the effective yield coefficient for any reactant or product pair *(i,j)*. 
It is a stoichiometric coefficient that is assumed constant for all domains. 
These coefficients are defined as the ratio of mass of species *i* generated to the amount of mass of species *j* consumed. 
The yield coefficients :math:`y{}_{ii}` are equal to :math:`-1` and represent the first-order decay of the *i*-*the species. 
Similar reaction terms have been presented by many authors \cite[][]{clement97,clement01,sun99,Falta07}. 
We have assumed that only aqueous concentrations are susceptible to undergo chemical reactions, i.e., no biodegradation in the sorbed phase occurs. Nevertheless, we note that other situations can be simulated by properly redefining the degradation rates \citep{vanGenuchten85}.

Sink
----------------
