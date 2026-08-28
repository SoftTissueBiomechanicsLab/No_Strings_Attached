# No Strings Attached

Finite-element simulations accompanying **“No Strings Attached: Predicting Tricuspid Valve Deformation Without In Vivo Chordal Geometry.”** The study develops a method for predicting tricuspid-valve deformation without first reconstructing subject-specific chordae tendineae from medical images.

## Overview

The workflow combines:

- hyperelastic shape matching between end-diastolic and end-systolic leaflet configurations;
- downward and lateral chordal-mimicking forces to substitute for the unresolved subvalvular apparatus;
- a rigid contact template and a Gaussian-smoothed, locally corrective pressure field (LCPF) to refine leaflet shape;
- zone-based rejection sampling to generate synthetic chordal insertion sites; and
- stress-based calibration of each synthetic chord’s unloaded length.

The simulations use the openly available, subject-specific **Texas TriValve 1.1** finite-element model of a human tricuspid valve. The Abaqus input files contain the model geometry, material parameters, boundary conditions, amplitudes, and analysis steps used in the manuscript.

## Repository contents

| Directory | Description | Abaqus inputs | User subroutines | VTU results |
| --- | --- | ---: | ---: | ---: |
| [`Shape_Matching/`](Shape_Matching/) | Shape matching without explicit chordal geometry. | 11 | 1 | 61 |
| [`Synthetic_Chordae_202_Density/`](Synthetic_Chordae_202_Density/) | Synthetic chordal configuration with 202 insertions. | 8 | 1 | 42 |
| [`Synthetic_Chordae_225_Count/`](Synthetic_Chordae_225_Count/) | Synthetic chordal configuration with 225 insertions. | 8 | 1 | 42 |
| [`Synthetic_Chordae_450_2x_Count/`](Synthetic_Chordae_450_2x_Count/) | Synthetic chordal configuration with 450 insertions, twice the 225-insertion count. | 8 | 1 | 42 |

Each simulation directory contains Abaqus `*.inp` files, a Fortran `*.f` user subroutine, and exported `*.vtu` files for visualization in ParaView. Files are numbered to indicate the major parts of the model setup and analysis sequence:

| Prefix | Role |
| --- | --- |
| `00_VALVE` | Base valve model and mesh definitions |
| `01_SECTIONS` | Element sections and associated properties |
| `02_MATERIALS` | Constitutive material definitions |
| `03_ASSEMBLY` | Assembly, sets, surfaces, and interactions |
| `04_AMP` | Loading and boundary-condition amplitudes |
| `05_SURFACE` | Surface and contact definitions |
| `06_STEP*` onward | Analysis steps and step-specific loading |
| `20_RIGID` | Rigid-template definitions used by shape matching |

## Simulation workflow

### Shape matching

The [`Shape_Matching/`](Shape_Matching/) case proceeds in three stages:

1. The valve is inflated against a rigid template while downward and lateral chordal-mimicking forces guide leaflet contact and tethering.
2. The LCPF is activated while the valve remains against the template. The pressure field penalizes displacement from the target end-systolic surface at 10 kPa/mm.
3. Contact with the rigid template is removed. The leaflets equilibrate under transvalvular pressure, chordal-mimicking forces, and the LCPF.

The manuscript reports a mean inter-surface distance of **0.29 +/- 0.35 mm** between the predicted and target leaflet surfaces. The calibrated chordal-mimicking forces were rounded to **1.0 N downward** and **0.4 N lateral** for the reported simulations.

### Synthetic chordae

The synthetic-chordae cases start from the matched valve geometry. Chordal insertion sites are generated from anatomical zones using rejection sampling. The unloaded length of each chord is then calibrated from its reaction force and chordal stress-stretch relationship before quasi-static valve closure is simulated.

The manuscript compares 202, 225, and 450 insertions. Increasing the insertion count reduced mean inter-surface distance from **0.63 +/- 0.52 mm** to **0.49 +/- 0.44 mm**. The 450-insertion configuration reproduced the target leaflet contact area within **0.59%**. Across configurations, mean maximum-principal-stretch errors in leaflet-belly regions remained below **2.4%**, while areal-strain errors ranged from **0.34% to 10.01%**.

## Requirements

To rerun the simulations, install:

- **Abaqus/Explicit 2020 or a compatible Abaqus release**;
- a Fortran compiler supported by that Abaqus installation, for the user subroutines; and
- **ParaView** for viewing the exported VTU results.

The repository does not include a project-level run script. Abaqus jobs should be launched from the relevant simulation directory so that referenced input files and user subroutines resolve relative to that directory. The exact command depends on the local Abaqus installation; a typical pattern is:

```text
abaqus job=<job-name> input=<input-file>.inp user=<subroutine>.f interactive
```

Use the numbered input files in the order required by the case. Inspect the `*INCLUDE`, `*MATERIAL`, `*STEP`, and user-subroutine references in the top-level input file before launching a run, since job names and the active entry point may vary with the Abaqus environment.

## Viewing results

Open the `*.vtu` files in ParaView. The numbered files represent successive exported simulation states. Load a sequence as a time series when ParaView recognizes the common `Shape_Matching_<n>.vtu` or case-specific filename pattern; otherwise, select the files together and use **File > Open** to inspect individual states.

## Data and related resources

The manuscript uses **Texas TriValve 1.1**, which is available from the Soft Tissue Biomechanics Lab:

- [Texas TriValve 1.1](https://github.com/SoftTissueBiomechanicsLab/Texas_TriValve_1.1)
- [This repository](https://github.com/SoftTissueBiomechanicsLab/No_Strings_Attached)

This repository provides the Abaqus inputs, user subroutines, and VTU result files for the simulations. It does not provide the manuscript’s source data, medical images, or a general-purpose automated preprocessing pipeline.

## Citation

Please cite the accompanying manuscript when using these files:

> Mathur, M., Haese, C. E., Dubey, V. K., Seetharam, S., Meador, W. D., Jazwiec, T., Simonian, N. T., Sacks, M. S., Malinowski, M., Timek, T. A., and Rausch, M. K. “No Strings Attached: Predicting Tricuspid Valve Deformation Without In Vivo Chordal Geometry.”

Add the final journal, year, DOI, and version information here once they are available.

## License

See [`LICENSE-CC-BY.txt`](LICENSE-CC-BY.txt) for the repository’s license terms. Please also review the terms of any third-party data or software referenced by the manuscript, including Texas TriValve 1.1, Abaqus, and ParaView.
