#12/7/21

Do some house cleaning by putting export LANG commands in .bashrc, creating symbolic link for .conda, and creating symbolic link to test data files for themis run.

```
>in home directory
>
nano .bashrc

add lines:
export LC_ALL=en_US.utf8
export LANG=en_US.utf8

>in tfuller

mkdir .conda
cp -r ~/.conda .conda

>in home directory

rm -r .conda/

ln -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/.conda .conda/

>in tfuller

mkdir themis_test

>in themis_test

ln -s /lustre/project/rumen_longread_metagenome_assembly/sequence_data/themis_test/oar4Test.fasta oar4Test.fasta

ln -s /lustre/project/rumen_longread_metagenome_assembly/sequence_data/themis_test/ram2Test.fasta ram2Test.fasta

ln -s /lustre/project/rumen_longread_metagenome_assembly/sequence_data/themis_test/rambouillet_reads.R1.fastq rambouillet_reads.R1.fastq

ln -s /lustre/project/rumen_longread_metagenome_assembly/sequence_data/themis_test/rambouillet_reads.R2.fastq rambouillet_reads.R2.fastq

ln -s /lustre/project/rumen_longread_metagenome_assembly/sequence_data/themis_test/texel_reads.R1.fastq texel_reads.R1.fastq

ln -s /lustre/project/rumen_longread_metagenome_assembly/sequence_data/themis_test/texel_reads.R2.fastq texel_reads.R2.fastq
```

>Prepare everything for test run in tfuller/themis_test

```
module load python_3/3.6.6 miniconda/3.6

conda create -p /KEEP/rumen_longread_metagenome_assembly/themis_fuller -c bioconda -y -c conda-forge mamba

mamba install -c bioconda merqury samtools bwa
```

>Busco lineage used by wdx run
"buscoLineage" : "mammalia_odb10"

>Everything is installed in environment. Let's try to run themis-ASM

```
sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="python themisASM.py  \
	-a /lustre/project/rumen_longread_metagenome_assembly/tfuller/themis_test/oar4Test.fasta  \
	-a /lustre/project/rumen_longread_metagenome_assembly/tfuller/themis_test/ram2Test.fasta  \
	-n OAR4	 \
	-n Rambouillet2 \
	-b mammalia_odb10 \
	-f /lustre/project/rumen_longread_metagenome_assembly/tfuller/themis_test/rambouillet_reads.R1.fastq,/lustre/project/rumen_longread_metagenome_assembly/tfuller/themis_test/rambouillet_reads.R2.fastq \
	-f /lustre/project/rumen_longread_metagenome_assembly/tfuller/themis_test/texel_reads.R1.fastq,/lustre/project/rumen_longread_metagenome_assembly/tfuller/themis_test/texel_reads.R2.fastq \
	-s Ram2Reads  \
	-s Texel \
	-j 50 \
	-c 'sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}'"

job ID 6770888
```

#12/8/21

>Busco didn't download properly. Derek told me he uses a work around. I am testing just adding python to environment yaml first. Otherwise will run with work around and test a couple other things in meantime.

```
module load python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/themis_fuller/

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="python themisASM.py  \
	-a /lustre/project/rumen_longread_metagenome_assembly/tfuller/themis_test/oar4Test.fasta  \
	-a /lustre/project/rumen_longread_metagenome_assembly/tfuller/themis_test/ram2Test.fasta  \
	-n OAR4	 \
	-n Rambouillet2 \
	-b mammalia_odb10 \
	-f /lustre/project/rumen_longread_metagenome_assembly/tfuller/themis_test/rambouillet_reads.R1.fastq,/lustre/project/rumen_longread_metagenome_assembly/tfuller/themis_test/rambouillet_reads.R2.fastq \
	-f /lustre/project/rumen_longread_metagenome_assembly/tfuller/themis_test/texel_reads.R1.fastq,/lustre/project/rumen_longread_metagenome_assembly/tfuller/themis_test/texel_reads.R2.fastq \
	-s Ram2Reads  \
	-s Texel \
	-j 50 \
	-c 'sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}' \
	-r"

job ID 6772171

Seems new error occurred in that the parameter "--mode" was not provided in busco download. Will look into this further tomorrow.
```

#12/9/21
> One more try with -m set to genome in download_busco rule

```
module load python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/themis_fuller/

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="python themisASM.py  \
	-a /lustre/project/rumen_longread_metagenome_assembly/tfuller/themis_test/oar4Test.fasta  \
	-a /lustre/project/rumen_longread_metagenome_assembly/tfuller/themis_test/ram2Test.fasta  \
	-n OAR4	 \
	-n Rambouillet2 \
	-b mammalia_odb10 \
	-f /lustre/project/rumen_longread_metagenome_assembly/tfuller/themis_test/rambouillet_reads.R1.fastq,/lustre/project/rumen_longread_metagenome_assembly/tfuller/themis_test/rambouillet_reads.R2.fastq \
	-f /lustre/project/rumen_longread_metagenome_assembly/tfuller/themis_test/texel_reads.R1.fastq,/lustre/project/rumen_longread_metagenome_assembly/tfuller/themis_test/texel_reads.R2.fastq \
	-s Ram2Reads  \
	-s Texel \
	-j 50 \
	-c 'sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}' \
	-r"
Job ID 6773410

weird bash error when running rule run_merqury

run dos2unix to try and fix based on web search
dos2unix merqury_spectra_venn.sh

maybe worked but now have a freebayes error

{snakemake.input.ref}.fai returns 
tfuller/themis_test/ram2Test.fasta.fai

should be
tfuller/themis_test/fastas/Rambouillet2.fa.fai

note this is in generate_regions_.py but later in freebayes-parallel {snakemake.input.ref} is called again. This likely won't matter since a symbolic link to the assemblies are in themis_test/fastas as well. Called Rambouillet2.fa for example
```

#12/13/21

Seen themis was still running but lumpy rule actually returned error but never shut down whole pipeline or even smaller job. Used scancel to stop them

>Made edit to rule download_busco and corrected input for freebayes. Added samblaster and sambamba to lumpy environment as well. Cleared out all folders and scripts from themis and will now get them from my github to easily keep track of changes and upload them into ceres

```
module load python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/themis_fuller/

>in tfuller folder

git clone https://github.com/tdfuller54/Themis-ASM.git

>in Themis-ASM folder

ln -s /lustre/project/rumen_longread_metagenome_assembly/sequence_data/themis_test/oar4Test.fasta oar4Test.fasta

ln -s /lustre/project/rumen_longread_metagenome_assembly/sequence_data/themis_test/ram2Test.fasta ram2Test.fasta

ln -s /lustre/project/rumen_longread_metagenome_assembly/sequence_data/themis_test/rambouillet_reads.R1.fastq rambouillet_reads.R1.fastq

ln -s /lustre/project/rumen_longread_metagenome_assembly/sequence_data/themis_test/rambouillet_reads.R2.fastq rambouillet_reads.R2.fastq

ln -s /lustre/project/rumen_longread_metagenome_assembly/sequence_data/themis_test/texel_reads.R1.fastq texel_reads.R1.fastq

ln -s /lustre/project/rumen_longread_metagenome_assembly/sequence_data/themis_test/texel_reads.R2.fastq texel_reads.R2.fastq


>Now start a new themis run inside Themis-ASM folder. This allows every file from my git profile to be maintained in this folder so new errors can be corrected and uploaded to ceres for testing more easily

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="python themisASM.py  \
	-a /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/oar4Test.fasta  \
	-a /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/ram2Test.fasta  \
	-n OAR4	 \
	-n Rambouillet2 \
	-b mammalia_odb10 \
	-f /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/rambouillet_reads.R1.fastq,/lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/rambouillet_reads.R2.fastq \
	-f /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/texel_reads.R1.fastq,/lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/texel_reads.R2.fastq \
	-s Ram2Reads  \
	-s Texel \
	-j 50 \
	-c 'sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}'"

job ID 6786807

needed to correct syntax error and will now resume job

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="python themisASM.py  \
	-a /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/oar4Test.fasta  \
	-a /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/ram2Test.fasta  \
	-n OAR4	 \
	-n Rambouillet2 \
	-b mammalia_odb10 \
	-f /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/rambouillet_reads.R1.fastq,/lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/rambouillet_reads.R2.fastq \
	-f /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/texel_reads.R1.fastq,/lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/texel_reads.R2.fastq \
	-s Ram2Reads  \
	-s Texel \
	-j 50 \
	-c 'sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}' \
	-r"

job ID 6786812

slightly odd error where busco_downloads directory claims to exist. made slight modification to mkdir command including -p flag that should fix this

12/14/21

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="python themisASM.py  \
	-a /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/oar4Test.fasta  \
	-a /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/ram2Test.fasta  \
	-n OAR4	 \
	-n Rambouillet2 \
	-b mammalia_odb10 \
	-f /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/rambouillet_reads.R1.fastq,/lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/rambouillet_reads.R2.fastq \
	-f /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/texel_reads.R1.fastq,/lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/texel_reads.R2.fastq \
	-s Ram2Reads  \
	-s Texel \
	-j 50 \
	-c 'sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}' \
	-r"

job ID 6789458

download_busco failed saying connection timed out


wget -r -nd -l1 -4 -np -A "mammalia_odb10.*.tar.gz" https://busco-data.ezlab.org/v5/data/lineages/ --no-check-certificate

Won't work like this either now.

pinging the host server didn't respond and telnet on the port also didn't work.

Checked website and it will not load on chrome. Website is likely down currently. Will retry in the morning and transfer files if still won't work to troubleshoot further later
```

#12/15/21

>Website seems back up. test wget connection just in ceres first

```
wget -r -nd -l1 -np -A "mammalia_odb10.*.tar.gz" https://busco-data.ezlab.org/v5/data/lineages/ --no-check-certificate
```

>Website is suddenly back down both on and off vpn. simply copy files over to bypass this for now

```
cp -r /lustre/project/rumen_longread_metagenome_assembly/wdxrom/busco_downloads /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/

>Everything transferred so simply try running the script again

module load python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/themis_fuller/

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="python themisASM.py  \
	-a /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/oar4Test.fasta  \
	-a /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/ram2Test.fasta  \
	-n OAR4	 \
	-n Rambouillet2 \
	-b mammalia_odb10 \
	-f /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/rambouillet_reads.R1.fastq,/lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/rambouillet_reads.R2.fastq \
	-f /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/texel_reads.R1.fastq,/lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/texel_reads.R2.fastq \
	-s Ram2Reads  \
	-s Texel \
	-j 50 \
	-c 'sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}' \
	-r"

job id 6793022

> Busco still tries to access the internet. made edits to busco_lift.py to bypass that and am trying again

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="python themisASM.py  \
	-a /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/oar4Test.fasta  \
	-a /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/ram2Test.fasta  \
	-n OAR4	 \
	-n Rambouillet2 \
	-b mammalia_odb10 \
	-f /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/rambouillet_reads.R1.fastq,/lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/rambouillet_reads.R2.fastq \
	-f /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/texel_reads.R1.fastq,/lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/texel_reads.R2.fastq \
	-s Ram2Reads  \
	-s Texel \
	-j 50 \
	-c 'sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}' \
	-r"

job ID 6793032
```

>Note to unlock command needed is snakemake --snakefile themisSnakefile --unlock

```
now run again

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="python themisASM.py  \
	-a /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/oar4Test.fasta  \
	-a /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/ram2Test.fasta  \
	-n OAR4	 \
	-n Rambouillet2 \
	-b mammalia_odb10 \
	-f /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/rambouillet_reads.R1.fastq,/lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/rambouillet_reads.R2.fastq \
	-f /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/texel_reads.R1.fastq,/lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/texel_reads.R2.fastq \
	-s Ram2Reads  \
	-s Texel \
	-j 50 \
	-c 'sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}' \
	-r"

job ID 6793068

>Create DAG of run

snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" --dag | dot -Tpdf > dag.pdf