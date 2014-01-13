Software Documentation
https://github.com/integrativemodeling/hybrid_ms_method
STEPS
Data and scripts to model protein complexes
The presented software package capable of sampling and evaluating candidate structures from information derived from mass-spectrometric based data. The method exploits a Monte Carlo algorithm combined with an optimization step for generating “good” candidate structures consistent with the input data.  From a large number of candidate model structures generated by sampling the conformational spaces of input structures (at the moment two input coordinate files are allowed), the algorithm gives a score (MS connectivity score) based on a Minimal Spanning Tree approach1.
To further evaluate the candidate models, the closeness of fit of calculated collision cross sections (CCSs) with their experimental counterparts is computed. Prior to comparison, the calculated CCS is scaled according to the equation below, which accounts for the underestimation of the projection approximation (PA) method2,3 and the missing residues in the input coordinate files 4.
CCS’=1.14CCS(Mexp/Mpdb)2/3
Moreover, this package is capable of computing the goodness of fit of candidate models with the cross-linkng experimental data by measuring the feasibility of residue-specific interactions within these structures. We have divided this documentation in two parts, the sampling of candidate docked structures and the evaluation of structures generated by comparison with the input data.

A: Generation and visualization of structures using a Monte Carlo algorithm followed by conjugated gradient optimization
Script name: Sampling.py
Input Fields:
•	Coordinate files: The input coordinate files, in pdb format, are given as inputs to the code. The code supports coarse grained modelling as soon as it is in coordinate type of file. At the moment, a maximum of two coordinate file can be specified.
•	Number of models to be generated: the number of model to generate as an output. A value of 10,000 models is recommended

Optional Input Fields (Optimization runs):
•	number_of_monte_carlo_steps:  Specify the number of Monte Carlo steps to take for each optimization step.
•	number_of_conjugate_gradient_steps: Specify the number of conjugated gradient optimization steps (default number:100)
•	Clustering: Specify the number of clusters to be generated and the number iterations. A k-means clustering approach is followed for generation of the clusters. Overall, more iterations produce better clusters.

Output:
The ouput of Sampling.py is a list of candidate structures formed by docking together the input coordinate files. The list of models is given to the user in the *.pym type of file ready to be opened by Pymol package. Also, a list of scores calculated using the MS connectivity restraint is produced in *.csv type of file.
CSV Format:
•	Model number: Number of solution generated
•	Score: MS connectivity restraint. The produced score for each solution generated is calculated using the Minimal Spanning Tree (MST) algorithm

Usage:  To run the sampling script takes three arguments:
Arg1: First Coordinate File (coordinateFileA)
Arg1: Second Coordinate File (coordinateFileB)
Arg3: number of models to generate (NumberModels)

usage: python sampling.py  CoordinateFileA  CoordinateFileB  NumberModels
Description of optimization process
We use a randomize sampler to generate the docked model structures as implemented in integrative modelling platform (IMP; http://www.integrativemodeling.org). This sampler (IMP::core::MCCGSampler) finds random conformations and then uses a conjugates gradient optimization step to search for “good” model structures. Each Monte Carlo step is followed by an optimization step that decides to accept or reject the conformation. The sampling process involves three nested loops: i) number of attempts to find acceptable conformations, ii) number of Monte Carlo steps, iii) number of conjugated gradient steps. For acceleration of the model generation process, the input structures are coarse-grained down to residue level, where each sphere represents a residue. Scaling of the sphere is obtained through the corresponding residue mass.
Visualization of generated structures
The produced structures are generated as *.pym type of file to be displayed in Pymol. The used writer supports points and spheres.
*Running the sampling.py requires installation of IMP. Information on how to install IMP, can be found here:
http://salilab.org/imp/nightly/doc/html/md__tmp_nightly-build-40773_imp-20130701_8develop_8498662f_doc_installation.html#installation
B: Evaluation of candidate model Structures
The output structures are subjects to evaluation by measuring their closeness-to-fit with the experimental data. The first step is to convert the *.pym type of files into *.mfj that can be read by Mobcal Code (Calculation of theoretical CCS). To do so, the pym2mfj.py script can be used. 
usage: python pym2mfj.py InputFileName OutputFileName
To evaluate the violation of cross-linking restraints, the scoringXlinks.py script is used. This script reads a *.txt file with the information about the location of cross-links identified by the experiments and after iteration over the candidate structures generated from the previous step, it calculates the overall violation of cross-link restraints for each structure. The cut-off of the physical distance between two cross-linked residues is set <30Å (25-30Å). This value is used as an upper bound threshold to judge whether or not a cross-link is violated. The proteins flexibility as well as the curvature of a non-euclidean distance is also taken into account. 
Usage: python scoringXlinks.py  XLinfo.txt CutoffDistance

Input Fields:
•	XLInfo.txt: The input *.txt file contains information about the cross-linked peptides/segments. Below is an example of such an input file:
No	Input File 	Chains
(interacting)	1st Residue 
Name	1st 
Residue
number	2nd
Residue 
Name	1st 
Residue
number
1	1mty	db	LYS	113	LYS	65
2	1mty	db	LYS	34	LYS	160
3	1mty	db	LYS	22	LYS	295
4	1mty	db	LYS	22	LYS	98
5	1mty	db	LYS	225	LYS	40


Output:
The ouput of ScoringXlinks.py is a list of candidate structures, which we iterated over, with a given number representing the violation of restraints for each of them. The output is printed in *.csv type of file.
CSV Format:
•	Model number: Number of solution structure.
•	Violation Score: The violation of cross-links score specified by the cut-off distance.

Collision Cross Section Calculations
The CCS values for each candidate structure are calculated by calling the mobcal code (“mobcal_cg”) from a python script. This script reads an *.mfj type of file and iterates over all the input structures by printint the projection approximation (PA) value for each of them in a *.csv type of file.  
Usage: python mobcal.py *.mfj
Output (CSV Format):
•	Model number: Number of solution structure.
•	PA value: Calculated CCS from PA method in Å2
Info
Author(s): Argyris Politis
Version: 1.0
License: LGPL. This library is free software; you can redistribute it and/or modify it under the terms of the GNU Lesser General Public License as published by the Free Software Foundation; either version 2 of the License, or (at your option) any later version.
Last known good IMP version: XXXX
Testable: Yes.
Parallelizeable: No
Publications:
•	Hall Z, Politis A, Robinson CV. [Structural modeling of heteromeric protein complexes from disassembly pathways and ion mobility-mass spectrometry] Structure 20, 1-14, 2012.
•	Politis A, Park AY, Hall Z, Ruotolo BT, Robinson CV.[Integrative modelling coupled with ion mobility mass spectrometry reveals structural features of the clamp loader in complex with single-stranded DNA binding protein] Journal of Molecular Biology 425, 4790-4801, 2013. 
•	Alber, F.; Dokudovskaya, S.; Veenhoff, L. M.; Zhang, W.; Kipper, J.; Devos, D.; Suprapto, A.; Karni-Schmidt, O.; Williams, R.; Chait, B. T.; Rout, M. P.; Sali, A. Nature 2007, 450, 683-94.
•	Mesleh, M. F.; Hunter, J. M.; Shvartsburg, A. A.; Schatz, G. C.; Jarrold, M. F. J. Phys. Chem 1996, 100, 16082-16086.
•	Shvartsburg, A. A.; Jarrold, M. F. Chem. Phys. Lett. 1996, 261, 86-91.
