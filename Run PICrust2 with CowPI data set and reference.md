#7/29/21
#Work to replicate CowPI results from original paper using PICrust2 and the CowPI database as a reference

```
--Path to Juliana's folder for .biom files for reference
/lustre/project/rumen_longread_metagenome_assembly/analysis/buccal/test_juliana/ASV/GWAS/Rumen/Picrust/INPUT

-- Create and activate a conda environment with mamba and try to install PICrust2

module load mpc/1.0.3 gmp/6.1.0 mpfr/3.1.3 gcc/8.1.0 openblas/0.2.15 python_3/3.6.6 miniconda/3.6

conda create -p /KEEP/rumen_longread_metagenome_assembly/picrust2 -c bioconda -y -c conda-forge mamba

conda activate /KEEP/rumen_longread_metagenome_assembly/picrust2

mambda install -c bioconda -c conda-forge picrust2=2.4.1
```
>This seems to have worked. picrust2 is shown in the list of installed packages now in the active environment.

> =Copied cowpi_db to my folder

```
cp -R CowPI_db /lustre/project/rumen_longread_metagenome_assembly/tfuller/cowpi/

--added the 16s and ko default tables from picrust2 github to compare as well in cowpi/picrust2_defaults
```

>Huws OTU abundance table is the table to use to get .biom file which is one of main inputs. Check to see if "total" column needs removed though

>Huws OTU.fas.txt is the representative sequence for each OTU from data. This is "Study-seqs" and one of main inputs.

>for whole pipeline --marker_gene_table is 16S_precal...., --custom_trait_table and --reaction_function are both ko_precalc1, use -no_regroup option

>If pipeline is ran individually use --ref_dir in placement call and point to directory of reference files. Also use --observed_trait_table in hsp.py to call 16s_precalc... and ko_precalc

```
--Remove "total" column from Huws abundance table just in case it would conflict later

-remove last column by making space delimited file
awk 'NF{NF-=1};1' Huws_16S_colonisation_OTU_abundance_Table.txt > test_table.txt

-convert space delimited to tab delimited
awk -v OFS='\t' '{ $1=$1; print }' test_table.txt > test2.txt``

-rename using the mv function and remove the old file with rm

mv test2.txt Huws_16S_colonisation_OTU_abundance_table_clean.txt

rm test_table.txt
```

#8/10/21 Install RAxML-NG into picrust2 environment to run RAxML-NG in order to generate tree file and model file

```
module load cmake miniconda/3.6 gcc

conda activate /KEEP/rumen_longread_metagenome_assembly/picrust2/

conda install -c bioconda raxml-ng

> Perform run of raxml-ng on all_rumen_16S file (reference database) to generate  a tree file and ultimately a model file

sbatch -N 1 -n 2 --mem=50000 -t 1-0 -p priority -q msn --wrap="raxml-ng --msa CowPi_V1.0_all_rumen_16S_combined.renamed.fas --model GTR+G --threads 10 --prefix raxml_output/cowpi_ref"

job id 6254889
```

##NOTE look into all rumen 16s. Not sure this includes the 497 hungate genomes. There may be a different file that represents the full reference data. After talking to juliana this does seem right. raxml-ng took too long to run though. running --parse estimates 441403MB of memory and using at least 6 threads

###RANDOM NOTE

awk -F: '{if($2>10)print$2}' <filename 

to check if column 2 greater than 10 in file.

pump into wc -l for count of rows