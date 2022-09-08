#8/10/22
####Generate new circos plots both for sativa_villosa and commonbean_cowpea

```bash

-Note that by generating txt files with each fasta I wanted to keep on it's own line I could use these as input to parse and extract the subset into new fasta files with awk

awk -F'>' 'NR==FNR{ids[$0]; next} NF>1{f=($2 in ids)} f' sat_chr.txt Vicia_sativa_reorg.fa > sat_chrs.fa


-Note this is in tfuller/rnaquast because the ReformatedV.fa file has the shortened scaffold names already

awk -F'>' 'NR==FNR{ids[$0]; next} NF>1{f=($2 in ids)} f' vill_scaffold.txt ReformatedV.fa > vill_scaffolds.fa

copied these over to Themis-ASM
```

####Need to generate a new paf file with minimap based on these new fasta files

```bash

conda activate /project/forage_assemblies/tfuller/Themis-ASM/.snakemake/conda/71004de4/

-in Themis-ASM

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="minimap2 -x asm10 -t 8 vill_scaffolds.fa sat_chrs.fa > mapVill_Sat_scaffolds.paf"

job id 7893588
```

####Generate circos plot for CowPea and CommonBean using 11 chromosomes so they are all included since they each have 11.

```bash
conda activate /project/forage_assemblies/tfuller/Themis-ASM/.snakemake/conda/3626987c/

python scripts/pafCircosPlotter.py -p mapped/mapCowPea_CommonBean.paf -r fastas/CowPea.fa -q fastas/CommonBean.fa -o CowPea_CommonBean_circos_100000 -c 11 -m 100000

DONE!
```
####once paf file is complete this can be ran for villosa and sativa as well

```bash

conda activate /project/forage_assemblies/tfuller/Themis-ASM/.snakemake/conda/3626987c/

python scripts/pafCircosPlotter.py -p mapVill_Sat_scaffolds.paf -r vill_scaffolds.fa -q sat_chrs.fa -o vill_sat_circos_100000 -c 10 -m 100000

