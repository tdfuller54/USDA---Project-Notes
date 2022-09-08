##To Do List

Run interproscan and eggnogg on peptide sequences

rerun rnaquast with larger assembly

Pull out merqury images generated and start to put together figures.

start to put relative stats into manuscript in tables. This can be simply written over into table incorporated into microsoft word


#5/5/22
Corrected NGX plot error. Also made edits to depth estimate which should give percent of genome coverage now for specificied threshold. Run again after deleting ngx plots and merged_Depth for Vvillosa.

```bash

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="python themisASM.py  \
	-a /project/forage_assemblies/tfuller/Themis-ASM/vetch_phase.assembly.fasta  \
	-a /project/forage_assemblies/tfuller/Themis-ASM/Lens_culinaris_2.0.fasta  \
	-a /project/forage_assemblies/tfuller/Themis-ASM/Ler.1DRT.fasta \
	-a /project/forage_assemblies/tfuller/Themis-ASM/Vunguiculata_469_v1.0.fa \
	-a /project/forage_assemblies/tfuller/Themis-ASM/Pvulgaris_442_v2.0.fa \
	-a /project/forage_assemblies/tfuller/Themis-ASM/Pisum_sativum_v1a.fa \
	-a /project/forage_assemblies/tfuller/Themis-ASM/MtrunA17r5.0-20161119-ANR.genome.fasta \
	-a /project/forage_assemblies/tfuller/Themis-ASM/ARS_DFRC_1.1.fasta \
	-a /project/forage_assemblies/tfuller/Themis-ASM/Vicia_sativa_reorg.fa \
	-n Vvillosa \
	-n Lentil \
	-n GrassPea \
	-n CowPea \
	-n CommonBean \
	-n Pea \
	-n MedTr \
	-n RedClover \
	-n CommonVetch \
	-b eudicots_odb10 \
	-f /project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L001_R1_001.fastq.gz,/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L001_R2_001.fastq.gz \
	-f /project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L002_R1_001.fastq.gz,/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L002_R2_001.fastq.gz \
	-f /project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L003_R1_001.fastq.gz,/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L003_R2_001.fastq.gz \
	-f /project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L004_R1_001.fastq.gz,/project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L004_R2_001.fastq.gz \
	-s lane1 \
	-s lane2 \
	-s lane3 \
	-s lane4 \
	-j 50 \
	-c 'sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}' \
	-r"

job id 7706001

merged_Depth was necessary for downstream processes so changed it back and running again

sbatch -N 1 -n 2 --mem=25000 -t 2-0 -p priority -q msn snakemake -s /project/forage_assemblies/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /project/forage_assemblies/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" -R samtools_depth

job id 7706124
```

Used depth_estimate as a template to write executable script from command line to get percentage coverage at various read depths. Will attempt to run this

```bash
sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="python Coverage_Percentage.py -b mapped/Vvillosa/merged.bam -t 1 -o coverage_1.tsv"

job id 7706424

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="python Coverage_Percentage.py -b mapped/Vvillosa/merged.bam -t 5 -o coverage_5.tsv"

job id 7706522

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="python Coverage_Percentage.py -b mapped/Vvillosa/merged.bam -t 10 -o coverage_10.tsv"

job id 7706523

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="python Coverage_Percentage.py -b mapped/Vvillosa/merged.bam -t 20 -o coverage_20.tsv"

job id 7706524
```

Run busco_plotting script for just Vvillosa and CommonVetch

```bash
sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="python scripts/plotBusco.py -b busco/Vvillosa/busco_summary.txt -b busco/CommonVetch/busco_summary.txt -a Vvillosa -a CommonVetch -o V_C_buscos.png 2> vc_buscoplot.log"

job id 7726317