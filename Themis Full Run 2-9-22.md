#2-9-22
##This is a full run of themis. This run will have 9 total assemblies and 4 sets of paired end short read data


>Start by creating a new project folder in /lustre/project/forage_assemblies/ folder, cloning github repo into it, and creating symbolic links for everything

```bash
srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

module load python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/themis_fuller/

cd /lustre/project/forage_assemblies/

mkdir tfuller

cd tfuller

git clone https://github.com/tdfuller54/Themis-ASM.git

cd Themis-ASM


-- Note submodule folder is now empty so need to run extra command to pull in submodule

-- in Themis-ASM directory

git submodule update --init --recursive
```

next up create symbolic links for everything in Themis-ASM directory

```bash
-name (short name)

1-hairy vetch (Vvillosa)
ln -s /lustre/project/forage_assemblies/assemblies/vetch/vetch_phase.assembly.fasta vetch_phase.assembly.fasta

2-Lens culinaris (Lentil)
ln -s /lustre/project/forage_assemblies/analysis/vetch/comp_assemblies/Lens_culinaris_2.0.fasta Lens_culinaris_2.0.fasta

3-Lathyrus sativus (GrassPea)
ln -s /lustre/project/forage_assemblies/analysis/vetch/comp_assemblies/Ler.1DRT.fasta Ler.1DRT.fasta

4-V unguiculate (CowPea)
ln -s /lustre/project/forage_assemblies/assemblies/legume_genomes/Vunguiculata_469_v1.0.fa Vunguiculata_469_v1.0.fa

5-P vulgaris (CommonBean)
ln -s /lustre/project/forage_assemblies/assemblies/legume_genomes/Pvulgaris_442_v2.0.fa Pvulgaris_442_v2.0.fa

6-Pisum sativum (Pea)
ln -s /lustre/project/forage_assemblies/assemblies/legume_genomes/Pisum_sativum_v1a.fa Pisum_sativum_v1a.fa

7-Medicago truncatulata (MedTr)
ln -s /lustre/project/forage_assemblies/assemblies/legume_genomes/MtrunA17r5.0-20161119-ANR.genome.fasta MtrunA17r5.0-20161119-ANR.genome.fasta

8-T. pratense (RedClover)
ln -s /lustre/project/rumen_longread_metagenome_assembly/assemblies/clover_ccs/placed_scaffs/ARS_DFRC_1.1.fasta ARS_DFRC_1.1.fasta

9-V. sativa (CommonVetch)
ln -s /lustre/project/forage_assemblies/analysis/vetch/comp_assemblies/Vicia_sativa_reorg.fa Vicia_sativa_reorg.fa


-short paired end reads


-lane1
ln -s /lustre/project/forage_assemblies/sequence_data/vetch_illumina/HV30Vetch_L001-ds.f5daa53646d64f4bac4fc79cf6e436ab/HV30Vetch_S1_L001_R1_001.fastq.gz HV30Vetch_S1_L001_R1_001.fastq.gz

ln -s /lustre/project/forage_assemblies/sequence_data/vetch_illumina/HV30Vetch_L001-ds.f5daa53646d64f4bac4fc79cf6e436ab/HV30Vetch_S1_L001_R2_001.fastq.gz HV30Vetch_S1_L001_R2_001.fastq.gz

lane2
ln -s /lustre/project/forage_assemblies/sequence_data/vetch_illumina/HV30Vetch_L002-ds.9bf58b47e3d34fbda00e6b36aa27d6a8/HV30Vetch_S1_L002_R1_001.fastq.gz HV30Vetch_S1_L002_R1_001.fastq.gz

ln -s /lustre/project/forage_assemblies/sequence_data/vetch_illumina/HV30Vetch_L002-ds.9bf58b47e3d34fbda00e6b36aa27d6a8/HV30Vetch_S1_L002_R2_001.fastq.gz HV30Vetch_S1_L002_R2_001.fastq.gz

lane3
ln -s /lustre/project/forage_assemblies/sequence_data/vetch_illumina/HV30Vetch_L003-ds.4ef7347a6fb24caba2038acc5247d3e6/HV30Vetch_S1_L003_R1_001.fastq.gz HV30Vetch_S1_L003_R1_001.fastq.gz

ln -s /lustre/project/forage_assemblies/sequence_data/vetch_illumina/HV30Vetch_L003-ds.4ef7347a6fb24caba2038acc5247d3e6/HV30Vetch_S1_L003_R2_001.fastq.gz HV30Vetch_S1_L003_R2_001.fastq.gz

lane4
ln -s /lustre/project/forage_assemblies/sequence_data/vetch_illumina/HV30Vetch_L004-ds.19bf16c029ab43d38bb477601fd81a1c/HV30Vetch_S1_L004_R1_001.fastq.gz HV30Vetch_S1_L004_R1_001.fastq.gz

ln -s /lustre/project/forage_assemblies/sequence_data/vetch_illumina/HV30Vetch_L004-ds.19bf16c029ab43d38bb477601fd81a1c/HV30Vetch_S1_L004_R2_001.fastq.gz HV30Vetch_S1_L004_R2_001.fastq.gz
```

>Done with creating symbolic links. Everything should now be ready to run Themis!

```bash

sbatch -N 1 -n 2 --mem=50000 -t 7-0 -p priority -q msn --wrap="python themisASM.py  \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/vetch_phase.assembly.fasta  \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Lens_culinaris_2.0.fasta  \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Ler.1DRT.fasta \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Vunguiculata_469_v1.0.fa \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Pvulgaris_442_v2.0.fa \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Pisum_sativum_v1a.fa \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/MtrunA17r5.0-20161119-ANR.genome.fasta \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/ARS_DFRC_1.1.fasta \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Vicia_sativa_reorg.fa \
	-n Vvillosa \
	-n Lentil \
	-n GrassPea \
	-n CowPea \
	-n CommonBean \
	-n Pea \
	-n MedTr \
	-n RedClover \
	-n CommonVetch \
	-b eudicots_odb10 \
	-f /lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L001_R1_001.fastq.gz,/lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L001_R2_001.fastq.gz \
	-f /lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L002_R1_001.fastq.gz,/lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L002_R2_001.fastq.gz \
	-f /lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L003_R1_001.fastq.gz,/lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L003_R2_001.fastq.gz \
	-f /lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L004_R1_001.fastq.gz,/lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L004_R2_001.fastq.gz \
	-s lane1 \
	-s lane2 \
	-s lane3 \
	-s lane4 \
	-j 50 \
	-c 'sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -t 5-0 --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}'"
```

Needed to correct run_busco input to know download_busco output is what it wanted but script is now running!

#2/11/22
##Derek was able to set the permissions to the two assembly files that returned errors rerunning again now.

common bean and cow pea gave permission errors

/lustre/project/forage_assemblies/assemblies/legume_genomes/Pvulgaris_442_v2.0.fa

/lustre/project/forage_assemblies/assemblies/legume_genomes/Vunguiculata_469_v1.0.fa

```bash
srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

module load python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/themis_fuller/

-- check to make sure assembly files can be opened

less /lustre/project/forage_assemblies/assemblies/legume_genomes/Pvulgaris_442_v2.0.fa

less /lustre/project/forage_assemblies/assemblies/legume_genomes/Vunguiculata_469_v1.0.fa

-- Move to directory, unlock it, and resume job

cd /lustre/project/forage_assemblies/tfuller/Themis-ASM/

snakemake --snakefile themisSnakefile --unlock

sbatch -N 1 -n 2 --mem=50000 -t 7-0 -p priority -q msn --wrap="python themisASM.py  \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/vetch_phase.assembly.fasta  \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Lens_culinaris_2.0.fasta  \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Ler.1DRT.fasta \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Vunguiculata_469_v1.0.fa \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Pvulgaris_442_v2.0.fa \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Pisum_sativum_v1a.fa \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/MtrunA17r5.0-20161119-ANR.genome.fasta \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/ARS_DFRC_1.1.fasta \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Vicia_sativa_reorg.fa \
	-n Vvillosa \
	-n Lentil \
	-n GrassPea \
	-n CowPea \
	-n CommonBean \
	-n Pea \
	-n MedTr \
	-n RedClover \
	-n CommonVetch \
	-b eudicots_odb10 \
	-f /lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L001_R1_001.fastq.gz,/lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L001_R2_001.fastq.gz \
	-f /lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L002_R1_001.fastq.gz,/lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L002_R2_001.fastq.gz \
	-f /lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L003_R1_001.fastq.gz,/lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L003_R2_001.fastq.gz \
	-f /lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L004_R1_001.fastq.gz,/lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L004_R2_001.fastq.gz \
	-s lane1 \
	-s lane2 \
	-s lane3 \
	-s lane4 \
	-j 50 \
	-c 'sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -t 5-0 --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}' \
	-r"

--Found error in run_busco because of change to input when calling index in script. Made edit to fix this but still run download_busco first. Cleared and removed all files to rerun everything from the very beginning to make sure this still runs all of the rules properly.

git pull


sbatch -N 1 -n 2 --mem=50000 -t 7-0 -p priority -q msn --wrap="python themisASM.py  \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/vetch_phase.assembly.fasta  \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Lens_culinaris_2.0.fasta  \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Ler.1DRT.fasta \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Vunguiculata_469_v1.0.fa \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Pvulgaris_442_v2.0.fa \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Pisum_sativum_v1a.fa \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/MtrunA17r5.0-20161119-ANR.genome.fasta \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/ARS_DFRC_1.1.fasta \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Vicia_sativa_reorg.fa \
	-n Vvillosa \
	-n Lentil \
	-n GrassPea \
	-n CowPea \
	-n CommonBean \
	-n Pea \
	-n MedTr \
	-n RedClover \
	-n CommonVetch \
	-b eudicots_odb10 \
	-f /lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L001_R1_001.fastq.gz,/lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L001_R2_001.fastq.gz \
	-f /lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L002_R1_001.fastq.gz,/lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L002_R2_001.fastq.gz \
	-f /lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L003_R1_001.fastq.gz,/lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L003_R2_001.fastq.gz \
	-f /lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L004_R1_001.fastq.gz,/lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L004_R2_001.fastq.gz \
	-s lane1 \
	-s lane2 \
	-s lane3 \
	-s lane4 \
	-j 50 \
	-c 'sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -t 5-0 --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}'"
```

#2/14/22
###Will run dotplot outside of themis per Derek's response to capture standard error. If need be may even try to run off ceres and on laptop. Will also make adjustment to minimap_align memory alotment and consider adding subfolders in log for each comparison so they do not get overwritten

--Not a file size issue because completed runs had larger input files with more lines

--Run outside of themis to try and capture stderr

```bash
srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

module load python_3/3.6.6 miniconda/3.6

conda activate /lustre/project/forage_assemblies/tfuller/Themis-ASM/.snakemake/conda/5ca3d7c7

--in Themis-ASM directory

--quickly test how many rows alignment in paf is >10,000 (min cutoff)

awk -F "\t" '{if($11>10000) print $11}' mapped/mapVvillosa_CowPea.paf | wc -l

>519 output

awk -F "\t" '{if($11>10000) print $11}' mapped/mapCommonBean_Pea.paf| wc -l

>69 output

awk -F "\t" '{if($11>10000) print $11}' mapped/mapPea_MedTr.paf | wc -l

> 629 output

-- Run Pea_MedTr to see standard known working output first then run other two problem runs

sbatch -N 1 -n 1 --mem=50000 -t 1-0 -p priority -q msn --wrap="Rscript /lustre/project/forage_assemblies/tfuller/Themis-ASM/scripts/pafDotPlotly.R -i mapped/mapPea_MedTr.paf -o Pea_MedTr -v -l -s"


sbatch -N 1 -n 1 --mem=50000 -t 1-0 -p priority -q msn --wrap="Rscript /lustre/project/forage_assemblies/tfuller/Themis-ASM/scripts/pafDotPlotly.R -i mapped/mapVvillosa_CowPea.paf -o Vvillosa_CowPea -v -l -s"

sbatch -N 1 -n 1 --mem=50000 -t 1-0 -p priority -q msn --wrap="Rscript /lustre/project/forage_assemblies/tfuller/Themis-ASM/scripts/pafDotPlotly.R -i mapped/mapCommonBean_Pea.paf -o CommonBean_Pea -v -l -s"
```
interestingly get different errors for each. I think CommonBean_Pea just doesn't have any input with alignment cutoffs so need to tweak cutoff values or maybe only print plot if things can be plotted to avoid error?? Test will lower threshold quick

```bash
sbatch -N 1 -n 1 --mem=50000 -t 1-0 -p priority -q msn --wrap="Rscript /lustre/project/forage_assemblies/tfuller/Themis-ASM/scripts/pafDotPlotly.R -i mapped/mapCommonBean_Pea.paf -o CommonBean_Pea -q 200000 -v -l -s"
```

#2/16/22
###Made adjustments to dotplot script. Realized that cumsum function makes all output NA after it hits one NA. Noted the first scaffold didn't have any alignments so it showed NA and as a result every value for plotting was converting to NA and resulting in an error.

###Made edits to go through and bypass the NAs while preserving the rest of the calculations. This worked when testing and I realized some other values were lost in other plots after one NA was encountered. Will rerun on all comparisons so new plots will show all alignments that pass minimum thresholds.

--Also made edits to increase minimap_align rule memory and lowered the minimum alignment threshold so hopefully all comparisons generate a dotplot and do not throw more errors.

--Also add the C wildcard into the stdout so errors can be more easily traced back for troubleshooting

```bash
srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

module load python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/themis_fuller/

cd /lustre/project/forage_assemblies/tfuller/Themis-ASM/

snakemake --snakefile themisSnakefile --unlock

git pull

--After removing all log files and previously generated plots rerun themis using the resume function.

sbatch -N 1 -n 2 --mem=50000 -t 4-0 -p priority -q msn --wrap="python themisASM.py  \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/vetch_phase.assembly.fasta  \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Lens_culinaris_2.0.fasta  \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Ler.1DRT.fasta \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Vunguiculata_469_v1.0.fa \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Pvulgaris_442_v2.0.fa \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Pisum_sativum_v1a.fa \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/MtrunA17r5.0-20161119-ANR.genome.fasta \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/ARS_DFRC_1.1.fasta \
	-a /lustre/project/forage_assemblies/tfuller/Themis-ASM/Vicia_sativa_reorg.fa \
	-n Vvillosa \
	-n Lentil \
	-n GrassPea \
	-n CowPea \
	-n CommonBean \
	-n Pea \
	-n MedTr \
	-n RedClover \
	-n CommonVetch \
	-b eudicots_odb10 \
	-f /lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L001_R1_001.fastq.gz,/lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L001_R2_001.fastq.gz \
	-f /lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L002_R1_001.fastq.gz,/lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L002_R2_001.fastq.gz \
	-f /lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L003_R1_001.fastq.gz,/lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L003_R2_001.fastq.gz \
	-f /lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L004_R1_001.fastq.gz,/lustre/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L004_R2_001.fastq.gz \
	-s lane1 \
	-s lane2 \
	-s lane3 \
	-s lane4 \
	-j 50 \
	-c 'sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -t 3-0 --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}' \
	-r"
```

so far so good but haven't seen new stdout directories for combinations appear in logs yet...
