#2/17/22
###Got error with indexing because some chromosomes were too large for samtools bai format. Made change to csi format. Also needed to create subdirectories for logs. Hopefully this solves error of not getting any output from rules I made log changes to. Finally. Removing entire directory and restarting because so many rules ran and were incomplete or had to be cancelled that I want to be sure any incomplete files were completely removed.

```bash

--- assembly files

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


-- sample reads

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

--- Attempt to run themis with new changes. Should automatically load everything into GetMaskBedFasta.

sbatch -N 1 -n 2 --mem=50000 -t 3-12 -p priority -q msn --wrap="python themisASM.py  \
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
	-c 'sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -t 1-12 --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}'"
```

#2/18/21
###lumpy rule returned error. Looking at output I think this is quite possibly just an out of memory error. looking at logs from test run it seems lumpy just outputs some error messages in the stdout before completing. frc_align is also out of memory.

--Note Need to test this with more memory next week (themis uses 200000 for this rule now). Will likely die due to time before it completes but why is this one taking so long???

sbatch -N 1 -n 2 --mem=50000 -t 1-0 -p priority -q msn --wrap="minimap2 -x asm10 -t 8 fastas/Lentil.fa fastas/GrassPea.fa > mapLentil_GrassPea.paf 2> test.log"


--info from lentil_grasspea minimap_align job

Cluster: ceres
User/Group: tyson.fuller/tyson.fuller
State: TIMEOUT (exit code 0)
Nodes: 1
Cores per node: 8
CPU Utilized: 2-06:24:41
CPU Efficiency: 18.89% of 12-00:00:24 core-walltime
Job Wall-clock time: 1-12:00:03
Memory Utilized: 178.05 GB
Memory Efficiency: 91.16% of 195.31 GB

2.0GB Vvillosa
3.6GB Lentil
2.8GB GrassPea
504MB CowPea
519MB CommonBean
3.8GB Pea
417MB MedTr
399MB RedClover
1.6GB CommonVetch

lentil and pea minimap rule used 28.77GB memory. grasspea and pea minimap rule used 26.39GB memory.. Why the heck does Lentil_GrassPea use so much memory and take so long!!!???

maybe adjust -I or maybe -K (capital) for increasing speed and give lots of memory?

-I defaults to 4G. Change this to 12G. and make memory much higher. Change memory to 400GB and remember to test true memory after completion

lumpy changed memory from 65G to 120G
frc_align changed memory from 15G to 50G