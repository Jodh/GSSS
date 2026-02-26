# Documentation
## What is Structural Homology Assisted search for Related Protiens (SHARP)?
Aim of SHARP is to provide a series of steps that can assist scientists to find possible functions of unknown genes. We leverage the fact that protiens with similar 3d structures will have similar functions[ref]. The general pipeline involves: 
1. Predicting protien structures of unknown genes using [alphafold3] we are interested in
2. Find protiens from the Uniprot database that have similar structures to the predicted structures
3. (Optional) If the unknown genes have domains of unknown functions (DUFs) that we are interested in, then select the protiens found in step 2 that only match over the DUFs
4. Label protiens found in step 2 with their given family or domain using Uniprot and Interpro databases and count the unique family names.
5. The families that appear with the highest frequency are chosen as the final candidates for possible gene function.

We provide scripts involved with each step to allow for anyone to smoothly go through the pipeline for their own research.

