#1/27/22

###Add submodule to Themis-ASM

-in command prompt

```bash
cd C:\Users\tyson.fuller\OneDrive - USDA\Documents\GitHub\Themis-ASM

git submodule add https://github.com/njdbickhart/GetMaskBedFasta.git

git commit -m "whatever comment was."

git push
```

-in ceres

```bash
srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

module load python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/themis_fuller/

cd /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM

git pull

-Now pull submodule

git submodule update --init --recursive

-now run rule

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" -R mask_bed_fasta
```

#1/28/22
###Want to incorporate Derek's GetMaskBedFasta update into repository.

-In command prompt in Themis directory
```bash
git submodule update --remote --merge
git commit -a -m "Updated submodule message."
git push
```
-On ceres in Themis directory

```bash
git pull

git submodule update --init --recursive (just to be extra safe)
```

-Another way
--in themis/getmaskbedfasta directory

```bash
git fetch

git log --oneline origin/master -3 (find newest commit number) this case 059d07c

git checkout -q 059d07c

cd ..

git add . (must actually tell commit to index files for commit)

git commit -m "Added new commits from the submodule repository"

git push
```

--in ceres in themis
```
git pull

git submodule update --init --recursive (just to be extra safe)

--NOW RUN RULE AGAIN

module load python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/themis_fuller/

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" -R mask_bed_fasta
```

Worked but you can note that the Rambouillet2 assembly didn't have many gaps (8 between both chromosomes). Also every signle gap wa 500bp.. Seems strange?? Also OAR4 had a lot more gaps and differences in gap sizes but the median gap was right at 100bp.. maybe discrepancies in sequencing or assembly methods leading to standard gap sizes when created??