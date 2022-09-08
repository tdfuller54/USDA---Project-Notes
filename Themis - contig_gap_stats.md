#2/1/22

###Get contig\_gap\_stats rule working and test output

--Looked at assembly_stats rule and calcFastaSumStats.py script and output (mapped/{asm}/stats.tab) for reference

-- noticed this is where contigN50 was improperly labeled. Corrected script to label ScaffoldN50 but note that I need to correct the label in the summary_table rule so this is correct in webpage as well.

#2/2/22

###Pick up were I left off with making edits to snakefile and scripts for contig\_gap\_stats rule.

--Added rule to clsuter.json file for snakemake job submission handling.

--Added output (mapped/{asm}/contig_stats.tab) to rule 'all' to force generate the file.

Will add calcContigGapN50.py script to scripts folder and run this to generate the output. After that will work to incorporate the proper contigN50 and other stats into the summary_table and the webpage table output.

```bash
srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

module load python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/themis_fuller/

cd /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM

git pull

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" -R contig_gap_stats
```

--Looking into the summary_table.py it looks like I need to write in any relevant values into the "solid" defaultdic(list). This should be just like the for statement for the stats.tab input.

--Made the input constats for snakemake for contig_stats.tab.

-- Should contig number be changed to scaffold number and add contig number from contig_stats?????

-- Should total bases be from scaffold (with N's) or from contigs????? Maybe both???

-- Number of gaps is the number of lines in gaps.bed
-- gaps.stats shows percent of gaps per bp per chromosome. Need this to be calculated to total? Or are these even true gaps? Were they generated simply by attaching contigs into scaffolds for spaceholders??

-- sum of gaps.stats column 3 (sum of N) divided by sum of gaps.stats column 2 (chromosome length) times 100 is percent of bp that are N in total assembly

```bash
git pull

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" -R contig_gap_stats assembly_stats summary_table

--made edit to i think resolve error with reading in header and running again

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" -R summary_table
```

---This for some reason executed most of the rules for snakemake. So everything is basically rerunning. WHY??

#2/4/22
###Returned error in summary_table rule. Identfied small syntax error. Made correction and am running again

```bash

srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

module load python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/themis_fuller/

cd /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM

git pull

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" -R summary_table
```

-Worked well

-- Will work to include gap data
-- Will change asm in description to full word in tables
-- Still need to invert circos input...
-- Anything else???

-- Now can likely add gap count and percentage. Where should this data be added (which table)? What about other data like longest gap and average gap size???

-- for vetch eudicots_odb10???