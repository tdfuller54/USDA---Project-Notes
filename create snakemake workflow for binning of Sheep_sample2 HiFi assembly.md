#7-21-21 Goal to create a snakemake workflow that bins HiFi assembly from Sheep_2 using metabat and CONCOCT using illumina reads from mg-atlas run for read coverage estimates. Then run these bin assignments through DASTool

>create a new directory "Sheep_2_HiFi_binning" within tfuller to work in and create a symbolic link within this directory that points to Sheep_2 HiFi assembly fasta

```
mkdir Sheep_2_HiFi_binning

ln -s /lustre/project/rumen_longread_metagenome_assembly/assemblies/sheep/sheep_sample2_metaflye/assembly.fasta sheep_sample2.fasta
```

>Emailed Derek regarding Metabat requirements. Metabat needs the assembly fasta as well as sorted BAM files of reads individually aligned to assembly. This is useless with mgatlas generated BAMs as they aligned to a different reference. Will still plan to work with illumina reads unless told otherwise and work on aligning to HiFi assembly with BWA

>Copy illumina QC reads to Sheep_2_HiFi_binning

```
cp LIB101996_QC_R1.fastq.gz LIB101996_QC_R2.fastq.gz LIB101996_QC_se.fastq.gz /lustre/project/rumen_longread_metagenome_assembly/tfuller/Sheep_2_HiFi_binning
```

>Created snakefile with environments that should build BAM files, sort and index them, and create a metabat depth file. Will try to upload this to Ceres and run it.

```
-- load dependent modules
module load mpc/1.0.3 gmp/6.1.0 mpfr/3.1.3 gcc/8.1.0 openblas/0.2.15 python_3/3.6.6 miniconda/3.6

-- Activate mgatlas_fuller environment
conda activate /KEEP/rumen_longread_metagenome_assembly/mgatlas_fuller

-- fix weird utf8 error (just in case)
export LC_ALL=en_US.utf8
export LANG=en_US.utf8

-- move to directory where files were uploaded and attempt sbatch job submission

cd /lustre/project/rumen_longread_metagenome_assembly/tfuller/Sheep_2_HiFi_binning/

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Sheep_2_HiFi_binning/Snakefile --configfile /lustre/project/rumen_longread_metagenome_assembly/tfuller/Sheep_2_HiFi_binning/config.yaml --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Sheep_2_HiFi_binning/cluster.yaml --cluster "sbatch -N 1 --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -p priority -q msn" --use-conda --jobs 50 --verbose --latency-wait 40
```

#Note the errors. BWA can be run with raw seq zipped data and not QC data so will copy over right files. It seems that BWA index needs to be ran (files in tutorial data already had proper generated files to skip this). Will add a rule to do this. Can also potentially skip the samtools "view" in BWA_map and use sort in it's place.

#7/28/21 Attempting to execute snakemake workflow with modifications noted above

```
-- make copies of seq files in  directory
cp LIB101996_R1.fastq.gz LIB101996_R2.fastq.gz /lustre/project/rumen_longread_metagenome_assembly/tfuller/Sheep_2_HiFi_binning

-- load dependent modules
module load mpc/1.0.3 gmp/6.1.0 mpfr/3.1.3 gcc/8.1.0 openblas/0.2.15 python_3/3.6.6 miniconda/3.6

-- create and activate snakemake_fuller environment
conda create -p /KEEP/rumen_longread_metagenome_assembly/snakemake_fuller -c bioconda -y -c conda-forge mamba

conda activate /KEEP/rumen_longread_metagenome_assembly/snakemake_fuller

mamba install -c conda-forge -c bioconda snakemake


-- attempt to run snakemake workflow in Sheep_2_HiFi_binning

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s Snakefile --configfile config.yaml --cluster-config cluster.yaml --cluster "sbatch -N 1 --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -p priority -q msn" --use-conda --jobs 50 --verbose --latency-wait 40

--Log
2021-07-28T161749.852867.snakemake.log
```

#Attempted to run bwa_sort at same time as bwa_index causing sort to return an error. Added other output files of index to the input of sort. Hopefully this will prevent sort from running until index is finished. Also will remove "verbose" flag as this seems too wordy and change wait to higher number

```
sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s Snakefile --configfile config.yaml --cluster-config cluster.yaml --cluster "sbatch -N 1 --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -p priority -q msn" --use-conda --jobs 50 --latency-wait 60

--Log
2021-07-28T185104.670835.snakemake.log
```

#8/2/21-8/3/21 Add rule to run metabat2 with assembly.fa and depth.txt files. Start working on rules needed to run concoct

```
-- load dependent modules
module load mpc/1.0.3 gmp/6.1.0 mpfr/3.1.3 gcc/8.1.0 openblas/0.2.15 python_3/3.6.6 miniconda/3.6

--Activate snakemake_fuller environment
conda activate /KEEP/rumen_longread_metagenome_assembly/snakemake_fuller

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s Snakefile --configfile config.yaml --cluster-config cluster.yaml --cluster "sbatch -N 1 --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -p priority -q msn" --use-conda --jobs 50 --verbose --latency-wait 60

6225490 last snakemake job id

--8/3/21
sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s Snakefile --configfile config.yaml --cluster-config cluster.yaml --cluster "sbatch -N 1 --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -p priority -q msn" --use-conda --jobs 50 --verbose --latency-wait 60

job id 6226109

--8/5/21
--Added rules to convert files from concoct and metabat2 for DASTool input. Added rule to run DASTool.

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s Snakefile --configfile config.yaml --cluster-config cluster.yaml --cluster "sbatch -N 1 --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -p priority -q msn" --use-conda --jobs 50 --verbose --latency-wait 60

job id 6230191

--8/6/21
--Attempting to run dastool again using version 1.1.2 to generate plots with "--create_plots". Deleted hidden conda environment and dastool output directory and running again with yaml saying =1.1.2

-- load dependent modules
module load mpc/1.0.3 gmp/6.1.0 mpfr/3.1.3 gcc/8.1.0 openblas/0.2.15 python_3/3.6.6 miniconda/3.6

--Activate snakemake_fuller environment
conda activate /KEEP/rumen_longread_metagenome_assembly/snakemake_fuller

-- fix weird utf8 error (just in case)
export LC_ALL=en_US.utf8
export LANG=en_US.utf8

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s Snakefile --configfile config.yaml --cluster-config cluster.yaml --cluster "sbatch -N 1 --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -p priority -q msn" --use-conda --jobs 50 --verbose --latency-wait 60

job ID 6235668