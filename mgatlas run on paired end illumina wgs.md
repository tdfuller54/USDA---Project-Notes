# 7/7/21

Goal - To use metagenome-atlas on paired end illumina seq data located
> Ceres: /lustre/project/rumen_longread_metagenome_assembly/sheep_poop/illumina_wgs/
> Cluster Profile: ~/.config/snakemake/cluster


```
#First load modules and generate a copy of the files in a new directory within tfuller

module load mpc/1.0.3 gmp/6.1.0 mpfr/3.1.3 gcc/8.1.0 openblas/0.2.15 python_3/3.6.6 miniconda/3.6

cd /lustre/project/rumen_longread_metagenome_assembly/tfuller

mkdir illumina_atlas_run

cp LIB101996_R1.fastq.gz LIB101996_R2.fastq.gz /lustre/project/rumen_longread_metagenome_assembly/tfuller/illumina_atlas_run/

#Activate mgatlas_fuller environment and run atlas

conda activate /KEEP/rumen_longread_metagenome_assembly/mgatlas_fuller

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="atlas init --db-dir databases /lustre/project/rumen_longread_metagenome_assembly/tfuller/illumina_atlas_run/"

##Note that atlas always returns error "RuntimeError: Click will abort further execution because Python was configured to use ASCII as encoding for the environment. Consult https://click.palletsprojects.com/unicode-support/ for mitigation steps."

##To fix
locale -a ##find syntaxt for english utf-8 "en_US.utf8"

export LC_ALL=en_US.utf8
export LANG=en_US.utf8

## Remove out file with error and run atlas init again

rm slurm-6101245.out

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="atlas init --db-dir databases /lustre/project/rumen_longread_metagenome_assembly/tfuller/illumina_atlas_run/"

job id 6101266

## Atlas init ran properly and created samples.tsv and config.yaml in working directory. Now need to run atlas but with the cluster profile generated previously to properly submit each job to the correct nodes and with the right qos

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="atlas run all --jobs 99 --profile cluster"

job id 6101459

```

Multiple errors on exit

1.
Error executing rule calculate_insert_size on cluster (jobid: 16, external: 6110860)
log: LIB101996/logs/QC/stats/calculate_insert_size.log
  Estimated valid kmers:          2041508401
Prefilter time: 4009.170 seconds.
After prefilter:
Memory: max=54760m, free=31681m, used=23079m

Estimated kmer capacity:        1210279403
After table allocation:
Memory: max=54760m, free=16626m, used=38134m

Exception in thread "Thread-22" Exception in thread "Thread-24" java.lang.OutOfMemoryError: Java heap space
 at ukmer.HashForestU.makeNode(HashForestU.java:47)
 at ukmer.HashForestU.makeNode(HashForestU.java:43)
 at ukmer.HashForestU.incrementAndReturnNumCreated(HashForestU.java:130)
 at ukmer.HashArrayU1D.incrementAndReturnNumCreated(HashArrayU1D.java:72)
 at ukmer.HashBufferU.dumpBuffer_inner(HashBufferU.java:196)
 at ukmer.HashBufferU.dumpBuffer(HashBufferU.java:168)
 at ukmer.HashBufferU.incrementAndReturnNumCreated(HashBufferU.java:57)
 at ukmer.KmerTableSetU$LoadThread.addKmersToTable(KmerTableSetU.java:576)
 at ukmer.KmerTableSetU$LoadThread.run(KmerTableSetU.java:511)

slurm log 6110860
slurmstepd: error: *** JOB 6110860 ON ceres18-compute-2 CANCELLED AT 2021-07-08T01:22:49 DUE TO TIME LIMIT ***

2.
Error executing rule "error_correction"
LIB101996/logs/assembly/pre_process/error_correction_QC.log
Exception in thread "Thread-8" Exception in thread "Thread-6" Exception in thread "Thread-12" Exception in thread "Thread-7" Exception in thread "Thread-11" java.lang.OutOfMemoryError: Java heap space
java.lang.OutOfMemoryError: Java heap space
java.lang.OutOfMemoryError: Java heap space
at kmer.HashForest.makeNode(HashForest.java:44)
at kmer.HashForest.incrementAndReturnNumCreated(HashForest.java:92)
at kmer.HashArray1D.incrementAndReturnNumCreated(HashArray1D.java:71)
at kmer.HashBuffer.dumpBuffer_inner(HashBuffer.java:167)
at kmer.HashBuffer.dumpBuffer(HashBuffer.java:147)
at kmer.HashBuffer.incrementAndReturnNumCreated(HashBuffer.java:41)
at kmer.KmerTableSet$LoadThread.addKmersToTable(KmerTableSet.java:597)
at kmer.KmerTableSet$LoadThread.run(KmerTableSet.java:531)



slurm log 6110861
slurmstepd: error: *** JOB 6110861 ON ceres18-compute-3 CANCELLED AT 2021-07-08T01:52:51 DUE TO TIME LIMIT ***



Complete log: /lustre/project/rumen_longread_metagenome_assembly/tfuller/illumina_atlas_run/.snakemake/log/2021-07-07T130254.049945.snakemake.log

Digging into the code I can find where a "JAVA_MEM_FRACTION" value is multiplied by the "MEM" value to get "java_mem". Editing these defaults might solve the error


### Trying again with mem and large_mem increase. This will also increase java_mem by default

#Made new directory and copied reads into it

```
sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="atlas init --db-dir databases /lustre/project/rumen_longread_metagenome_assembly/tfuller/illumina_atlas_run_2/

job id 6118812

changed config.yaml mem=250 and large_mem=230

sbatch -N 1 -n 2 --mem=80000 -p priority -q msn --wrap="atlas run all --jobs 50 --profile cluster"

job id 6118826
```

>Stopped again at error_correction and calculate_insert_size

error_correction log: LIB101996/logs/assembly/pre_process/error_correction_QC.log


calculate_insert_sizelog: LIB101996/logs/QC/stats/calculate_insert_size.log

Those logs don't show anything obvious but slurm-log still timed out. Need more time or stuck somehow???


#Try 3 on 7/9/21. Will run with crazy amount of time and memory because I can and hopefully will work

```
mkdir illumina_atlas_run_3

-- make copies of seq files in new directory
cp LIB101996_R1.fastq.gz LIB101996_R2.fastq.gz /lustre/project/rumen_longread_metagenome_assembly/tfuller/illumina_atlas_run_3/

-- load dependent modules
module load mpc/1.0.3 gmp/6.1.0 mpfr/3.1.3 gcc/8.1.0 openblas/0.2.15 python_3/3.6.6 miniconda/3.6

-- fix weird utf8 error
export LC_ALL=en_US.utf8
export LANG=en_US.utf8

-- Activate mgatlas_fuller environment and run atlas
conda activate /KEEP/rumen_longread_metagenome_assembly/mgatlas_fuller

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="atlas init --db-dir databases /lustre/project/rumen_longread_metagenome_assembly/tfuller/illumina_atlas_run_3/"

job id 6121659

-- Fix config.yaml so mem=350, large_mem=320, assembly_memory=350 and all time=336 h

-- Run atlas in illumina_atlas_run_3 with updated parameters and hope no memory or time out errors occur
sbatch -N 1 -n 2 --mem=80000 -p priority -q msn --wrap="atlas run all --jobs 50 --profile cluster"
```

#7/15/21 noticed job crashed saying no snakemake module. Likely because I had to uninstall and reinstall things for the DAG generation. Trying the run again in hopes that it will recognize what has been done already and skip those steps

```
conda activate /KEEP/rumen_longread_metagenome_assembly/mgatlas_fuller

export LC_ALL=en_US.utf8
export LANG=en_US.utf8

sbatch -N 1 -n 2 --mem=80000 -p priority -q msn --wrap="atlas run all --jobs 50 --profile cluster"

failed for some reason
slurm-6149457.out

Ran conda install snakemake and it seemed to fix it but didn't recognize --cpus-per-node option I changed to so changed it back
slurm-6149852.out


sbatch -N 1 -n 2 --mem=80000 -p priority -q msn --wrap="atlas run all --jobs 50 --profile cluster"
slurm-6149892.out



