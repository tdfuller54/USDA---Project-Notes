#8/17/21 Snakemake workflow returned low number of bins from DASTool and really all. After looking at bin summaries it seems that a lot from concoct and a fair amount from metabat2 have high reduncancy. Perhaps because these are long reads??

```
>Make a .tsv with headers
echo -e "contig\tbin" > header.tsv

>Get all contigs from assembly. Pipes grep into cut which includes all characters from the second on (2-)
grep ">" sheep_sample2.fa | cut -c 2- > test.tsv

>Fill second column with "bin."i++ using awk. \t says tab delim.
awk -F "\t" '{$2="bin."++i;}1' OFS="\t" test.tsv > test1.tsv

>use cat to concatenate headers onto file
cat header.tsv test1.tsv > assembly_scaffold2bins.tsv

>This can now serve as a scaffold2bins file with each contig having it's own unique bin for DASTool input
```

#Want to run just contigs through DASTool. Also through checkm? look into checkm and what it takes as input and gives as output to be sure it looks at the contigs and doesn't want bins? First just get DASTool running...

```
>Load modules and activate environment or create one if necessary

module load mpc/1.0.3 gmp/6.1.0 mpfr/3.1.3 gcc/8.1.0 openblas/0.2.15 python_3/3.6.6 miniconda/3.6


conda create -p /KEEP/rumen_longread_metagenome_assembly/DASTool -c bioconda -y -c conda-forge mamba

conda activate /KEEP/rumen_longread_metagenome_assembly/DASTool

conda config --add channels ruby-lang

mamba install -c bioconda das_tool=1.1.2

mamba kept seeming to freeze so used conda. Took awhile but it worked

>Run DASTool


sbatch -N 1 -n 2 --mem=50000 -t 3-0 -p priority -q msn --wrap="DAS_Tool --outputbasename contigs_only/DASTool/results --bins assembly/assembly_scaffolds2bin.tsv  --contigs assembly/sheep_sample2.fa --search_engine diamond --write_bin_evals 1 --create_plots 1 --threads 10 --debug"
```

#Think this run was competing with partial made files. Seemed froze so stopped it. removed output directory. Will recreate output directory and restart run.

```
module load mpc/1.0.3 gmp/6.1.0 mpfr/3.1.3 gcc/8.1.0 openblas/0.2.15 python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/DASTool

mkdir contigs_only
mkdir DASTool

sbatch -N 1 -n 4 --mem=150000 -t 3-0 -p priority -q msn --wrap="DAS_Tool --outputbasename contigs_only/DASTool/results --bins assembly/assembly_scaffolds2bin.tsv  --contigs assembly/sheep_sample2.fa --search_engine diamond --write_bin_evals 1 --create_plots 1 --threads 20 --debug"

job id 6295430
```

#Run checkM on bins from metabat and concoct

```
--in Sheep_2_HiFi_binning/binning/sheep_sample2/metabat2 directory
mkdir checkm_metabat2

sbatch -N 1 -n 2 --mem=100000 -t 3-0 -p priority -q msn --wrap="checkm lineage_wf -t 16 -x fa bins checkm_metabat2"

job id 6295634

--in Sheep_2_HiFi_binning/binning/sheep_sample2/concoct directory
mkdir checkm_concoct

sbatch -N 1 -n 2 --mem=100000 -t 3-0 -p priority -q msn --wrap="checkm lineage_wf -t 16 -x fa bins checkm_concoct"

job id 6295645
```


#10/22/21 - Rerun DASTool on contigs only with the write_bins flag as 1 to generate bins for checkM analysis.

```
--load dependent modules and activate previously made environment
module load mpc/1.0.3 gmp/6.1.0 mpfr/3.1.3 gcc/8.1.0 openblas/0.2.15 python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/DASTool

-- fix weird utf8 error (just in case)
export LC_ALL=en_US.utf8
export LANG=en_US.utf8

--in Sheep_2_HiFi_binning/contigs_only directory
mkdir DASTool_Run2

--in Sheep_2_HiFi_binning directory run
sbatch -N 1 -n 4 --mem=150000 -t 3-0 -p priority -q msn --wrap="DAS_Tool --outputbasename contigs_only/DASTool_Run2/results --bins assembly/assembly_scaffolds2bin.tsv  --contigs assembly/sheep_sample2.fa --search_engine diamond --write_bin_evals 1 --create_plots 1 --write_bins 1 --threads 20 --debug"

job id 6549571
```

Have bins from contigs only run and metabat and concoct run. Now will run these through checkM

#10/23/21 - Run checkM on bins from contigs_only DASTool run and bins DASTool got from concoct and metabat

```

--load dependent modules and activate previously made environment
module load mpc/1.0.3 gmp/6.1.0 mpfr/3.1.3 gcc/8.1.0 openblas/0.2.15 python_3/3.6.6 miniconda/3.6

--activate environment
conda activate /KEEP/rumen_longread_metagenome_assembly/checkm

-- fix weird utf8 error (just in case)
export LC_ALL=en_US.utf8
export LANG=en_US.utf8

--in contigs_only/DASTool_Run2

mkdir checkm

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="checkm lineage_wf -t 16 -x fa results_DASTool_bins checkm"

job ID 6549985


--in binning/sheep_sample2/DASTool/output

mkdir checkm

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="checkm lineage_wf -t 16 -x fa DASTool_results_DASTool_bins checkm"

job ID 6549986
```

-Marker file generated is the .ms file

- checkm qa <marker file> <output folder>
--tab_table (nothing after it) writes in plain format

-f <file name> path/to/output/result.txt or .tsv would likely be fine..  used to write output

-o 1 is wanted for output type


- checkm bin_qa_plot -x fa <output folder> <bin folder> <new output folder to put plots>

#10/28/21 Run checkm again using the -o and -tab_table flags to generate output in the desired format

```
--load dependent modules and activate previously made environment
module load mpc/1.0.3 gmp/6.1.0 mpfr/3.1.3 gcc/8.1.0 openblas/0.2.15 python_3/3.6.6 miniconda/3.6

--activate environment
conda activate /KEEP/rumen_longread_metagenome_assembly/checkm

-- fix weird utf8 error (just in case)
export LC_ALL=en_US.utf8
export LANG=en_US.utf8

--in contigs_only/DASTool_Run2

sbatch -N 1 -n 2 --mem=100000 -t 2-0 -p priority -q msn --wrap="checkm qa -t 16 --out_format 1 --tab_table -f checkm/result.tsv checkm/lineage.ms checkm"

job ID 6558438


--in binning/sheep_sample2/DASTool/output

sbatch -N 1 -n 2 --mem=100000 -t 2-0 -p priority -q msn --wrap="checkm qa -t 16 --out_format 1 --tab_table -f checkm/result.tsv checkm/lineage.ms checkm"

job ID 6558439


--in contigs_only/DASTool_Run2

sbatch -N 1 -n 2 --mem=100000 -t 2-0 -p priority -q msn --wrap="checkm bin_qa_plot -t 16 -x fa checkm results_DASTool_bins checkm/plots"

job ID 6558459


--in binning/sheep_sample2/DASTool/output

sbatch -N 1 -n 2 --mem=100000 -t 2-0 -p priority -q msn --wrap="checkm bin_qa_plot -t 16 -x fa checkm DASTool_results_DASTool_bins checkm/plots"

job ID - did not run this because bin_qa_plot no longer works