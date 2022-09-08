#3/2/22
###Now that at least one of the runs of the Lentil_Grasspea minimap2 have completed I can copy over this file and rename it accordingly to serve as a placeholder to finish the themis-asm run

```bash

module load python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/themis_fuller/

>in forage_assemblies directory

cp mapLentil_GrassPea_asm5_255w.paf /project/forage_assemblies/tfuller/Themis-ASM/mapped/

>in mapped directory

mv mapLentil_GrassPea_asm5_255w.paf mapLentil_GrassPea.paf

>in Themis-ASM directory run touch command to make sure this new file integrates properly

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /project/forage_assemblies/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /project/forage_assemblies/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" --touch

job id 7257321


>metadata from snakemake trying to recognize mapLentil_GrassPea.paf as incomplete since it was generated outside snakemake workflow. update this by cleaning up metadata and run touch again


snakemake -s /project/forage_assemblies/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /project/forage_assemblies/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" --cleanup-metadata mapped/mapLentil_GrassPea.paf


snakemake -s /project/forage_assemblies/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /project/forage_assemblies/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" --touch

job id 7257328

>Test dryrun to see what still needs to be completed


sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /project/forage_assemblies/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /project/forage_assemblies/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -t 2-0 --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" --dryrun

job id 7257342
```

###Everything looks good to start the run again. Will use the actual themisASM.py script to see if it executes properly.

```bash
sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="python themisASM.py  \
	-a /project/forage_assemblies/tfuller/Themis-ASM/vetch_phase.assembly.fasta  \
	-a /project/forage_assemblies/tfuller/Themis-ASM/Lens_culinaris_2.0.fasta  \
	-a /project/forage_assemblies/tfuller/Themis-ASM/Ler.1DRT.fasta \
	-a /project/forage_assemblies/tfuller/Themis-ASM/Vunguiculata_469_v1.0.fa \
	-a /project/forage_assemblies/tfuller/Themis-ASM/Pvulgaris_442_v2.0.fa \
	-a /project/forage_assemblies/tfuller/Themis-ASM/Pisum_sativum_v1a.fa \
	-a /project/forage_assemblies/tfuller/Themis-ASM/MtrunA17r5.0-20161119-ANR.genome.fasta \
	-a /project/forage_assemblies/tfuller/Themis-ASM/ARS_DFRC_1.1.fasta \
	-a /project/forage_assemblies/tfuller/Themis-ASM/Vicia_sativa_reorg.fa \
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
	-f /project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L001_R1_001.fastq.gz,/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L001_R2_001.fastq.gz \
	-f /project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L002_R1_001.fastq.gz,/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L002_R2_001.fastq.gz \
	-f /project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L003_R1_001.fastq.gz,/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L003_R2_001.fastq.gz \
	-f /project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L004_R1_001.fastq.gz,/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L004_R2_001.fastq.gz \
	-s lane1 \
	-s lane2 \
	-s lane3 \
	-s lane4 \
	-j 50 \
	-c 'sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}' \
	-r"

job id 7257355
```


###Just saw over 3 day run finished with 24k and 24w and asm5 params. Cancelled all snakejobs, cleaned files from mapLentil_GrassPea.paf and will copy over this paf file that should be a little better alignment and restart snakejob accordingly

```bash
>in forage_assemblies directory


cp mapLentil_GrassPea_Lkmer.paf /project/forage_assemblies/tfuller/Themis-ASM/mapped/

>in mapped directory

rm mapLentil_GrassPea.paf
mv mapLentil_GrassPea_Lkmer.paf mapLentil_GrassPea.paf


>in Themis directory

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /project/forage_assemblies/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /project/forage_assemblies/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" --touch

job id 7257460

>Test dryrun to see what still needs to be completed


sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /project/forage_assemblies/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /project/forage_assemblies/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -t 2-0 --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" --dryrun

job id 7257461


>Now start themis again to hopefully finish

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="python themisASM.py  \
	-a /project/forage_assemblies/tfuller/Themis-ASM/vetch_phase.assembly.fasta  \
	-a /project/forage_assemblies/tfuller/Themis-ASM/Lens_culinaris_2.0.fasta  \
	-a /project/forage_assemblies/tfuller/Themis-ASM/Ler.1DRT.fasta \
	-a /project/forage_assemblies/tfuller/Themis-ASM/Vunguiculata_469_v1.0.fa \
	-a /project/forage_assemblies/tfuller/Themis-ASM/Pvulgaris_442_v2.0.fa \
	-a /project/forage_assemblies/tfuller/Themis-ASM/Pisum_sativum_v1a.fa \
	-a /project/forage_assemblies/tfuller/Themis-ASM/MtrunA17r5.0-20161119-ANR.genome.fasta \
	-a /project/forage_assemblies/tfuller/Themis-ASM/ARS_DFRC_1.1.fasta \
	-a /project/forage_assemblies/tfuller/Themis-ASM/Vicia_sativa_reorg.fa \
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
	-f /project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L001_R1_001.fastq.gz,/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L001_R2_001.fastq.gz \
	-f /project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L002_R1_001.fastq.gz,/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L002_R2_001.fastq.gz \
	-f /project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L003_R1_001.fastq.gz,/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L003_R2_001.fastq.gz \
	-f /project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L004_R1_001.fastq.gz,/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L004_R2_001.fastq.gz \
	-s lane1 \
	-s lane2 \
	-s lane3 \
	-s lane4 \
	-j 50 \
	-c 'sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}' \
	-r"

job id 7257463 - COMPLETED!!!
```

#3/3/22
###lumpy for Pea is still running but seems "stuck" at last contig. Start an independent job submission to see if that completes

```bash
module load python_3/3.6.6 miniconda/3.6

conda activate /project/forage_assemblies/tfuller/Themis-ASM/.snakemake/conda/f4f11be0

--Note large increase to memory just in case this is somehow still whats causing the lag

sbatch -N 1 -n 8 --mem=240000 -p priority -q msn --wrap="lumpyexpress -B mapped/Pea/merged.bam -o Pea_merged_lumpy.vcf -v 2> Pea_lumpy.log"

job id 7262906
--Cancelled this because the run in the snakemake job finished.
```

###Using --recursive works to clone submodules into repository with the main git clone command. Comment out git_submdule rule and push to main git repository.