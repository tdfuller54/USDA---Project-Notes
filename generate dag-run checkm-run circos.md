/OLD/project/rumen_longread_metagenome_assembly/assemblies/sheep/sheep_
sample2_metaflye/assembly.fasta


/OLD/project/rumen_longread_metagenome_assembly/sheep_poop/illumina_wgs/

snakemake --snakefile /KEEP/rumen_longread_metagenome_assembly/mgatlas_fuller/lib/python3.6/site-packages/atlas/Snakefile --configfile /lustre/project/rumen_longread_metagenome_assembly/tfuller/illumina_atlas_run_3/config.yaml --profile cluster --
jobs 50 --dag | dot -Tpdf > dag.pdf

snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" --dag | dot -Tpdf > dag.pdf


sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s Snakefile --configfile config.yaml --cluster-config cluster.yaml --cluster "sbatch -N 1 --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -p priority -q msn" --use-conda --jobs 50 --dag | dot -Tpdf > dag.pdf


conda install numpy matplotlib pysam
conda install hmmer prodigal pplacer


mkdir checkm
sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="checkm lineage_wf -t 16 -x fa --tab_table -f checkm/result.tsv DASTool_results_DASTool_bins checkm"

#5/20/22
###Made minor change to CircosPlotter and will adjust minimum plotting length. Realized this was giving cutoffs in the 10-12 million range for most of the chromosomes

```bash

python scripts/pafCircosPlotter.py -p mapped/mapVvillosa_CommonVetch.paf -r fastas/Vvillosa.fa -q fastas/CommonVetch.fa -o circos_50000 -c 10 -m 50000

python scripts/pafCircosPlotter.py -p mapped/mapVvillosa_CommonVetch.paf -r fastas/Vvillosa.fa -q fastas/CommonVetch.fa -o circos_100000 -c 10 -m 100000

python scripts/pafCircosPlotter.py -p mapped/mapVvillosa_CommonVetch.paf -r fastas/Vvillosa.fa -q fastas/CommonVetch.fa -o circos_1000000 -c 10 -m 1000000

python scripts/pafCircosPlotter.py -p mapped/mapVvillosa_CommonVetch.paf -r fastas/Vvillosa.fa -q fastas/CommonVetch.fa -o circos_500000 -c 10 -m 500000

python scripts/pafCircosPlotter.py -p mapped/mapVvillosa_CommonVetch.paf -r fastas/Vvillosa.fa -q fastas/CommonVetch.fa -o circos_250000 -c 10 -m 250000