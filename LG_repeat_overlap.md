#7/6/22
###Generate bed files from LG_probe.fa files

used python to get bed file for each group of probes. edited python script to that got .txt files for faidx input to convert to tab separated output bed files.

```bash

in Themis-ASM directory

bedtools sort -i LG1.bed > LG1_sorted.bed for each LG1-7.



bedtools intersect -wa -wb -a /90daydata/forage_assemblies/analysis/gene_phylogeny/ReformattedV.repeats.sort.merge.bed -b LG1_sorted.bed -sorted > rptlg_overlap/LG1_overlaps.bed for each LG1-7
```