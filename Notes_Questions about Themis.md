#12/7/21

Note that syntax highlighting or grammar is slightly changed for themisSnakefile when grammar is python. Same text entered into a file called Snakefile auto fixes this but not even sure it matters.


-Graph axis labels get cut off on certain graphs. Minor aesthetic error that should be fixed.

- Distribution of kmers?

-Where do the reads from different assemblies come into play exactly?



>Workflow from derek regarding 
Getting the chromosome names and sizes for the extraction

```
head -n 27 rambouillet_reads/fastas/oar4.fa.fai | tail -n 2

CM001607.2      44047080        2435654882      80      81
CM001608.2      135185801       2480252630      80      81

head -n 27 rambouillet_reads/fastas/ram2.fa.fai | tail -n 2

CM028729.1      45052359        2457770955      80      81
CM028730.1      143171725       2503386578      80      81
```
why does this work and are these chromosome names???

>Now need to filter alignments based on chromosomes. Uses samtools to do this with bedtools???

```
samtools view -h -F 0x4 -F 0x8 rambouillet_reads/mapped/ram2/merged.bam CM028729.1 CM028730.1 | samtools sort -@ 18 -n -o /lustre/project/rumen_longread_metagenome_assembly/sequence_data/themis_test/rambouillet_reads_template.bam -

samtools view -h -f 0x2 texel_reads/mapped/ram2/merged.bam scaffold_chr26 scaffold_chrX | samtools sort -@ 18 -n -o /lustre/project/rumen_longread_metagenome_assembly/sequence_data/themis_test/texel_reads_template.bam -

# Finally, let's grab the two chromosomes we want to compare
samtools faidx rambouillet_reads/fastas/oar4.fa CM001607.2 CM001608.2 > /lustre/project/rumen_longread_metagenome_assembly/sequence_data/themis_test/oar4Test.fasta
samtools faidx rambouillet_reads/fastas/ram2.fa CM028729.1 CM028730.1> /lustre/project/rumen_longread_metagenome_assembly/sequence_data/themis_test/ram2Test.fasta
```

>Now extract fastq data for themis run
>in folder /lustre/project/rumen_longread_metagenome_assembly/sequence_data/themis_test/

```
sbatch -N 1 -n 2 -p priority -q msn --mem=36000 --wrap="bedtools bamtofastq -i rambouillet_reads_template.bam -fq rambouillet_reads.R1.fastq -fq2 rambouillet_reads.R2.fastq"

sbatch -N 1 -n 2 -p priority -q msn --mem=36000 --wrap="bedtools bamtofastq -i texel_reads_template.bam -fq texel_reads.R1.fastq -fq2 texel_reads.R2.fastq"
```

#12/29/21

-Where did the basic template for jinja2 come from? I see where the title and content are added in but what are all the beginning parts?

-Is there better documentation or examples with markdown? Where do I go to learn the basic syntax for the templates or text

- What does the if else statement do when splicing the summary table? When does it append ''  and shouldnt that always be overwritten so extra sections arent lost?

-Likely need to find out where jinja2 base template came from. This may be why the output insists on so much dead space to the sides.

-Look into markdown lines as well to see how to make plots/tables side by side.

