-- Anything else???

-- What about other data like longest gap and average gap size???

-- for vetch use eudicots_odb10

--Once complete it should be ready to run the full data Derek sent for the vetch genome assembly


--rerun to test circos plot and make sure temp busco files stay

```bash
sbatch -N 1 -n 2 --mem=25000 -t 2-0 -p priority -q msn snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -t 2-0 --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" -R run_busco circos_plot
```

#2/8/22

Incorporated gap data in summary table. Will now run updated rule to ensure output is correct and see final layout. If this works I can run Themis on real data from Derek tomorrow.

```bash
srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

module load python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/themis_fuller/

cd /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM

git pull

sbatch -N 1 -n 2 --mem=25000 -t 2-0 -p priority -q msn snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -t 2-0 --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" -R summary_table
```

-- Worked after troubleshooting some rounding errors
