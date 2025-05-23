.. _randomwalk:

Random-Walk Particle-Tracking
====================================

Background
----------

**RW3D** is a random-walk particle-tracking (RWPT) code designed to simulate the fate and transport of solutes in porous media. 
The RWPT method models solute transport by tracking the movement of a large number of particles. Each particle's trajectory is controled by the local velocity field—derived from the solution of the groundwater flow equation—representing **advection**, the transport of solutes by fluid flow.

To account for **dispersion** and **diffusion**, a stochastic (random) displacement is added to each particle’s movement. This approach captures the spreading of the solute plume due to both mechanical dispersion and molecular diffusion.

The transport of a conservative solute in RW3D is governed by the **advection-dispersion equation (ADE)**:

.. math::
   :label: ade

   \phi\frac{\partial c}{\partial t} = \nabla \cdot (\phi \mathbf{D} \nabla c) - \nabla \cdot (\mathbf{q} c)

where:

- :math:`c` :math:`[g \cdot m^{-3}]` is the solute concentration  
- :math:`\phi` is the effective porosity  
- :math:`\mathbf{q}` is the Darcy flux  
- :math:`\mathbf{D}` is the hydrodynamic dispersion tensor, defined as:

.. math::
   :label: disp_tensor

   D_{ij} = (\alpha_T |v| + D_m) \delta_{ij} + (\alpha_L - \alpha_T) \frac{v_i v_j}{|v|}

with:

- :math:`\mathbf{v}` as the pore water velocity  
- :math:`D_m` the molecular diffusion coefficient  
- :math:`\delta_{ij}` the Kronecker delta  
- :math:`\alpha_L` and :math:`\alpha_T` the longitudinal and transverse dispersivities, respectively


Mathematical Model
------------------

The Random Walk Particle Tracking (RWPT) method solves the advection-dispersion equation (ADE) by discretizing the solute mass into a large number of representative particles. 
The position of each particle evolves over time due to two main components:

1. A **drift term**, representing advective transport, and  
2. A **random (Brownian) motion**, accounting for dispersion and diffusion.

The particle displacement is modeled using the Itô–Taylor integration scheme, as described by :cite:t:`gardiner1985handbook`:

.. math::
   :label: rwpt

   \mathbf{x}_p(t+\Delta t) = \mathbf{x}_p(t) + \mathbf{A}(\mathbf{x}_p,t) \Delta t + \mathbf{B}(\mathbf{x}_p,t) \cdot \boldsymbol{\xi}(t)\sqrt{\Delta t}

where:

- :math:`\mathbf{x}_p` is the particle position,  
- :math:`\Delta t` is the time step,  
- :math:`\boldsymbol{\xi}(t)` is a vector of independent, normally distributed random variables with zero mean and unit variance,  
- :math:`\mathbf{A}` is the deterministic drift vector, and  
- :math:`\mathbf{B}` is the dispersion-related displacement matrix.

The drift vector :math:`\mathbf{A}` is defined as:

.. math::
   :label: eqA

   \mathbf{A} = \mathbf{u}(\mathbf{x}_p) + \nabla \cdot \mathbf{D}(\mathbf{x}_p) + \frac{1}{\theta(\mathbf{x}_p)} \mathbf{D}(\mathbf{x}_p) \cdot \nabla \theta(\mathbf{x}_p)

where:

- :math:`\mathbf{u}` is the pore water velocity,  
- :math:`\mathbf{D}` is the dispersion tensor, and  
- :math:`\theta` is the volumetric water content or porosity (depending on context).

The displacement matrix :math:`\mathbf{B}` is related to the dispersion tensor by:

.. math::
   :label: eqB

   2\mathbf{D} = \mathbf{B} \cdot \mathbf{B}^T

For a detailed derivation and discussion of the RWPT method, refer to :cite:t:`Salamon06`, who provide a comprehensive and accessible presentation.



Advantages
----------

The Random Walk Particle Tracking (RWPT) method offers several notable advantages:

- **Elimination of numerical dispersion and oscillations**: Unlike Eulerian methods, RWPT bypasses the numerical solution of partial differential equations, avoiding common numerical artifacts. RWPT does not suffer from artificial dispersion or spurious oscillations, which can artificially distort solute plumes and misrepresent reactions.
- **Computational efficiency**: Especially in models with a large number of grid cells or strong heterogeneities, RWPT can be more efficient than traditional methods such as Eulerian finite difference or finite volume schemes, Mixed Eulerian–Lagrangian approaches, and Total Variation Diminishing (TVD) schemes.
- **Scalability and flexibility**: The method is well-suited for high-resolution, large-scale simulations and can easily incorporate complex boundary conditions and heterogeneous media.

Due to these strengths, RWPT has become a valuable tool for modeling complex solute transport problems in porous media, particularly when high accuracy and resolution are required.

Limitations
-----------

Despite its advantages, the RWPT method has some limitations:

- **Statistical noise in concentration estimates**: Because concentrations are estimated from a finite number of particles, random fluctuations (subsampling noise) can occur. This is especially noticeable in regions with low particle density.
- **Trade-off between accuracy and computational cost**: Reducing statistical noise requires increasing the number of particles, which can significantly raise computational demands. The standard deviation of concentration estimates is inversely proportional to the square root of the number of particles per cell.
- **Post-processing requirements**: To mitigate noise without excessive particle counts, post-processing techniques such as the **Kernel Density Estimator (KDE)** can be applied. KDE smooths particle distributions and improves concentration estimates without compromising accuracy :cite:t:`Fernandez11`.
- **Breakthrough curve smoothing**: RW3D includes an option to apply KDE to smooth temporal particle arrival data (i.e., breakthrough curves), enhancing the interpretability of simulation results.

While these limitations are manageable, they highlight the importance of careful model design and post-processing when using RWPT methods.

