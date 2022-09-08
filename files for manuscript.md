-zipped folder
-FRC text files output
-lumpy text output
-busco long and short output and intermediate files?
-karyotype and tick and links circos files
-Final assembly
-Original assembly??
-short and long reads
-rna seq reads
-protein coding sequences for any analyses
-protein coding annotation file gff
-repeat annotation gff
-raw images for subfigures
-any multiple sequence alignment?

-Readme file: This file should contain information about the files (to be) hosted in a dataset, including any file naming convention used and the directory structure of any compressed archive (.tar) files. For more general information about Readme Files, the following links may be useful: http://en.wikipedia.org/wiki/README and http://www.wikihow.com/Write-a-Read-Me.



###8/4/22
####Need to run r script to plot merqury output in pdf format

####Also run busco plot script for pdf of  busco plot


```bash

conda activate /project/forage_assemblies/tfuller/Themis-ASM/.snakemake/conda/71004de4/

in Themi-ASM directory

sbatch -N 1 -n 8 --mem=200000 -p priority -q msn --wrap="Rscript /software/7/apps/merqury/1.0/plot/plot_spectra_cn.R -f merqury/Vvillosa/Vvillosa.spectra-asm.hist -o Vvillosa.spectra-asm -z merqury/Vvillosa/Vvillosa.dist_only.hist --pdf"

job id 7880805

worked

conda deactivate

conda activate /project/forage_assemblies/tfuller/Themis-ASM/.snakemake/conda/f2e83543/

sbatch -N 1 -n 8 --mem=200000 -p priority -q msn --wrap="python scripts/plotBusco.py -b busco/CommonVetch/busco_summary.txt -b busco/Vvillosa/busco_summary.txt -a V.sativa -a Vvill1.0 -o Sativa_Villosa2.pdf 2> busco_pdf.log"

job id 7880848

worked

sbatch -N 1 -n 8 --mem=200000 -p priority -q msn --wrap="python scripts/plotBusco.py -b busco/CommonVetch/busco_summary.txt -b busco/Vvillosa/busco_summary.txt -a 'V. sativa' -a Vvill1.0 -o Sativa_Villosa2.pdf 2> busco_pdf2.log"

job id 7880850

worked
```

TO DO:
-combine nr and swisspro condensed excel sheets


-Create table of plant species and descriptions


-Run short read data from Vsativa paper against our assemblies

-Organize all the necessary supplemental data.

-start table of software versions used

DONE:

-Insert protein annotation table and write up summary similar to Vsativa description - XXX

-busco plot can be saved as a pdf if I can edit the legends in illustrator - XXX

-FIRST TRY TO JUST MODIFY IN ILLUSTRATOR.... try to copy Vvillosa.fa to Vvill1.0.fa and run and plot merqury. Hopefully that changes the name in the plot legend. - XXX

-Find shortened Vvill file with just 7 scaffolds and run the circos plotter script to plot against just those. - XXX