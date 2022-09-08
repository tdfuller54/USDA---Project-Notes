#7/6/22
####Use predownloaded swissprot database to annotate proteins predicted by Derek's RNA seq analysis. Also will run eggnog mapper to annotate proteins as well.

####script used to make database for swissprot

```bash
in /project/forage_assemblies/tfuller/swissp_db

sbatch -N 1 -n 4 --mem=200000 -p priority -q msn --wrap="diamond makedb --in uniprot_sprot.fasta -d swissprot"

-clean old annotation data from trinity runs

rm swissp_anno.tsv


-now run diamond against swissprot database with Derek's protein sequences

conda activate /project/rumen_longread_metagenome_assembly/environments/diamond/


sbatch -N 1 -n 8 --mem=200000 -p priority -q msn --wrap="diamond blastp --db swissprot.dmnd --query /90daydata/forage_assemblies/analysis/gene_phylogeny/gene_fastas/hairy_vetch.faa -o swissp_anno.tsv --outfmt 6 qseqid sseqid pident length mismatch gaps qstart qend sstart send evalue bitscore stitle --sensitive --threads 8 --top 1"

job id 7818779
```

next run eggnog


```bash

conda create -p /project/rumen_longread_metagenome_assembly/environments/eggnog/ -c bioconda eggnog-mapper

conda activate /project/rumen_longread_metagenome_assembly/environments/eggnog

python /project/forage_assemblies/tfuller/eggnog/eggnog-mapper-2.1.8/download_eggnog_data.py -P -y

sbatch -N 1 -n 8 --mem=200000 -p priority -q msn --wrap="python /project/forage_assemblies/tfuller/eggnog/eggnog-mapper-2.1.8/emapper.py -m diamond -i /90daydata/forage_assemblies/analysis/gene_phylogeny/gene_fastas/hairy_vetch.faa -o /project/forage_assemblies/tfuller/eggnog/output/ --cpu 8"

job id 7823542

```
eggnog version and database version
__VERSION__='2.1.8'
__DB_VERSION__='5.0.2'

eggnog output is output/.emapper.hits, .emapper.seed_orthologs, .emapper.annotations

---output/.emapper.annotations is what is needed and simply removed beginning lines


##7/11/22
####Run against ncbi- non redundant database as well to try and increase hits.

```bash
conda activate /project/rumen_longread_metagenome_assembly/environments/diamond/

--in tfuller/nrdb

sbatch -N 1 -n 8 --mem=200000 -p priority -q msn --wrap="diamond blastp --db nr.dmnd --query /90daydata/forage_assemblies/analysis/gene_phylogeny/gene_fastas/hairy_vetch.faa -o nr_anno.tsv --outfmt 6 qseqid sseqid pident length mismatch gaps qstart qend sstart send evalue bitscore stitle --sensitive --threads 8 --top 1"

job id 7827202
```

nrdb found
Reported 149395 pairwise alignments, 149395 HSPs.
43455 queries aligned.

swiss found
Reported 42757 pairwise alignments, 42757 HSPs.
32445 queries aligned.


Note that sativa paper used diamond cutoff of e-value cut-off of 1 × 10−5 for swissprot. Also I am curious about the proteins with multiple predictions. I assume I want to only count each one a single time? Do they remove duplicates??




DAS_Tool-1.1.2/package/DASTool/R/
DER_Plot.R