##Should have all necessary packages downloaded and now starting snakemake tutorial

>Make new directory and move to it

```
mkdir snakemake-tutorial
path: /home/tdfuller/snakemake-tutorial

cd snakemake-tutorial
```

>Download example data putting directory "data" and environment.yaml file in working directory

```
wget https://github.com/snakemake/snakemake-tutorial-data/archive/v5.24.1.tar.gz

tar --wildcards -xf v5.24.1.tar.gz --strip 1 "*/data" "*/environment.yaml"
```

>Activate base conda environment and use yaml file to download all necessary software into isolated conda environment "snakemake-tutorial" and activate it

```
conda activate base

mamba env create --name snakemake-tutorial --file environment.yaml

conda activate snakemake-tutorial
```
>Create Snakefile using editor of choice. Needed to go to C:\Users\tyson.fuller\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79rhkp1fndgsc\LocalState\rootfs\home\tdfuller to get to my ubuntu account files. In there I found the snakemake-tutorial folder to save a script to.

>Currently using atom because it recognizes snakemake syntax but will also try VS code 

```
>In Snakefile add the following rule

rule bwa_map:
    input:
        "data/genome.fa",
        "data/samples/{sample}.fastq"
    output:
        "mapped_reads/{sample}.bam"
    shell:
        "bwa mem {input} | samtools view -Sb - > {output}


>execute a dryrun with -n but output shell command with -p

snakemake -np mapped_reads/A.bam

>Run Snakefile to make A.bam file

snakemake --cores 1 mapped_reads/A.bam
```

>Create new rules in Snakefile under bwa_map to sort the BAM files and index them with samtools . wildcards can be used to tell it to iterate through all of that wildcard

```
rule samtools_sort:
    input:
        "mapped_reads/{sample}.bam"
    output:
        "sorted_reads/{sample}.bam"
    shell:
        "samtools sort -T sorted_reads/{wildcards.sample} "
        "-O bam {input} > {output}"

rule samtools_index:
    input:
        "sorted_reads/{sample}.bam"
    output:
        "sorted_reads/{sample}.bam.bai"
    shell:
        "samtools index {input}"
```

>View DAG of current Snakefile.

```
snakemake --dag sorted_reads/{A,B}.bam.bai | dot -Tsvg > dag.svg
```

> Add new rule for variant calling. Use the 'expand" function to make create a list of variables based on the format given.

```
>at top of file define SAMPLES

SAMPLES = ["A", "B"]

>Add rule with expand. This for example makes bam=["sorted_reads/A.bam", "sorted_reads/B.bam"]

rule bcftools_call:
    input:
        fa="data/genome.fa",
        bam=expand("sorted_reads/{sample}.bam", sample=SAMPLES),
        bai=expand("sorted_reads/{sample}.bam.bai", sample=SAMPLES)
    output:
        "calls/all.vcf"
    shell:
        "samtools mpileup -g -f {input.fa} {input.bam} | "
        "bcftools call -mv - > {output}"
```

>Add rule that call python script and write the python script in a new directory "scripts"

```
> Rule calling python script

rule plot_quals:
    input:
        "calls/all.vcf"
    output:
        "plots/quals.svg"
    script:
        "scripts/plot-quals.py"

> Python script. Note that snakemake.input and .output are "lists" so you need to tell file which value in list to call. This can be a name  if they are named in Snakefile

import matplotlib
matplotlib.use("Agg")
import matplotlib.pyplot as plt
from pysam import VariantFile

quals = [record.qual for record in VariantFile(snakemake.input[0])]
plt.hist(quals)

plt.savefig(snakemake.output[0])
```

>At top of Snakefile rules add a "target" rule "all" that has no wildcards and can be used in command line to generate any relevant output. In this case just "plots/quals.svg"

```
rule all:
    input:
        "plots/quals.svg"
