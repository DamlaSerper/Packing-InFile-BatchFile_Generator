# Packing-InFile-BatchFile_Generator - Application specific non-overlapping particle packing, in.file and batch file generator for LIGGGHTS-PUBLIC (uses MATLAB scripting)

This repository consists of two MATLAB scripts, PACCCK.mlx (function) and INNN.mlx, which are both written in MATLAB. PACCCK.mlx calculates critical DEM timestep and generates a non-overlapping packing of four size groups of particles within a cylindirical pipe. INNN.mlx generates LIGGGHTS script files (.in), batch files, replica cases and moves generated files to correct folders. These scripts are specific to case (cake formation in a vertical centrifugal filter) described in article: and includes new primitives and physics from https://github.com/DamlaSerper/SCM and https://github.com/DamlaSerper/DEMPhysics.

## Contributing Authors
MSc. Damla Serper (Aalto Univeristy, Finland) ***corresponding
Dr. Kevin Hanley (University of Edinburgh, UK)

## PACCCK.mlx

PACCCK is a function that generates particles within a cylindirical domain wihtout any overlap with each other.

The particles are part of four discrete size groups, with certain probabilities of being generated, here a size multiplier.

The particle are assigned coordinates starting from smaller sized ones, preventing large particles blocking the space.

The amount of particles are defined based on volumetric flow rate, solid content of suspension and feeding time.

The pipe height is adjusted depending on the dummy_liquid ratio. This means one can adjust this ratio to have less water, thus more dense packing. Also if one used size_mult to scale up particle size, they should also adjust the pipe height (radius kept same).

The function creates the packing and writes the information related to location and initial velocities in to a dump file format that is readable by LIGGGHTS-PUBLIC.

This function also has the option to calculate the DEM simulation timestep based on algorithm described in  https://doi.org/10.1002/nme.6056, if not already provided one. 

### The function takes the arguments below (units are in SI unless otherwise stated):

- **time_int:** *value in seconds* - How often to create dump files
- **calc:** *"yes", "no"* - Yes enables calculation of DEM timestep, while no results in critical timestep calculation from delta_tc_init
- **delta_tc_init:** *0, positive value* - 0 for calculating the timestep, value for using the previously calculated critical timestep
- **time:** *value in seconds* - How many of the seconds to print out as dump files
- **Boundary:** *"mesh", "prim", "both"*- This is required for generating the correct folder name for each case
- **prob_sph:** *[Par_prob_1 Par_prob_2 Par_prob_3 Par_prob_4]* - Variable row vector containing probability of each particle size class
- **r_sph:** *[Par_rad_1 Par_rad_2 Par_rad_3 Par_rad_4]* - Variable row vector containing raidus of each size class
- **size_mult:** *positive integer value* - The size multiplier used for scaling the particle sizes
- **pipe_filling:** *value between 0 and 1* - This signifies how much of the supplied pipe is filled
- **dummy_liquid_ratio:** *value between 0 and 1* - The ratio of liquid compared to reality, this is useful for creating denser packings
- **vol_suspension:** *positive value* - The volume of suspension
- **weightfrac_sph:** *value between 0 and 1* - The ratio of solid weigth and total suspension weight
- **rho_sph:** *positive value* - Density of the solids
- **rho_liq:** *positive value* - Density of the fluid
- **total_feeding_time:** *value in seconds* - Length of feeding period
- **r_cyl:** *positive value* - Feeding pipe radius
- **cyl_lower_coor:** *positive or negative value* - Lower coordinate of the feeding pipe
- **box_bounds:** *[x_lo x_hi; y_lo y_hi; z_lo z_hi]* - Matrix that is containing the x, y and z bounds of the DEM simulation domain
- **youngs_mod:** *positive value* - Young's modulus of solids
- **poissons_rat:** *positive value* - Poisson's ratio of solids
- **coeff_rest:** *positive value* - Coefficient of restituation of solids in liquid
- **rpm:** *positive value* - Rotation speed of the centrifugal filter inner basket (in rounds per minute)
- **r_basket_in:** *positive value* - Radius of the centrifugal filter inner basket
- **safety_factor_ts:** *value between 0 and 1* - Safety margin used in critical timestep calculations

### Function returns the arguments below:

- **safe_timestep:** *positive number* - Safe timstep of DEM simulation to be set-up including the safety factor
- **t_c/nu_steps_in_sec:** *positive integer value* - Number of steps in a second based on safe timestep
- **nu_sph_vec:** *positive integer value* - Number of particles generated in the cylindircal inlet pipe
- **dummy_velocity_of_fluid:** *positive or negative value* - Calculated velocity of the modified fluid
- **vy_p:** *positive or negative value* - y velocity of particles

## INNN.mlx

This script includes options for generating:

- in file for LIGGGHTS-PUBLIC
- batch file for batch job submission on supercomputer
- replicas
- cases with "mesh", mesh_free ("prim", SCM), or combination ("both") boundary representations
- cases with optional force switching (tangential Hertzian contact force, DEMPhysics gravitational force, bouyant force, DEMPhysics centrifugal force, DEMPhysics drag force (inactive currently), cohesive force (sjkr))
- cases with optional "mesh" or "prim" geometries and optional rotational speed

This script also copies and moves all the relevant files from the main directory to required folders.

By adjusting **transition fraction**, one can one or multiple parallel run cases based on first initial critical timestep calculation and dump file generation. For example [0] means "mesh" boundary representation, while [0 0.2 0.5] means ("both") we will have three cases one being "mesh" and other being "prim" with different transition ratios. The primitive cases will use the same dump file that was generated at "mesh" case. For further information on these primitives and transition ratios check SCM documentation.

In this file, every replica case can be given a different supercomputer project number to ease of handling.

For further details, check the source code and the supplied links.





