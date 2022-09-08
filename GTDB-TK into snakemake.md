#Work to run DASTool bins through GTDB-TK.

-First need to change DASTool to write bins to fasta files
-Will use those directory of those as input into GTDB-TK

-Download the GTDB-TK database. This is done with download-db.sh calling the path to download by GTDBTK_DATA_PATH

#Cannot find clearly where bins are written. will go back to that with placeholder to move forward

#9/8/21 attempt to run snakefile to execute gtdbtk as part of workflow

```
load dependent modules
module load mpc/1.0.3 gmp/6.1.0 mpfr/3.1.3 gcc/8.1.0 openblas/0.2.15 python_3/3.6.6 miniconda/3.6

--Activate snakemake_fuller environment
conda activate /KEEP/rumen_longread_metagenome_assembly/snakemake_fuller

-- fix weird utf8 error (just in case)
export LC_ALL=en_US.utf8
export LANG=en_US.utf8

--Try to run newly uploaded snakefile with new rules folder and files

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s Snakefile --configfile config.yaml --cluster-config cluster.yaml --cluster "sbatch -N 1 --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -p priority -q msn" --use-conda --jobs 50 --verbose --latency-wait 60

job id slurm-6324499.out

--Ran out of disk space while creating environment. Cleared tar files and trying again.

sbatch -N 1 -n 2 --mem=75000 -p priority -q msn snakemake -s Snakefile --configfile config.yaml --cluster-config cluster.yaml --cluster "sbatch -N 1 --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -p priority -q msn" --use-conda --jobs 50 --verbose --latency-wait 60

--Same error occurred. Tried creating independent environment as well and error still occurred.. emailed scinet about this issue.

-- Found tar file in home directory and a DAG.pdf. home directory still seems big but deleted those. Will keep an eye on home directory in case conda is saving caches there but for now everything is working.

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s Snakefile --configfile config.yaml --cluster-config cluster.yaml --cluster "sbatch -N 1 --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -p priority -q msn" --use-conda --jobs 50 --verbose --latency-wait 60

job id slurm-6326135.out

All worked except syntax error in rule classify. Fixed and rerun

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s Snakefile --configfile config.yaml --cluster-config cluster.yaml --cluster "sbatch -N 1 --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -p priority -q msn" --use-conda --jobs 50 --verbose --latency-wait 60

job id 6326167

error seems to be out of memory. oddly mem_mb and disc_mb are 1000 and another resource is listed as mem. mem should've overwritten mem_mb and also didn't seem to add the cluster.yaml defaults in general. Try to fix this and troubleshoot why cluster.yaml isn't working

changed cluster-config file to submit job with enough mem and the rule resource to "mem_mb". This did not change the disk_mb so will see how it works

job id 6329082