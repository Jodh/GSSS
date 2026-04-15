# Documentation
## What is Structural Homology Assisted search for Related Proteins (SHARP)?
Aim of SHARP is to provide a series of steps that can assist scientists to find possible functions of unknown genes. We leverage the fact that proteins with similar 3d structures will have similar functions. The general pipeline involves: 
1. Predicting protein structures of unknown genes or genes with domains of unknown functions (DUFs) using alphafold3.
2. Find known proteins from the Uniprot database that have similar structures to the predicted structures, using GTalign.
3. Filter the results for good candidates.
4. Use insights from step 3 to label the unknown genes with possible family or domains.

We provide scripts involved with each step to allow for anyone to smoothly go through the pipeline for their own research. 

## The Pipeline
### Step 1: Predicting Structures using Alphafold3
The structures for genes with unknown functions can be predicted using the alphafold 3 model. If you have smaller volumes of genes to predict we recommend using the web interface for [alphafold3](https://alphafoldserver.com/) or [colabfold](https://github.com/sokrypton/ColabFold). 

Otherwise it is better to install the models on a local server with GPU capabilities. Use either [localcolabfold](https://github.com/YoshitakaMo/localcolabfold) or [alphafold3](https://github.com/google-deepmind/alphafold3) for installation instructions. The former option is easier but has more limited capabilities.

At the end of this step the output should be .pdb files for each gene in question. We will refer to those genes as queries or query set for the rest of this tutorial. The .pdb file describes the 3d structure of a given sequence of amino-acids. The structure can be viwed by using progams like [pyMOL](https://www.pymol.org/).
### Step 2: Homology Search using GT-Align and Parsing Outputs
[GtAlign](https://github.com/minmarg/gtalign_alpha) is a protein structure alignment, superposition and search tool. It can be either installed locally on a server or you can use the web service [here](https://bioinformatics.lt/comer/gtalign/) for smaller query sets.
This step requires a pdb file for each query in a single directory. 

If the directory name is `query_set` and GTAlign is installed locally then run the following command:

`gtalign -v --qrs=/your/path/query_set --rfs=/your/path/swissprot_pdb_v4.tar -o /your/path/output_dir --dev-queries-per-chunk=2 --speed=13 --pre-score=0.6`

Here `--qrs` specifies where the pdb files for the query set are stored. Provide the full address to the direcory on your machine.
`--rfs` is the address of all the structures present in the Uniprot dataset that can be downloaded from the uniprot website as a compressed file. It does not need to be extracted and can be used as is. `-o /your/path/output_dir` is where the output of GTAlign will be stored. `--speed` this option changes many other options to run the tool faster at the cost of accuracy. If you want to manually set up every option for gtalign, remove --speed as an option and instead type `gtalign` to bring out all the options or check their documentation [here](https://github.com/minmarg/gtalign_alpha/blob/master/out/gtalign_options.md).

The output of gtalign will be a text file for each query gene in the `output_dir`. The text file will include a table of candidate proteins and alignment for each of those candidates. 

If you want to skip the above steps we have provided example GT-Align outputs from our research [here](https://drive.google.com/drive/folders/17HHS3vhXfy5_Lsj3xZi2kRrH4NK-nqXK?usp=sharing).

Run `gtalign_output_parser.py your/path/output_dir num_lines` to convert these files into a single table using:

`python gtalign_output_parser.py your/path/output_dir num_lines`

The first option is the path to your gtalign output directory and second option is how many of the candidates you want to extract from the output, default is 300. For query genes with lower than the given amount, the script will automatically get the maximum amount possible.

At the end the output should be `gtalign_table` a csv file.
### Step 3: Filtering the Outputs
Many of the candidate proteins might not align with the query gene over the DUF we are interested in or they might be too small compared to the query size. Thus we need to filter out the false positives:
   - Filter By Overlap: Remove resuts that dont have a significant overlap with the DUF region. Default 50%
   - Filter By Relative Size: Remove results with sizes that are much smaller than the gene DUF. Default 50%

Use `python filter_gtalign_output.py duf_positions gtalign_table filtered_output` to filter your table. 

Here `gtalign_table` is the output of the previous step. `filtered_output` is the path and name of the output csv file. `duf_positions` is a csv file that should look like this:

﻿`Uniprot_ID`,`DUF_name`,`DUF_start`,`DUF_end`

`Q8SY72`,`DUF4519`,`1`,`55`

`A1ZB91`,`DUF4470`,`2`,`124`

...

The DUF_start and DUF_end are amino acid positions of the DUF region you are interested in.
### Step 4: Label the Ouputs
Each filtered output in the file `filtered_output` needs to be assinged a gene family or domain for the final step. For this we will use the Uniprot API. Use the following script to automatically assign the domains:

`python get_gene_family.py filtered_output labeled_output`. 

Here `filtered_output` is the name of the file we got from the previous step. `labeled_output` is the name of the output file. It should also be a csv file with the family name appended as a column.
### Step 5: Generate Frequency Tables and Assign Function to Unknown Genes or DUFs
Finally for each DUF, we will generate a table that counts the number of times each family or domain was found per query gene. This gives us a ranked list of candidate domains to assign to the DUFs. 

Use:

`python get_family_freq_tables.py labeled_output output_dir`

Here `labeled_output` is the path and name of the output file from the previous step and `output_dir` is the path to the directory where the output of this script will be stored.

The script will produce a number of tables equal to the number of unique DUFs among all query genes. Name of each of these tables will be the name of the DUF. Each of these files should look like this:

`family`,`count`

`Thioredoxin`,`86`

`Thioredoxin-like_sf`,`8`

`6-hairpin_glycosidase_sf`,`5`

...


