#6/2/22
###Realign reads to v sativa and use idxstats and frc-align to confirm themis findings.

```bash
srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

module load python_3/3.6.6

-make new symbolic link in new directory for assembly and reads

-in tfuller

mkdir sativa

ln -s /project/forage_assemblies/assemblies/legume_comparison/Vicia_sativa_reorg.fa /project/forage_assemblies/tfuller/sativa/CommonVetch.fa


ln -s /project/forage_assemblies/sequence/vetch_illumina/HV30Vetch_S1_L001_R1_001.fastq.gz /project/forage_assemblies/tfuller/sativa/HV30Vetch_S1_L001_R1_001.fastq.gz


ln -s /project/forage_assemblies/sequence/vetch_illumina/HV30Vetch_S1_L001_R2_001.fastq.gz /project/forage_assemblies/tfuller/sativa/HV30Vetch_S1_L001_R2_001.fastq.gz


ln -s /project/forage_assemblies/sequence/vetch_illumina/HV30Vetch_S1_L002_R1_001.fastq.gz /project/forage_assemblies/tfuller/sativa/HV30Vetch_S1_L002_R1_001.fastq.gz


ln -s /project/forage_assemblies/sequence/vetch_illumina/HV30Vetch_S1_L002_R2_001.fastq.gz /project/forage_assemblies/tfuller/sativa/HV30Vetch_S1_L002_R2_001.fastq.gz


ln -s /project/forage_assemblies/sequence/vetch_illumina/HV30Vetch_S1_L003_R1_001.fastq.gz /project/forage_assemblies/tfuller/sativa/HV30Vetch_S1_L003_R1_001.fastq.gz


ln -s /project/forage_assemblies/sequence/vetch_illumina/HV30Vetch_S1_L003_R2_001.fastq.gz /project/forage_assemblies/tfuller/sativa/HV30Vetch_S1_L003_R2_001.fastq.gz


ln -s /project/forage_assemblies/sequence/vetch_illumina/HV30Vetch_S1_L004_R1_001.fastq.gz /project/forage_assemblies/tfuller/sativa/HV30Vetch_S1_L004_R1_001.fastq.gz


ln -s /project/forage_assemblies/sequence/vetch_illumina/HV30Vetch_S1_L004_R2_001.fastq.gz /project/forage_assemblies/tfuller/sativa/HV30Vetch_S1_L004_R2_001.fastq.gz

- note slurm-7235164.out for a reference of what code to use for aligning and indexing the reads

-in sativa

mkdir fastas

mv CommonVetch.fa fastas/

mkdir logs

conda activate /project/rumen_longread_metagenome_assembly/environments/themis_fuller2/


sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="bwa index fastas/CommonVetch.fa 2> logs/index.log
samtools faidx fastas/CommonVetch.fa 2> logs/index.log"

job id 7761893

mkdir mapped

- Need to wait for index to finish then run these


sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="bwa mem -R '@RG\\tID:lane1\\tSM:lane1\\tPL:ILLUMINA' -t 8 -M fastas/CommonVetch.fa /project/forage_assemblies/tfuller/sativa/HV30Vetch_S1_L001_R1_001.fastq.gz /project/forage_assemblies/tfuller/sativa/HV30Vetch_S1_L001_R2_001.fastq.gz | samtools sort -o mapped/lane1.bam  - >> logs/lane1_bwa.log 2>&1
samtools index -c mapped/lane1.bam"

job id 7762081

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="bwa mem -R '@RG\\tID:lane2\\tSM:lane2\\tPL:ILLUMINA' -t 8 -M fastas/CommonVetch.fa /project/forage_assemblies/tfuller/sativa/HV30Vetch_S1_L002_R1_001.fastq.gz /project/forage_assemblies/tfuller/sativa/HV30Vetch_S1_L002_R2_001.fastq.gz | samtools sort -o mapped/lane2.bam  - >> logs/lane2_bwa.log 2>&1
samtools index -c mapped/lane2.bam"

job id 7762082

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="bwa mem -R '@RG\\tID:lane3\\tSM:lane3\\tPL:ILLUMINA' -t 8 -M fastas/CommonVetch.fa /project/forage_assemblies/tfuller/sativa/HV30Vetch_S1_L003_R1_001.fastq.gz /project/forage_assemblies/tfuller/sativa/HV30Vetch_S1_L003_R2_001.fastq.gz | samtools sort -o mapped/lane3.bam  - >> logs/lane3_bwa.log 2>&1
samtools index -c mapped/lane3.bam"

job id 7762083

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="bwa mem -R '@RG\\tID:lane4\\tSM:lane4\\tPL:ILLUMINA' -t 8 -M fastas/CommonVetch.fa /project/forage_assemblies/tfuller/sativa/HV30Vetch_S1_L004_R1_001.fastq.gz /project/forage_assemblies/tfuller/sativa/HV30Vetch_S1_L004_R2_001.fastq.gz | samtools sort -o mapped/lane4.bam  - >> logs/lane4_bwa.log 2>&1
samtools index -c mapped/lane4.bam"

job id 7762086
```

as of 2:08pm on Friday these are still running. Will need to merge these once completed before running idxstats.

--Still not done as of 5:49pm. Looked back and lane1 reads took 29 hours to complete the alignment so this is not that surprising.

--Note that idxstats is utilized in the summarytable.py script directly for unmapped percentage.

-- Do I want genome coverage and if so why won't it work with pysam? keeps saying it is installed but than no module named pysam found.. 

#6/6/22
depth_estimate has it's own environment "depth" so may running the genome coverage script in that environment will work.

Try running this in "depth" environment
```bash
sbatch -N 1 -n 2 --mem=100000 -t 1-0 -p priority -q msn --wrap="python Coverage_Percentage.py -b mapped/CommonVetch/merged.bam -t 1 -o cv_coverage_1.tsv"

job id 7768698 this actually did work as well
```
-quickly test if any sbatch jobs run

```bash
sbatch -N 1 -n 2 --mem=100000 -t 1-0 -p priority -q msn --wrap="echo 'job submission test' > test.txt"

job id 7768716 - THIS WORKED SO I ASSUME THE COVERAGE PERCENTAGE IS RUNNING? MAYBE JUST SLOW??
```

-note slurm-7235164.out for a reference of what code to use for aligning and indexing the reads

last 3 look right. When lane 1 finishes it should be 128 files and 1 in-memory block total based on Themis logs that other 3 matched

-All finished and removed old tmp files. Now need to merge the bams

```bash



sbatch -N 1 -n 2 --mem=100000 -t 5-0 -p priority -q msn --wrap="samtools merge -@ 8 mapped/merged.bam mapped/lane1.bam mapped/lane2.bam mapped/lane3.bam mapped/lane4.bam
samtools index -c mapped/merged.bam"

job id 7770015
```

#6/9/22
###Run samtools idxstats on merged.bam file

```bash
module load samtools

in sativa folder

samtools idxstats mapped/merged.bam > idxs_out.tsv

awk '{map+=$3;} {unmap+=$4;} END{print unmap/(map+unmap)*100;}' idxs_out.tsv

this prints out unmapped % which is 52.96 which matches!!
```

#6/10/22
###Run FRC_align and lumpy-SV to confirm those numbers as well

```bash

conda activate /project/rumen_longread_metagenome_assembly/environments/themis_fuller2/

module load frc_align

in sativa folder

sbatch -N 1 -n 2 --mem=100000 -t 5-0 -p priority -q msn --wrap="FRC --pe-sam mapped/merged.bam --output calls/merged_frc"

job id 7772972

--Now run lumpy again

conda deactivate

--activate created "lumpy" environment

/project/forage_assemblies/tfuller/Themis-ASM/.snakemake/conda/f4f11be0a312467484fbb1f300479f6d/

in sativa folder

sbatch -N 1 -n 2 --mem=100000 -t 5-0 -p priority -q msn --wrap="lumpyexpress -B mapped/merged.bam -o calls/merged_lumpy.vcf -v 2> logs/lumpy.log"

job id 7772973
```

NOTE- NEED TO RUN DELLY TO DOUBLE CONFIRM LUMPY STRUCTURAL VARIANT CALLS