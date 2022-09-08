##Create directories in ceres to run both gmap (using trinity) and run RNAquast.



```bash
srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

module load python_3/3.6.6 miniconda/3.6 trinityrnaseq/2.11.0 blast+/2.11.0

>in /project/forage_assemblies/tfuller


mkdir trinity

>in trinity/

(transcripts symlink)
ln -s /project/forage_assemblies/assemblies/vetch_transcripts/sahv.transcripts.fasta sahv.transcripts.fasta

(assembly symlink)
ln -s /project/forage_assemblies/assemblies/legume_comparison/ReformatedV.fa ReformatedV.fa

-try getting trinity summary stats

TrinityStats.pl sahv.transcripts.fasta > trinitystats.txt
```

This works but there is not gmap in trinity. Honestly it might be best to try rnaquast first because gmap documentation is not clear and I think rnaquast utilizes gmap anyway so the output might include everything needed

```bash

>in /project/forage_assemblies/tfuller

mkdir rnaquast

(transcripts symlink)
ln -s /project/forage_assemblies/assemblies/vetch_transcripts/sahv.transcripts.fasta sahv.transcripts.fasta

(assembly symlink)
ln -s /project/forage_assemblies/assemblies/legume_comparison/ReformatedV.fa ReformatedV.fa

TrinityStats.pl sahv.transcripts.fasta > trinitystats.txt

conda create -p /project/rumen_longread_metagenome_assembly/environments/rnaquast/ -c bioconda -y rnaquast

--This is frozen on solving environment. Try it using mamba

conda create -p /project/rumen_longread_metagenome_assembly/environments/rnaquast/ -c bioconda -c conda-forge mamba

--update conda after
conda update -n base -c defaults conda

conda activate /project/rumen_longread_metagenome_assembly/environments/rnaquast

mamba install -c bioconda rnaquast


>Now run rnaQUAST in rnaquast directory

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="python /project/rumen_longread_metagenome_assembly/environments/rnaquast/bin/rnaQUAST.py \
--transcripts /project/forage_assemblies/tfuller/rnaquast/sahv.transcripts.fasta \
--reference /project/forage_assemblies/tfuller/rnaquast/ReformatedV.fa \
--labels Vvillosa \
--output_dir /project/forage_assemblies/tfuller/rnaquast/Vvillosa_rnaQUAST_results/"

job id 7677657

>Kept showing no module named matplotlib. Looking online I saw people mention that the wrong matplotlib version was default installing so used pip3 to install

pip3 install matplotlib

>now it says gffutils is not installed so will try this for gffutils

pip3 install gffutils

>now says no module named joblib so will do same thing

pip3 install joblib

>Now run again

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="python /project/rumen_longread_metagenome_assembly/environments/rnaquast/bin/rnaQUAST.py \
--transcripts /project/forage_assemblies/tfuller/rnaquast/sahv.transcripts.fasta \
--reference /project/forage_assemblies/tfuller/rnaquast/ReformatedV.fa \
--labels Vvillosa \
--output_dir /project/forage_assemblies/tfuller/rnaquast/Vvillosa_rnaQUAST_results/"

job id 7677667

It's working!!