# Griffin-BISON Multiphysics Model

## a) KRUSTY Mesh Model

The KRUSTY mesh employed in the simulations was generated by using MOOSE mesh generator tools including `ParsedCurveGenerator`, `XYDelaunayGenerator`, `CircularBoundaryCorrectionGenerator`, and `PeripheralTriangleMeshGenerator`, etc. Combinations of these tools facilitate the formation of sophisticated meshes while maintaining their volume integrity. Figure 3(A) shows the comprehensive core mesh from MOOSE, while a detailed 2D mesh sample from the core's center is provided in Figure 3(B). An input example of combining `ParsedCurveGenerator` and `XYDelaunayGenerator` to generate a 2D mesh of irregular shape (blue mesh in Figure 3(C), which is part of the 2D mesh of the KRUSTY fuel region (Figure 3(B))) is shown in Figure 4.

The KRUSTY mesh is 1/4 angular symmetric and could be divided into half or quarter meshes using `PlaneDeletionGenerator` or `CartesianMeshTrimmer` objects. Figure 5 shows the examples of full core, half core, and quarter meshes. The quarter core mesh was selected in the simulations.


!media media/KRUSTY/Fig_3.jpg
      style=display: block;margin-left:auto;margin-right:auto;width:60%;
      id=Fig_3
      caption= (A) KRUSTY model; (B) the 2D mesh of KRUSTY fuel region with heat pipes; (C) the enlarged framed area in (B). 


!media media/KRUSTY/Fig_4.jpg
      style=display: block;margin-left:auto;margin-right:auto;width:60%;
      id=Fig_4
      caption= Input of generating an 2D mesh of irregular shape (blue mesh in Figure 3C))



!media media/KRUSTY/Fig_5.jpg
      style=display: block;margin-left:auto;margin-right:auto;width:60%;
      id=Fig_5
      caption= KRUSTY meshes generated using MOOSE: (a) full core, (b) half core, and (c) quarter core meshes




## b) KRUSTY Cross Section Generation

KRUSTY is a small fast spectrum core with a significant amount of neutrons leaking out of the fuel region. The energy spectrum shifts due to neutron leakages and thermal neutrons returning from the reflectors. Therefore, the KRUSTY fuel region was divided into seven radial regions and six axial zones as deciphered in Figure 2 for applying different cross sections for different fuel regions.

Both Serpent and MC2-3 were used to prepare cross sections for modeling KRUSTY. Sensitivity analysis showed that MC2-3 provides better anisotropic scattering cross sections in the BeO reflector regions, and Serpent provides better cross sections in the fuel region for accounting fuel Doppler reactivity feedback. Therefore, a hybrid multigroup cross section set that used MC2-3 cross sections in the reflector region and used Serpent cross sections in other regions was prepared for modeling KRUSTY.

The MC2-3 calculation takes a two-step approach. The first step creates energy self-shielded cross sections and the number of energy-groups used is larger than 1000 groups. These cross sections are imported into an approximated RZ model of KRUSTY as shown in Figure 6. Neutron transport calculation was performed by TWODANT to solve the neutron fluxes in each material region in the RZ model. The neutron fluxes calculated by TWODANT have considered both the neutron leakage effects and spatial self-shielding effects and were imported into the MC2-3 second step. Multigroup cross sections were condensed based on the TWODANT fluxes in each material zone. Table III lists the 40-group structure used in the calculations. The MC2-3 input for the first step and the input for TWODANT are documented in `mc_s1.in`. The MC2-3 input for the second step calculation is similar to the input in the first step but has a different control block as shown in Figure 7.



!media media/KRUSTY/Fig_6.jpg
      style=display: block;margin-left:auto;margin-right:auto;width:60%;
      id=Fig_6
      caption= KRUSTY RZ model for twodant calculation



!media media/KRUSTY/Fig_7.jpg
      style=display: block;margin-left:auto;margin-right:auto;width:60%;
      id=Fig_7
      caption= MC2-3 input control blocks in a) step 1 calculation b) step 2 calculation



**Table III. The 40-Energy Group Structure for Modeling KRUSTY**

| Group # | Upper bond (MeV) | Group # | Upper bond (MeV) | Group # | Upper bond (MeV) |
|---------|-------------------|---------|-------------------|---------|-------------------|
| 1       | 1.4191E+01        | 15      | 2.1445E-04        | 29      | 6.4759E-06        |
| 2       | 1.0000E+01        | 16      | 1.6702E-04        | 30      | 5.0435E-06        |
| 3       | 6.0653E+00        | 17      | 1.3007E-04        | 31      | 3.9279E-06        |
| 4       | 2.2313E+00        | 18      | 1.0130E-04        | 32      | 3.0590E-06        |
| 5       | 8.2085E-01        | 19      | 7.8893E-05        | 33      | 2.3824E-06        |
| 6       | 3.0197E-01        | 20      | 6.1442E-05        | 34      | 1.8554E-06        |
| 7       | 1.1109E-01        | 21      | 4.7851E-05        | 35      | 1.4450E-06        |
| 8       | 4.0868E-02        | 22      | 3.7267E-05        | 36      | 1.1254E-06        |
| 9       | 1.5034E-02        | 23      | 2.9023E-05        | 37      | 8.7642E-07        |
| 10      | 5.5308E-03        | 24      | 2.2603E-05        | 38      | 6.8256E-07        |
| 11      | 3.3546E-03        | 25      | 1.7603E-05        | 39      | 5.3158E-07        |
| 12      | 1.2341E-03        | 26      | 1.3710E-05        | 40      | 4.1746E-07        |
| 13      | 4.5400E-04        | 27      | 1.0677E-05        |         |                   |
| 14      | 2.7536E-04        | 28      | 8.3153E-06        |         |                   |



## c) Griffin Stand-Alone Neutronic Model

The stand-alone Griffin input for the neutronic model of KRUSTY is included. Figure 8(a) shows the mesh block of this input that has used the unstructured file `Krusty_3D_vol_pre_densify_fuel_v0_hp_all_in.e`. The volume for each material region in the mesh file is preserved and made to be consistent with the KRUSTY Serpent reference model. All heat pipe locations are filled. The list in the `subdomains` block references block ids in the mesh file, and the `extra_element_ids` is the list of material ids filling the mesh block.



!media media/KRUSTY/Fig_8.jpg
      style=display: block;margin-left:auto;margin-right:auto;width:60%;
      id=Fig_8
      caption= Griffin mesh block a) subdomain b) coarse mesh for CMFD acceleration



The KRUSTY mesh file has modeled 1/4th of the core with reflecting boundaries applied at the center-cut planes. Vacuum boundary conditions are applied on the KRUSTY top, bottom surface, as well as on its outermost radial surface as shown in Figure 9.



!media media/KRUSTY/Fig_9.jpg
      style=display: block;margin-left:auto;margin-right:auto;width:60%;
      id=Fig_9
      caption= Boundary conditions of the Griffin neutronic model for KRUSTY




!media media/KRUSTY/Fig_10.jpg
      style=display: block;margin-left:auto;margin-right:auto;width:60%;
      id=Fig_10
      caption= Transport system defined in Griffin neutronic model for KRUSTY



The Griffin neutronic model solves the k-eff and the flux distributions at critical state. Figure 10 shows the transport system block. It used the DFEM-SN solver. Spatial variables are discretized by the DFEM method with shape functions from the first order MONOMIAL family. Angular variables are discretized by the SN method using the Gauss-Chebyshev quadratures. Sensitivity analysis showed that the calculated k-eff was converged after considering the anisotropic terms NA=3 and using NPolar=3 angles per octant in the polar direction and NAzmthl=5 angles per octant in the azimuthal direction. As shown in Figure 10, six groups of delayed neutron group families were considered in the numerical calculations. Six delayed neutron data were obtained using Serpent and the values for each group were included in the ISOXML cross section file.

Figure 11 shows the executioner block in the Griffin input where the maximum number of allowed iterations and convergence criteria are specified. The SweepUpdate executioner unique for DFEM-SN was used. The Coarse-Mesh Finite Difference (CMFD) method was deployed to accelerate the DFEM-SN solver. The coarse mesh for the low-order diffusion calculation was a regular mesh that was generated in the mesh block as shown in Figure 8(b). The maximum diffusion coefficient was set to 10.0, as a limit value facilitating convergence in problems that contain void regions. It can be adjusted and will not significantly affect the accuracy of the results. Newton’s method was used to solve the low-order diffusion equation. Another option to use is the krylovshur method. It was found that it is important to use preconditioners for solving the diffusion equation in order to converge the DFEM-SN solver with CMFD acceleration. In this case, the LU decomposition was used and was found to be effective. The multiplicative prolongation was chosen to update all transport flux moments after solving the low-order diffusion calculation.



!media media/KRUSTY/Fig_11.jpg
      style=display: block;margin-left:auto;margin-right:auto;width:60%;
      id=Fig_11
      caption= Griffin executioner block with options for CMFD acceleration


!media media/KRUSTY/Fig_12.jpg
      style=display: block;margin-left:auto;margin-right:auto;width:60%;
      id=Fig_12
      caption= a) The material block  b) AuxVariables defined for cross section grid indices in Griffin neutronic model for KRUSTY



Figure 12(a) shows the material block in the Griffin input. The `CoupledFeedbackMatIDNeutronicsMaterial`, which interpolates multigroup cross sections on-the-fly among a tabulated cross section set, was used for multiphysics simulation. The material ID was read from the mesh block. The hybrid cross section set that was generated from Serpent and MC2-3 was used. The cross sections within the file are zone-averaged macroscopic cross sections. Therefore, the isotopes were assigned to be ‘pseudo’ and its density was 1.0.

The cross section set included a 2-D cross section table. The indices to lookup the 2-D table were the fuel temperature “Tf” and the structure temperature “Ts” that covers all other nonfuel regions. The state temperature points include fuel at 300 K, 600 K, 800 K, 1000 K and 1200 K and the structure material at 300 K, 600 K, 800 K and 1000 K, respectively. Figure 12(b) shows how different fuel and structure temperatures can be specified in the stand-alone neutronic calculations.

Finally, Figure 13 shows the steps to tally the axial power distribution in the KRUSTY innermost (“r1”) layer. To integrate the fission power within each fuel layer (seven radial layers), an AuxVariable in MOOSE that can be processed by the VectorPostprocessor was first defined to hold the layer-integrated total fission power for the axial regions within that layer as shown in Figure 13(a). A UserObject as shown in Figure 13(b) was necessary to perform the actual integration among the different axial layers. The integrated values were then placed into the AuxVariable as shown in Figure 13(c) and were printed out by the VectorPostprocessor as shown in Figure 13(d).


!media media/KRUSTY/Fig_13.jpg
      style=display: block;margin-left:auto;margin-right:auto;width:60%;
      id=Fig_13
      caption= Output blocks in Griffin neutronic model for KRUSTY to tally the power density in one of the fuel region a) AuxVariable block  b) UserObject block c) AuxKernel block d) VectorPostprocessor block



## (a) BISON Thermal-Mechanical Model

BISON was utilized to conduct thermo-mechanical simulations. In KRUSTY, the fuel disk and its radial shield hung down from the top stainless-steel plates. The bottom and radial reflectors sat on a movable platen below the fuel disk and were pushed up into the radial shield region to reach criticality. With very good insulation of the fuel disk, the top and bottom plates were about room temperature. Therefore, the reactor core was modeled between the fixed top and bottom surfaces with no total displacement in the axial direction but allowing radial thermal expansion or contraction. The top and bottom parts of KRUSTY were connected by “soft materials” to represent the vacuum and air region surrounding the fuel disk. In the KRUSTY thermo-mechanical model, the radial reflector expands upward into the “soft material” region when the fission power increases, and the fuel expansion is downward resulting in more part of fuel covered by the reflectors. Convective heat flux boundary conditions (h=5 W/m²·K) were applied to the top, bottom, and side surfaces of the core.

For a thermo-mechanical model, thermophysical properties (i.e., thermal conductivity, density, specific heat, and thermal expansion coefficient) and mechanical properties (i.e., elastic modulus) of all the involved components are needed. Table IV lists the BISON/MOOSE models used for thermo-mechanical simulations. BISON doesn't include models for KRUSTY’s U-7.65Mo fuel material, so the thermal conductivity of U-10Mo and specific heat of U-8Mo are currently applied. Other thermal and mechanical attributes for U-7.65Mo and B4C were sourced from the open literature or based on certain assumptions. The displacements in the “soft materials” regions are not solved using MOOSE’s tensor mechanics solver as the solid components. Instead, simple diffusion is used for these regions to simply propagate the deformation of the solid components.

**Table IV. Thermophysical and Elasticity Models Used for Thermo-Mechanical Simulation**

| BISON/MOOSE models                   | Materials | Description                                           |
|--------------------------------------|-----------|-------------------------------------------------------|
| HeatConductionMaterial               | UMo       | Thermal properties of UMo [18, 19]                    |
| BeOThermal                           | BeO       | Thermal properties of BeO                             |
| BeOElasticityTensor                  | BeO       | Young's modulus and Poisson's ratio for BeO           |
| BeOThermalExpansionEigenstrain       | BeO       | Computation of eigenstrain due to thermal expansion in BeO |
| SS316Thermal                         | SS        | Thermal properties of SS-316                          |
| SS316ElasticityTensor                | SS        | Young's modulus and Poisson's ratio for SS-316        |
| SS316ThermalExpansion Eigenstrain    | SS        | Computation of eigenstrain due to thermal expansion in SS316 |

As mentioned above, in the KRUSTY design, a thin insulation layer is used to thermally isolate the fuel from other components to reduce the loss of energy through heat transfer from fuel to reactor external surface. In order to simulate the thermal behavior of this thin insulation layer, MOOSE’s interface kernel is adopted to model the thermal resistance of the insulation layer without the need to explicitly mesh the thin structure. The thermal resistance of the insulation layer is tuned to match the reported behavior of KRUSTY (i.e., 30 W power leading to 200ºC fuel temperature).



## (b) MOOSE Multiphysics Coupling

In KRUSTY warm critical experiments, the power excursion transient was initiated by shifting the radial reflector of KRUSTY upward to insert positive reactivity when the reactor was in a cold/critical state [5]. This VTB model includes the multiphysics model prepared for modeling this reactivity insertion test. A two-level MOOSE MultiApps hierarchy which tightly couples the aforementioned Griffin neutronics model and BISON thermo-mechanical model has been developed to simulate the KRUSTY warm critical tests. Griffin is set as the parent (main) application and uses the DFEM-SN(2,3) solver with CMFD acceleration and NA=3 (anisotropic scattering order), while BISON is set as the child application.

The power density profile, initially calculated by Griffin, is then transferred to BISON. In BISON, thermo-mechanical computations take place, allowing for the determination of temperature distribution within all solid components. This subsequently leads to the calculation of thermal expansion. Both the fuel temperature profile and displacement field are then sent back to Griffin as feedback for the neutronics simulation. The coupling of these two applications occurs through fixed point iteration. Notably, in this calculation, all heat is passively removed through the external boundary of the reactor, as no heat pipes are involved.

The movement of the axial reflector to insert the reactivity was modeled by forcing a Dirichlet boundary condition on the bottom of the solid assembly that includes the axial reflector to allow BISON to calculate the consequent displacement field through solving tensor mechanics equations. Two steady-state eigenvalue calculations were included in the VTB model. These calculations were performed to confirm that an adequate amount of external reactivity was introduced for initiating the transient. The first one corresponds to the initial steady state for future transient simulation, and the second steady state corresponds to the first step of the transient after the reflector is shifted upward. In this calculation, the bias in the axial direction was set in the BISON input as shown in Figure 14 to generate the displacement in the mesh. The displacements were passed to Griffin neutronic for calculating the total reactivity inserted into the reactor using the MultiApp coupling framework.


!media media/KRUSTY/Fig_14.jpg
      style=display: block;margin-left:auto;margin-right:auto;width:60%;
      id=Fig_14
      caption=  Bison inputs for modeling the movement of the axial reflectors in KRUSTY warm critical experiments A) no displacement B) with displacement