# Documentation
## What is Structural Homology Assisted search for Related Protiens (SHARP)?
Aim of SHARP is to provide a series of steps that can assist scientists to find possible functions of unknown genes. We leverage the fact that protiens with similar 3d structures will have similar functions[ref]. The general pipeline involves: 
1. Predicting protien structures of unknown genes or genes with domains of unknown functions (DUFs) using [alphafold3] we are interested in
2. Find protiens from the Uniprot database that have similar structures to the predicted structures, using [gt-Align]
3. Filter the Results-
   - If the unknown genes have domains of unknown functions (DUFs) that we are interested in, then select the protiens found in step 2 that only match over the DUFs
   - Remove results with sizes that are much smaller than the genes in question. 
4. Label protiens found in step 2 with their given family or domain using Uniprot and Interpro databases and count the unique family names.
5. The families that appear with the highest frequency are chosen as the final candidates for possible gene function or domain function.

We provide scripts involved with each step to allow for anyone to smoothly go through the pipeline for their own research. 

## The Pipeline
-insert image-
### Step 1: Predicting Structures using Alphafold3
### Step 2: Homology Search using GT-Align and Parsing Outputs
### Step 3: Filtering the Outputs
### Step 4: Label the Ouputs
### Step 5: Generate Frequency Tables and Assign Function to Unknown Genes or DUFs


