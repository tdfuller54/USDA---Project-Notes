#1/5/22

##Use tables to put figures side by side in webpage.

>Made edits to the generate summary table script. Will now run that rule to see what the new output looks like. Should be properly separated.

```bash

module load python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/themis_fuller/

cd /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM

git pull

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" -R summary_table

job ID 6855968
```

>Table looks clean. Now need to make appropriate edits into create_webpage script to reorganize images and tables into tables

#1/6/22
## Made first edits to create_webpage.py to actually format items side by side within a table. Run this as a test to get an idea of general appearance and see if there are any errors with syntax.

```bash

srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

module load python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/themis_fuller/

cd /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM

git pull

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" -R comparison_webpage
```

try 12 is where I put plots side by side and these rendered fine.

-Added row to summary_table to list file names. Test to see if this works

```bash

srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

module load python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/themis_fuller/

cd /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM

git pull

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" -R summary_table

-Try to rerun multiple rules to generate all new plots at once

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" -R ideogram_plot -R plot_frc -R ngx_plot -R plot_busco

--Seems to only force run the last of all the called rules. Will try with all rules in a row (no -R between)

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" -R ideogram_plot plot_frc ngx_plot plot_busco

-- WORKED!
```

#1/26/22
Made size edits to ideogram plot and added circos plot. Circos plot fills in nicely. ideogram likely needs a little bigger (maybe from 500 to 550px??)