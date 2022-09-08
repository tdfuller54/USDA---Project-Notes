#8/8/22
####Download v sativa short reads from online and align them to sativa and villosa then get percentage of aligned reads from idx stats?? Confirm numbers from sativa paper and test to see how they align with villosa

```bash

-in tfuller

mkdir sativa_reads/

-in sativa_reads

wget ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR160/015/SRR16004115/SRR16004115_1.fastq.gz

wget ftp://ftp.sra.ebi.ac.uk/vol1/fastq/SRR160/058/SRR16004258/SRR16004258_2.fastq.gz

ln -s /project/forage_assemblies/assemblies/legume_comparison/Vicia_sativa_reorg.fa /project/forage_assemblies/tfuller/sativa/Vsativa.fa

ln -s /project/forage_assemblies/assemblies/legume_comparison/vetch_phase.assembly.fasta /project/forage_assemblies/tfuller/sativa_reads/Vvillosa.fa


mkdir fastas

mv Vvillosa.fa fastas/

mv Vsativa.fa fastas/

mkdir logs

conda activate /project/rumen_longread_metagenome_assembly/environments/themis_fuller2/


sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="bwa index fastas/Vsativa.fa 2> logs/sat_index.log
samtools faidx fastas/Vsativa.fa 2> logs/sat_index.log"

job id 7892992

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="bwa index fastas/Vvillosa.fa 2> logs/vill_index.log
samtools faidx fastas/Vvillosa.fa 2> logs/vill_index.log"

job id 7892993



mkdir sat_mapped

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="bwa mem -t 8 -M fastas/Vsativa.fa /project/forage_assemblies/tfuller/sativa_reads/SRR16004115_1.fastq.gz /project/forage_assemblies/tfuller/sativa_reads/SRR16004115_2.fastq.gz | samtools sort -o sat_mapped/sat_aligned.bam  - >> logs/sat_bwa.log 2>&1
samtools index -c sat_mapped/sat_aligned.bam"

job id 7893497

mkdir vill_mapped

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="bwa mem -t 8 -M fastas/Vvillosa.fa /project/forage_assemblies/tfuller/sativa_reads/SRR16004115_1.fastq.gz /project/forage_assemblies/tfuller/sativa_reads/SRR16004115_2.fastq.gz | samtools sort -o vill_mapped/vill_aligned.bam  - >> logs/vill_bwa.log 2>&1
samtools index -c vill_mapped/vill_aligned.bam"

job id 7893498
```

as of 8/12/22 at 9:30 these are still running.. This is really taking a long time...

Still running at 4:54pm 8/12/22...

8/15 and 8/16 was out sick.

#8/17/22

Sent location of Vvill1.0 genome to John Raasch so he could analyze it.

Emailed Lisa a bit more about getting Themis to run. She expressed interest in trying again or just having me run the more complete analysis.

Without conda working to install environments properly it is very hard to troubleshoot. Conda not working is a new problem though.

Either Lisa cannot work in environments that she did not create herself or there is an issue with the conda activate vs source activate and conda init commands.

#8/18/22

What is source activate vs conda doing and what would conda init change??

####Should now be able to use samtools idxstats to get aligned and unaligned read percentages from aligning Vsativa reads to both Vsativa and Vvillosa genomes.





#8/19/22


bcftools-1.10-h5d15f04_0 requires htslib >=1.10,<1.11.0a0


I'm thinking I can try to tell conda to install htslib 1.10.2-hd3b49d5_1 in the .yaml file of freebayes. Really not sure why in themis_fuller2 environment it can find and create the new environment but it can't in another.

The active environment "testenv" and "themis_fuller2" both actually have a new htslib version installed but both do not have bcftools.


#8/22/22
Organize notes for future use upon departure.

Went through and saw notes were documented in .md files. All of these are currently in documents folder. Cleaned them to my ability. Should have clear documentation of what I was doing each process for and why. Any use of codes and scripts are marked accordingly and should be exactly what was executed on ceres.

Ultimately will put these into github repo.

#8/23/22-8/25/22

Try to troubleshoot Themis further. I cannot find any noticeable differences between packages of each starting environment. It just does not seem to make sense that themis_fuller2 will properly create the environment each time themis is ran in a new directory with new inputs but testenv will not create the environment. Is adding the htslib 1.10.2 in the yaml enough to work? There really should be some explanation


#8/26/22

Realized I still need to calculate the mapped and unmapped percentages of v sativa reads to villosa and sativa assemblies

```bash
module load samtools

in sativa_reads folder

samtools idxstats sat_mapped/sat_aligned.bam > sat_idxs_out.tsv

awk '{map+=$3;} {unmap+=$4;} END{print unmap/(map+unmap)*100;}' sat_idxs_out.tsv


awk '{map+=$3;} {unmap+=$4;} END{print map/(map+unmap)*100;}' sat_idxs_out.tsv

```

Note that apparently the alignment to vvillosa timed out. This is  interesting. Will see how much I can max out the cores and threads available.

```bash
conda activate /project/rumen_longread_metagenome_assembly/environments/themis_fuller2/

sbatch -N 1 -n 48 --mem=300000 -p priority -q msn --wrap="bwa mem -t 48 -M fastas/Vvillosa.fa /project/forage_assemblies/tfuller/sativa_reads/SRR16004115_1.fastq.gz /project/forage_assemblies/tfuller/sativa_reads/SRR16004115_2.fastq.gz | samtools sort -o vill_mapped/vill_aligned.bam  - >> logs/vill_bwa.log 2>&1
samtools index -c vill_mapped/vill_aligned.bam"

job id 7917431

DONE!!

```

WHEN DONE WILL NEED TO DO!!!

```bash

module load samtools

in sativa_reads folder

samtools idxstats vill_mapped/vill_aligned.bam > vill_idxs_out.tsv

awk '{map+=$3;} {unmap+=$4;} END{print unmap/(map+unmap)*100;}' vill_idxs_out.tsv

36.02%


awk '{map+=$3;} {unmap+=$4;} END{print map/(map+unmap)*100;}' vill_idxs_out.tsv

63.98%
```

