#11/19/21 - Jupiter plots requires circos and circos tools, minimap2, samtools, and GNU make. Will need to insall circos with linuxbrew or some other way? Input files only need a set of scaffolds in fasta format and a reference genome in fasta format.


Scaffolds are large spans of genomic sequence created by combining contigs and can have gaps. Need to see if themis already has scaffolds generated or what to do to create them. Could scaffolds be the completed assemblies?
Definitely seems as though scaffold files would just be the assembly files

Also need the reference genome in fasta format. Does this exist from themis or BUSCO already. Documentation does not make it sound like BUSCO uses reference genomes but rather gene marker lists. Could also consider comparing completed assemblies from each species if those  meet requirements for scaffold and reference genome files both.

It seems all reference genomes can be anywhere from the scaffold level to the chromosome level. Using different assemblies as both the input scaffold and input reference should work fine. Also, can dig into what's available for completed reference genomes for each species to determine if these are already downloaded for other parts of themis or if I can download them

#11/23/21

Not really clear how to fully install and execute

```
>Load modules and activate environment or create one if necessary

module load mpc/1.0.3 gmp/6.1.0 mpfr/3.1.3 gcc/8.1.0 openblas/0.2.15 python_3/3.6.6 miniconda/3.6

cd /lustre/project/rumen_longread_metagenome_assembly/tfuller

wget https://github.com/JustinChu/JupiterPlot/archive/refs/tags/1.0.tar.gz

tar xvzf 1.0.tar.gz

in JupiterPlot-1.0 the jupiter file is an executable??

>create environement with mamba to install at least circos first

conda create -p /KEEP/rumen_longread_metagenome_assembly/jupiterplot -c bioconda -y  mamba

mamba install -c bioconda circos
```

#11/26/21

need to figure out how to execute jupiterplot in sbatch  wrap command

Copy two files over or create symbolic link for testing of jupterplot outside of pipeline.

Do I need a reference genome or just compare between two assemblies or both?

#11/30/21

Use AMouflon and tibetan since they have the highest completeness based on SCGs and are both in assemblies folder

```
tibetan
/lustre/project/rumen_longread_metagenome_assembly/wdxrom/assemblies/GCA_017524585.1_CAU_O.aries_1.0_genomic.fna

AMouflon
/lustre/project/rumen_longread_metagenome_assembly/wdxrom/assemblies/GCA_014523465.1_CAU_Oori_1.0_genomic.fna

ln -s /lustre/project/rumen_longread_metagenome_assembly/wdxrom/assemblies/GCA_017524585.1_CAU_O.aries_1.0_genomic.fna tibetan.fna

ln -s /lustre/project/rumen_longread_metagenome_assembly/wdxrom/assemblies/GCA_014523465.1_CAU_Oori_1.0_genomic.fna AMouflon.fna
```

Really can't do anything else because of slurm error

#12/1/21

Slurm error still present as of about 8am

```
> Load modules and activate environment

module load mpc/1.0.3 gmp/6.1.0 mpfr/3.1.3 gcc/8.1.0 openblas/0.2.15 python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/jupiterplot

> Go to folder where jupiterplot was extracted (JupiterPlot-1.0) to attempt to run. This also has symbolic links to assemblies in it. Try to run jupiterplot

sbatch -N 1 -n 2 --mem=100000 -t 2-0 -p priority -q msn --wrap="jupiter name=tibetan_AMouflon ref=tibetan.fna fa=AMouflon.fna"

job id 6743041

I think I need to tell sbatch to run perl

sbatch -N 1 -n 2 --mem=100000 -t 2-0 -p priority -q msn --wrap="perl jupiter name=tibetan_AMouflon ref=tibetan.fna fa=AMouflon.fna"

jobid 6743043

notes weird language warnings but think real error was bwa isn't on the system path
```

install bwa environment. any other needs?

```
mamba install -c bioconda bwa
mamba install -c bioconda minimap2 samtools

>this might fix language warning
export LC_ALL=en_US.utf8
export LANG=en_US.utf8

> Now try again

sbatch -N 1 -n 2 --mem=100000 -t 2-0 -p priority -q msn --wrap="perl jupiter name=tibetan_AMouflon ref=tibetan.fna fa=AMouflon.fna"

job id 6743049

-- Job maybe seems froze.. end of output seems stuck at 

bwa mem -t 8 -x intractg tibetan_AMouflon_reference.fa tibetan_AMouflon-agp.fa > tibetan_AMouflon-agp.sam
[M::bwa_idx_load_from_disk] read 0 ALT contigs
[M::process] read 6 sequences (99010889 bp)...
[M::process] read 14 sequences (175092624 bp)...

--Also before that there was a warning that kept getting repeated

Use of uninitialized value within %bandStr in concatenation (.) or string at /lustre/project/rumen_longread_metagenome_assembly/tfuller/JupiterPlot-1.0/bin/generateKaryotype.pl line 62, <> line 33173126
```

#12/2/21

```
>Run overnight did move forward more.

now has 2 new lines

[M::mem_process_seqs] Processed 6 reads in 52278.674 CPU sec, 42227.179 real sec
[M::process] read 20 sequences (97420541 bp)...

so processed 6 reads in just under 12 hours.. This likely can be sped up but not sure if tasks per node or mem should be increased or something else?
```
#12/3/21

Run Aborted because of time limit. removed .sam file because was likely incomplete and run again to see if it picks up where it left off

```

module load mpc/1.0.3 gmp/6.1.0 mpfr/3.1.3 gcc/8.1.0 openblas/0.2.15 python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/jupiterplot

export LC_ALL=en_US.utf8
export LANG=en_US.utf8

sbatch -N 1 -n 4 --mem=150000 -p priority -q msn --wrap="perl jupiter name=tibetan_AMouflon ref=tibetan.fna fa=AMouflon.fna"

job id 6757442
```

#12/6/21

BWA mem is still running. This definitely should not be the case.