#5/23/22
###Generate fai file for Reformatted vetch fasta file. This can then be used to generate a paf file of this against the common vetch assembly for circos plot generation.

```bash
ln -s /project/forage_assemblies/assemblies/legume_comparison/ReformatedV.fa /project/forage_assemblies/tfuller/Themis-ASM/fastas/reformatted_vetch.fa

-shell script contents below

#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=2
#SBATCH --mem=100000
#SBATCH -p priority
#SBATCH -q msn

bwa index fastas/reformatted_vetch.fa 2> logs/reformatted_vetch/indexing.log
samtools faidx fastas/reformatted_vetch.fa 2> logs/reformatted_vetch/indexing.log

-saved as bwa_index.sh

now run

sbatch bwa_index.sh

job id 7746628
```

worked so now try to run minimap

```bash

sbatch -N 1 -n 2 --mem=250000 -p priority -q msn --wrap="minimap2 -x asm10 -t 8 fastas/reformatted_vetch.fa fastas/CommonVetch.fa > mapped/mapReformatted_Common.paf 2> R_Cminimap.log"

job id 7747644
```

###Necessary files are now created. Will run circos scripts to generate plot with correct names for contigs


###Double check with Derek on estimated genome size for Nx plot and also on cutoff for circos plot and if all smaller scaffolds should be plotted?

Note that common vetch has chr1-6 and chloroplast and mitochondrion. Then 10 contigs. The reformatted vetch has 9 "scaffolds" but 2 of these are much smaller. smaller are 3 million each but other 7 are 140 million to 250 million