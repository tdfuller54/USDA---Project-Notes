#2/25/22
###Copy seemed to work. Now need to clean up symbolic links

--remove bad symlinks and create new in Themis-ASM directory

1-hairy vetch (Vvillosa)
ln -s /project/forage_assemblies/assemblies/legume_comparison/vetch_phase.assembly.fasta vetch_phase.assembly.fasta

2-Lens culinaris (Lentil)
ln -s /project/forage_assemblies/assemblies/legume_comparison/Lens_culinaris_2.0.fasta Lens_culinaris_2.0.fasta

3-Lathyrus sativus (GrassPea)
ln -s /project/forage_assemblies/assemblies/legume_comparison/Ler.1DRT.fasta Ler.1DRT.fasta

4-V unguiculate (CowPea)
ln -s /project/forage_assemblies/assemblies/legume_comparison/Vunguiculata_469_v1.0.fa Vunguiculata_469_v1.0.fa

5-P vulgaris (CommonBean)
ln -s /project/forage_assemblies/assemblies/legume_comparison/Pvulgaris_442_v2.0.fa Pvulgaris_442_v2.0.fa

6-Pisum sativum (Pea)
ln -s /project/forage_assemblies/assemblies/legume_comparison/Pisum_sativum_v1a.fa Pisum_sativum_v1a.fa

7-Medicago truncatulata (MedTr)
ln -s /project/forage_assemblies/assemblies/legume_comparison/MtrunA17r5.0-20161119-ANR.genome.fasta MtrunA17r5.0-20161119-ANR.genome.fasta

8-T. pratense (RedClover)
ln -s /project/forage_assemblies/assemblies/legume_comparison/ARS_DFRC_1.1.fasta ARS_DFRC_1.1.fasta

9-V. sativa (CommonVetch)
ln -s /project/forage_assemblies/assemblies/legume_comparison/Vicia_sativa_reorg.fa Vicia_sativa_reorg.fa


-- sample reads

ln -s /project/forage_assemblies/sequence/vetch_illumina/HV30Vetch_S1_L001_R1_001.fastq.gz HV30Vetch_S1_L001_R1_001.fastq.gz

ln -s /project/forage_assemblies/sequence/vetch_illumina/HV30Vetch_S1_L001_R2_001.fastq.gz HV30Vetch_S1_L001_R2_001.fastq.gz

lane2
ln -s /project/forage_assemblies/sequence/vetch_illumina/HV30Vetch_S1_L002_R1_001.fastq.gz HV30Vetch_S1_L002_R1_001.fastq.gz

ln -s /project/forage_assemblies/sequence/vetch_illumina/HV30Vetch_S1_L002_R2_001.fastq.gz HV30Vetch_S1_L002_R2_001.fastq.gz

lane3
ln -s /project/forage_assemblies/sequence/vetch_illumina/HV30Vetch_S1_L003_R1_001.fastq.gz HV30Vetch_S1_L003_R1_001.fastq.gz

ln -s /project/forage_assemblies/sequence/vetch_illumina/HV30Vetch_S1_L003_R2_001.fastq.gz HV30Vetch_S1_L003_R2_001.fastq.gz

lane4
ln -s /project/forage_assemblies/sequence/vetch_illumina/HV30Vetch_S1_L004_R1_001.fastq.gz HV30Vetch_S1_L004_R1_001.fastq.gz

ln -s /project/forage_assemblies/sequence/vetch_illumina/HV30Vetch_S1_L004_R2_001.fastq.gz HV30Vetch_S1_L004_R2_001.fastq.gz


--in fastas directory remove and create new symbolic links

ln -s /project/forage_assemblies/tfuller/Themis-ASM/Pvulgaris_442_v2.0.fa CommonBean.fa

ln -s /project/forage_assemblies/tfuller/Themis-ASM/vetch_phase.assembly.fasta Vvillosa.fa

ln -s /project/forage_assemblies/tfuller/Themis-ASM/Pisum_sativum_v1a.fa Pea.fa

ln -s /project/forage_assemblies/tfuller/Themis-ASM/ARS_DFRC_1.1.fasta RedClover.fa

ln -s /project/forage_assemblies/tfuller/Themis-ASM/MtrunA17r5.0-20161119-ANR.genome.fasta MedTr.fa

ln -s /project/forage_assemblies/tfuller/Themis-ASM/Lens_culinaris_2.0.fasta Lentil.fa

ln -s /project/forage_assemblies/tfuller/Themis-ASM/Ler.1DRT.fasta GrassPea.fa

ln -s /project/forage_assemblies/tfuller/Themis-ASM/Vunguiculata_469_v1.0.fa CowPea.fa

ln -s /project/forage_assemblies/tfuller/Themis-ASM/Vicia_sativa_reorg.fa CommonVetch.fa


```bash

--I had to remove everything in GetMaskBedFasta and reload the submodule then added the gitsubmodule.done file. This was needed so I could pull in tweaks to repository

--Everything should be good to go but waiting to see if minimap run works. In the meantime will attempt to use --touch to tell snakemake to update timestamps to match what it expects the order of file generation to be

sbatch -N 1 -n 2 --mem=25000 -t 2-0 -p priority -q msn snakemake -s /project/forage_assemblies/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /project/forage_assemblies/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -t 2-0 --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" --touch

--NOTE THIS RETURNED ERRORS TO DO WITH .conda reference to home folder. Need to correct this and run again



--Run minimap of lentil and grasspea independently

--in forage_assemblies

sbatch -N 1 -n 2 --mem=250000 -p priority -q msn --wrap="minimap2 -x asm5 -t 8 assemblies/legume_comparison/Lens_culinaris_2.0.fasta assemblies/legume_comparison/Ler.1DRT.fasta > mapLentil_GrassPea.paf 2> minimap.log"

job id 7242697 (In forage_assemblies directory)



>error with symbolic link in home folder try to copy .conda folder from OLD directory

>in /project/forage_assemblies/tfuller

mkdir .conda

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="cp -avr /OLD/project/rumen_longread_metagenome_assembly/tfuller/.conda .conda 2> copy.log && echo \"conda copied successful\" || echo \"something didn't complete right\""

job id 7242846

>in home directory

rm -r .conda/

ln -s /project/forage_assemblies/tfuller/.conda .conda/



>In forage_assemblies/tfuller/Themis-ASM

module load python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/themis_fuller/

>Now try to run --touch with snakemake again now that ~/.conda is linked to the right place


sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /project/forage_assemblies/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /project/forage_assemblies/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -t 2-0 --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" --touch


job id 7242874

new job id after editing write protection 7242893

random errors with missing output maybe fine since those jobs didn't run.

bigger issue may be with write-protected output files from busco that dont like to be rewritten with --touch

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /project/forage_assemblies/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /project/forage_assemblies/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -t 2-0 --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" --dryrun

job id 7242894

seems to be in working order now. Just need to get the minimap2 alignment to complete
```

#2/26/22
###Try a couple other ways to get the alignment to work

```bash
>in forage_assemblies
>first is brute force

sbatch -N 1 -n 8 --mem=350000 -p priority -q msn --wrap="minimap2 -x asm5 -t 16 -I16G assemblies/legume_comparison/Lens_culinaris_2.0.fasta assemblies/legume_comparison/Ler.1DRT.fasta > mapLentil_GrassPea_Lmem.paf 2> minimap_Lmem.log"

job id 7242965 - FAILED!

>next restart asm5 run

sbatch -N 1 -n 2 --mem=250000 -p priority -q msn --wrap="minimap2 -x asm5 -t 8 assemblies/legume_comparison/Lens_culinaris_2.0.fasta assemblies/legume_comparison/Ler.1DRT.fasta > mapLentil_GrassPea.paf 2> minimap.log"

job id 7242967 - FAILED!

> Last increase kmer and w from 19 to 24


sbatch -N 1 -n 2 --mem=250000 -p priority -q msn --wrap="minimap2 -x asm5 -t 8 -k24 -w24 assemblies/legume_comparison/Lens_culinaris_2.0.fasta assemblies/legume_comparison/Ler.1DRT.fasta > mapLentil_GrassPea_Lkmer.paf 2> minimap_Lkmer.log"

job id 7242968 - DONE!
```

#3/1/22
##Try a couple other run parameters to speed up process further

```bash
module load python_3/3.6.6 miniconda/3.6

conda activate /project/forage_assemblies/tfuller/Themis-ASM/.snakemake/conda/71004de4/


>asm10 with 30kmer and 30w


sbatch -N 1 -n 8 --mem=350000 -p priority -q msn --wrap="minimap2 -x asm10 -t 8 -I8G -k30 -w30 assemblies/legume_comparison/Lens_culinaris_2.0.fasta assemblies/legume_comparison/Ler.1DRT.fasta > mapLentil_GrassPea_asm10_30kmer.paf 2> minimap_asm10_30kmer.log"

job id 7255883 to show error



>asm5 with 28kmer and 30w

sbatch -N 1 -n 8 --mem=350000 -p priority -q msn --wrap="minimap2 -x asm5 -t 8 -I8G -k28 -w30 assemblies/legume_comparison/Lens_culinaris_2.0.fasta assemblies/legume_comparison/Ler.1DRT.fasta > mapLentil_GrassPea_asm5_28kmer.paf 2> minimap_asm5_28kmer.log"

job id 7255880


>asm10 with 28kmer and 30w


sbatch -N 1 -n 8 --mem=350000 -p priority -q msn --wrap="minimap2 -x asm10 -t 8 -I8G -k28 -w30 assemblies/legume_comparison/Lens_culinaris_2.0.fasta assemblies/legume_comparison/Ler.1DRT.fasta > mapLentil_GrassPea_asm10_28kmer.paf 2> minimap_asm10_28kmer.log"

job id 7255885


>asm20 with 28kmer and 30w

sbatch -N 1 -n 8 --mem=350000 -p priority -q msn --wrap="minimap2 -x asm20 -t 8 -I8G -k28 -w30 assemblies/legume_comparison/Lens_culinaris_2.0.fasta assemblies/legume_comparison/Ler.1DRT.fasta > mapLentil_GrassPea_asm20_28kmer.paf 2> minimap_asm20_28kmer.log"

job id 7255886



>asm5 with 28kmer and 100w

sbatch -N 1 -n 8 --mem=350000 -p priority -q msn --wrap="minimap2 -x asm5 -t 8 -I8G -k28 -w100 assemblies/legume_comparison/Lens_culinaris_2.0.fasta assemblies/legume_comparison/Ler.1DRT.fasta > mapLentil_GrassPea_asm5_100w.paf 2> minimap_asm5_100w.log"

job id 7255888 - DONE!

>asm10 with 28kmer and 100w


sbatch -N 1 -n 8 --mem=350000 -p priority -q msn --wrap="minimap2 -x asm10 -t 8 -I8G -k28 -w100 assemblies/legume_comparison/Lens_culinaris_2.0.fasta assemblies/legume_comparison/Ler.1DRT.fasta > mapLentil_GrassPea_asm10_100w.paf 2> minimap_asm10_100w.log"

job id 7255890 - DONE!


>asm5 with 28kmer and 255w

sbatch -N 1 -n 8 --mem=350000 -p priority -q msn --wrap="minimap2 -x asm5 -t 8 -I8G -k28 -w255 assemblies/legume_comparison/Lens_culinaris_2.0.fasta assemblies/legume_comparison/Ler.1DRT.fasta > mapLentil_GrassPea_asm5_255w.paf 2> minimap_asm5_255w.log"

job id 7255891 -- DONE!!
```

###Interestingly there was not a large difference in speed using asm5 compared to asm10 or asm20. Actually found out that asm20 completed the fastest. This should be noted for future problem alignments. Cannot be sure that will always be the case though.