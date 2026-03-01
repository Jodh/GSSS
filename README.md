# Documentation
## What is Structural Homology Assisted search for Related Proteins (SHARP)?
Aim of SHARP is to provide a series of steps that can assist scientists to find possible functions of unknown genes. We leverage the fact that proteins with similar 3d structures will have similar functions. The general pipeline involves: 
1. Predicting protein structures of unknown genes or genes with domains of unknown functions (DUFs) using alphafold3.
2. Find known proteins from the Uniprot database that have similar structures to the predicted structures, using GTalign.
3. Filter the results for good candidates.
4. Use insights from step 3 to label the unknown genes with possible family or domains.

We provide scripts involved with each step to allow for anyone to smoothly go through the pipeline for their own research. 

## The Pipeline
-insert image-
### Step 1: Predicting Structures using Alphafold3
The structures for genes with unknown functions can be predicted using the alphafold 3 model. If you have smaller volumes of genes to predict we recommend using the web interface for [alphafold3](https://alphafoldserver.com/) or [colabfold](https://github.com/sokrypton/ColabFold). 

Otherwise it is better to install the models on a local server with GPU capabilities. Use either [localcolabfold](https://github.com/YoshitakaMo/localcolabfold) or [alphafold3](https://github.com/google-deepmind/alphafold3) for installation instructions. The former option is easier but has more limited capabilities.

At the end of this step the output should be .pdb files for each gene in question. We will refer to those genes as queries or query set for the rest of this tutorial. The .pdb file describes the 3d structure of a given sequence of amino-acids. The structure can be viwed by using progams like [pyMOL](https://www.pymol.org/).
### Step 2: Homology Search using GT-Align and Parsing Outputs
(GtAlign)[https://github.com/minmarg/gtalign_alpha] is a protein structure alignment, superposition and search tool. It can be either installed locally on a server or you can use the web service [here](https://bioinformatics.lt/comer/gtalign/) for smaller query sets.
This step requires a pdb file for each query in a single directory. 

If the directory name is `query_set` and GTAlign is installed locally then run the following command:

`gtalign -v --qrs=query_set --rfs=refernce_structs -o ouput_directory`

Here `--qrs` specifies where the pdb files for the query set are stored. Provide the full address to the direcory on your machine.
`--rfs` is the address of all the structures present in the Uniprot dataset that can be downloaded from the uniprot website as a compressed file. It does not need to be extracted and can be used as is. `-o output_drirectory` is where the output of GTAlign will be stored. 
### Step 3: Filtering the Outputs
   - If the unknown genes have domains of unknown functions (DUFs) that we are interested in, then select the protiens found in step 2 that only match over the DUFs
   - Remove results with sizes that are much smaller than the genes in question.
### Step 4: Label the Ouputs
### Step 5: Generate Frequency Tables and Assign Function to Unknown Genes or DUFs


