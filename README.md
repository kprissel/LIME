# Log-ratio Inversion of Mixed Endmembers (LIME)
Log-ratio Inversion of Mixed Endmembers (LIME) is a program for calculating the phase proportions in a rock given a bulk composition and the composition of each phase. The mathematical details, outputs, and example applications are fully described in the main text. Here, instructions are presented for performing calculations within LIME.

## Getting started
The plotting commands and statistical functions in the source code for LIME require that MATLAB version 2019a or later, including the “Statistics and Machine Learning Toolbox”, be installed on your machine in order to perform a LIME calculation. To
begin using LIME, add the provided files to your MATLAB path.

### Contents of LIME.zip
- lime_input.m: This is the front-end, user-interfacing file for computing with LIME. User inputs and preferences are given, and a calculation can be completed from start to finish by running this script.
- Inputs (folder): Example input files for compositional data including .xlsx and .txt formats. Use these files as templates for entering your own data into LIME.
- main_script.m: Serves as the main engine for the code by calling upon subscripts. Called in lime input.m for each input composition.
- preplime.m: Function that prepares user-input data for the formatting expected in subsequent scripts. This allows the user to input the compositional data with any oxide order and include phase labels that will be used in LIME figures.
- clr.m: Function that computes the centered log-ratio transform from the D-dimensional SD simplex to R(D) (Aitchison, 1986).
- clrinv.m: Function that computes the inverse centered log-ratio transform from R(D) to the D-dimensional SD simplex (Aitchison, 1986).
- draw_compos.m: Function that generates a set of random compositions with a specified mean and standard deviation.
- forwardilr.m: Function that computes the forward composition problem in ilr formulation.
- ilr.m: Function that computes the isometric log-ratio transform from the D-dimensional SD simplex to R(D-1) (Egozcue et al. 2003).
- ilrinv.m: Function that computers the inverse-isometric log-ratio transform from R(D-1) to the D-dimensional SD simplex (Egozcue et al. 2003).
- lime.m: Function that computes the logratio-inversion of mixed endmembers.
- OKnorm.m: Function that amalgamates oxides in a pre-defined way, as described in the manuscript text (Section 2.1).

## Data inputs required for LIME
Data required for LIME are the average bulk composition and the compositions of each phase, all reported as weight percent oxides (SiO2, TiO2, Al2O3, Fe2O3, Cr2O3, FeO, MnO, MgO, NiO, CaO, Na2O, K2O, P2O5, H2O). Uncertainty on the bulk composition can be input as generalized percent or using specified values for each oxide. The uncertainty on each phase composition will be determined based on the standard deviation of all the compositions input for a given phase. Because of this, you must give at least two compositional analyses for each phase. For data that has been published as averages and standard deviations of n points, you can reproduce a synthetic dataset with those statistics using draw_compos.m. To do this, run the function draw_compos(N, mean, std), where "N" is the number of analyses to be generated, mean is the average of those analyses, and "std" is the standard deviation of the normal distribution that will be used to randomly generate compositions. Note that for the compositional inputs, FeO and Fe2O3 get amalgamated to total FeO (FeOT), so the phase abundance calculation will not directly include variations in Fe oxidation state. Though NiO and H2O are accepted as inputs, the current version of LIME zeroes these oxides out prior to performing a calculation.

Data are input as either a spreadsheet or text file. There are multiple input file examples included in the Inputs folder. We suggest using TEMPLATE.xlsx to get started with your own data. The format of the input files has columns for the phase label and
oxides. In this version of LIME, the phase label must be the first column but the oxides can be given in any order. After the row of column labels, each subsequent row corresponds to a composition.

The bulk composition is listed first in TEMPLATE.xlsx, and in this version of LIME the bulk composition must be given the “bulk” phase label. If the uncertainty (wt.%) is known for each oxide in “bulk”, include these values in a subsequent row labeled “U”. If no “U” labeled row is given for uncertainty on the bulk composition, the pct err bulk given in lime_input.m will be used instead (1% uncertainty default). The remaining rows are compositional analyses for the phases, with the phase label corresponding to each row given in the first column (e.g., “phase1”). During a routine, the subfile preplime.m will find the “bulk” and “U” entries, rearrange the oxide columns to be in the expected order for the rest of the calculations, and group the rows of analyses according to the phase labels. Be careful to use the exact same label (matching characters and title case) for each row that is meant to represent the same phase. For example, if phases are labeled “clinopyroxene” and one of the clinopyroxene rows is given as “cpx”, the code will treat these are separate phases. Another common issue when users first start with LIME is the oxide column labels. Be sure to use the oxide labels given in the example input files (e.g., “SiO2”); any additional spaces or characters in the oxide column names will prevent the code from finding the expected oxide (e.g., “wt% SiO2” or “SiO 2”).

## Running the Code
Aside from the input files, LIME users will only need to interface with lime_input.m to successfully perform a calculation. Once the input file is prepared, open lime input.m in MATLAB. Give the file path for the input file in the filename variable at Line 7. Multiple file names can be given, with each file name string separated by a comma. When running multiple input files at once, we recommend saving the outputs (set ouput_yn = 1 on Line 11), because variable names will be overwritten with each code iteration.

- User-Specified Parameters, Lines 9-12: Here you can define a percent uncertainty in the bulk composition (Line 10), choose whether to save the result outputs as a separate file (Line 11), and choose whether to display the result convergence plot to assess the performance of the code (Line 12).
- Additional Parameters, Lines 19-28: These parameters relate to the ilr conversion and calculations in ilr space. They also define how many synthetic compositions to generate for the bulk composition (Line 23) and how many samples to generate for the posterior distribution (Line 28). The specific details of these calculations are given in manuscript text Section 2.1.

The variable NEWT.error_measure defines how the reported error will be calculated. When this variable is set to “1”, the reported errors on the posterior distribution will be the 1-sigma equivalent of a Gaussian distribution (15.9 and 84.1 percentiles). If the variable is set to “2”, the errors will be the 2-sigma error equivalent for a Gaussian distribution (2.3 and 97.7 percentiles). Because not all posterior distributions have a Gaussian distribution (see asymmetry in Section 3), you can also define a vector of two percentile values to be used in the error calculation instead. If a vector of two values is specified, those percentiles will be used instead of the default 15.9 and 84.1. The provided lime_input.m file sets the reported errors to be the 25th and 75th percentiles of the posterior distribution.
