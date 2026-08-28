# NoStringsAttached
Find the Abaqus input files and Paraview results for the No Strings Attached Paper.

There are four tricuspid valve simulations included in this repository:

Pressure_Field: Simulation of the valve without any chordae architecture, with three steps - 1) inflation against a rigid template with lateral and vertical chordal mimicking forces present, 2) while inflated, turn on a locally-corrective pressure field, and 3) remove the rigid template while leaving the pressure field. 

Synthetic Chordae_#: After generating a generic synthetic chordal architecture with # chordae and calibrating the lengths, simulate the quasi-static closing of the valve. Three different densities of chordae are presented:

    - 202: Total number of chordae based on the average chordal density of the excised valve.
    
    - 225: Number of chordae based on the average chordal density of all excised donor valve's.
    
    - 450: Twice the number of chordae based on the average chordal density of all excised donor valve's.

Within each simulation directory, the necessary Abaqus input files (\*.inp) and user material/loading subroutines (\*.f) are provided. Additionally, Paraview files (\*.vtu) of the resulting simulation are provided.
