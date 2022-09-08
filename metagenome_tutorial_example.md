## Rerunning CLR3

Context: one of our metagenome samples was prematurely truncated during assembly. The log file showed discrepancies in the length that were not backed up by the reduction in sequence dataset size. I had to rerun the assembly and analysis prior to publication. 

#### The assembly with metaFlye

> Ceres: /lustre/project/rumen_longread_metagenome_assembly/assemblies/sheep/

```bash
# First, backing up previous data
sbatch -N 1 -n 1 -p priority -q msn --mem=5000 --wrap="cp -r sheep_clr3 sheep_clr3_backup"

module load miniconda/3.6 minimap2
source activate /KEEP/rumen_longread_metagenome_assembly/flye

sbatch --nodes=1 --mem=1000000 --ntasks-per-node=70 -p priority-mem -q msn-mem flye -g 1.0g --pacbio-raw ../../sheep_poop/m54033_180919_161442_clr.3.fastq.gz ../../sheep_poop/m54033_180921_182853_clr.3.fastq.gz ../../sheep_poop/m54033_181128_171116_clr.3.fastq.gz ../../sheep_poop/m54033_181130_213801_clr.3.fastq.gz ../../sheep_poop/m54033_181203_205641_clr.3.fastq.gz ../../sheep_poop/m54033_181204_171640_clr.3.fastq.gz ../../sheep_poop/m54033_181217_171601_clr.3.fastq.gz ../../sheep_poop/m54033_181219_095558_clr.3.fastq.gz ../../sheep_poop/m54033_181220_061614_clr.3.fastq.gz ../../sheep_poop/m54033_190206_141429_clr.3.fastq.gz ../../sheep_poop/m54033_190207_103359_clr.3.fastq.gz ../../sheep_poop/m54337_181123_182744_clr.3.fastq.gz ../../sheep_poop/m54337_181124_144902_clr.3.fastq.gz ../../sheep_poop/m54337_181125_110904_clr.3.fastq.gz ../../sheep_poop/m54337_181126_072918_clr.3.fastq.gz ../../sheep_poop/m54337_181210_204959_clr.3.fastq.gz ../../sheep_poop/m54337_181212_153618_clr.3.fastq.gz ../../sheep_poop/m54337_181214_195839_clr.3.fastq.gz ../../sheep_poop/m54337_181215_161949_clr.3.fastq.gz ../../sheep_poop/m54337_181217_200214_clr.3.fastq.gz ../../sheep_poop/m54337_181218_162309_clr.3.fastq.gz ../../sheep_poop/m54337_181219_124328_clr.3.fastq.gz ../../sheep_poop/m54337_181220_090342_clr.3.fastq.gz ../../sheep_poop/m54337U_200203_184822_clr.3.fastq.gz ../../sheep_poop/m54337U_200211_145859_clr.3.fastq.gz ../../sheep_poop/m54337U_200213_024013_clr.3.fastq.gz ../../sheep_poop/m54337U_200214_212611_clr.3.fastq.gz ../../sheep_poop/m54337U_200220_195233_clr.3.fastq.gz ../../sheep_poop/m54337U_200222_075656_clr.3.fastq.gz ../../sheep_poop/m54337U_200223_200916_clr.3.fastq.gz ../../sheep_poop/m54337U_200227_201603_clr.3.fastq.gz -o sheep_clr3 --meta -t 70 --resume-from consensus

cp sheep_clr3/assembly.fasta sheep_clr3/asm1/sheep_clr3.fasta

```

#### Pilon correction

Assemblies using high error rate long-reads need to be corrected with low error short reads prior to use. Otherwise, [the content of INDELs is too high.](https://www.nature.com/articles/s41587-018-0004-z?platform=hootsuite)

```bash
conda activate /KEEP/rumen_longread_metagenome_assembly/python3/
cd sheep_clr3/

sbatch --nodes=1 --mem=10000 --ntasks-per-node=1 -p priority -q msn snakemake -s ~/python_toolchain/snakeMake/pilonCorrection/Snakefile --cluster "sbatch --nodes=1 --ntasks-per-node={threads} --mem=33000 -p priority -q msn -o logs/{rule}.stdout" --latency-wait 40 --jobs 50

for i in pilon_round1/*.fasta; do echo $i; perl -ne 'if($_ =~ /^>/){chomp; $_ =~ s/_pilon//; $_ .= "\n";} print $_;' < $i >> sheep_clr3_pilon1.fasta; done

sbatch -N 1 -n 2 --mem=15000 -p priority -q msn --wrap="bwa index sheep_clr3_pilon1.fasta"

sbatch -N 1 -n 70 --mem=300000 --dependency=afterok:5653656 -p priority -q msn --wrap="bwa mem -t 34 sheep_clr3_pilon1.fasta /lustre/project/rumen_longread_metagenome_assembly/sheep_poop/illumina_wgs/LIB101996_R1.fastq.gz /lustre/project/rumen_longread_metagenome_assembly/sheep_poop/illumina_wgs/LIB101996_R2.fastq.gz | samtools sort -T sheep_pilon2 -@ 34 -o sheep_clr3_pilon1.bam -"

sbatch -N 1 -n 2 --mem=15000 -p priority -q msn --dependency=afterok:5653661 --wrap="samtools index sheep_clr3_pilon1.bam; python3 ~/python_toolchain/sequenceData/slurmPilonCorrectionPipeline.py -f sheep_clr3_pilon1.bam -g sheep_clr3_pilon1.fasta -o sheep_clr3_pilon2 -p priority -q msn -e 30000 -m"
```


## Analysis on fixed CLR3 assembly

I had to create a whole manuscript's worth of figures and tables, so I had to run through a workflow to get it all done within a reasonable amount of time. I got it done in a week. How did I do it? Snakemake workflow design and automation!

First, I created a new directory to keep things separate from the original project and to allow the files to generate in a safe location without overwriting previous contents.

> Ceres: /lustre/project/forage_assemblies/sheep_project/complete_flye/clr3_rerun

```bash
cp -r ../assembly/ ./assembly
cp -r ../mapping/ ./mapping
cp -r ../binning ./binning

# Note: only works with module load python_3/3.6.6
sbatch -N 1 -n 2 --mem=10000 -p priority -q msn snakemake -s ~/python_toolchain/snakeMake/hifiMAGManuscript/Snakefile --cluster-config ~/python_toolchain/snakeMake/hifiMAGManuscript/cluster.json --cluster "sbatch -N 1 --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -p priority -q msn -o {cluster.stdout}" -p --use-conda --jobs 250 --verbose --latency-wait 40
```

The above was the snakemake workflow! We will look at the graph later, but this is how you do it. 

---

### Supplementary analysis

Some snakemake rules failed or were too difficult to try to automate in time. These were run manually during the running of the pipeline.

Next up, we have to calculate contig-level summary statistics including assembly lengths and contamination/completeness estimates.

```bash
for i in clr1.contigs clr2.contigs clr3.contigs flye4.contigs; do echo $i; mkdir assembly/$i; perl -e '%data; chomp(@ARGV); open(IN, "< $ARGV[0]"); while(<IN>){chomp; @s = split(/\t/); $data{$s[0]} = $s[1];} close IN; $count = 0; foreach $k (sort {$data{$b} <=> $data{$a}} keys(%data)){system("samtools faidx assembly/$ARGV[1].fa $k > assembly/$ARGV[1]/$k.fasta"); $count++; if($count >= 1000){last;}}' assembly/$i.fa.fai $i; done

for i in clr1 clr2 clr3 flye4; do echo $i; sbatch -N 1 -n 30 --mem=150000 -p priority -q msn --wrap="checkm lineage_wf -f tables/$i.contigs.contigs.checkm.txt -t 30 -x fasta {output.directory}"; done

### NOTE: Redo ridgeline plots and distributions after removing bins with > 5% contamination

```

#### Taxonomic classification

I generated bins from the snakemake workflow, but it's time we attempted to assign them to distinct taxa using GTDB-TK.

```bash
### GTDB-TK recalculation
mkdir gtdbtk_bins
for i in clr1 clr2 clr3 flye4; do echo $i; mkdir gtdbtk_bins/${i}.contigs; sbatch ../create_bins.pl binning/DASTool/${i}.contigs.full_bin3c.eval binning/DASTool/${i}.contigs.full_cluster_attribution.tsv assembly/${i}.contigs.fa gtdbtk_bins/${i}.contigs; done

for i in gtdbtk_bins/clr3.contigs/*.fna; do echo -n -e "$i\t"; samtools faidx $i; perl -e '$c = 0; while(<>){chomp; @s = split(/\t/); $c += $s[1];} print "$c\n";' < $i.fai; done > gtdbtk_bins/clr3.contigs.binsizes.tab
perl -lane '@bsegs = split(/[\/\.]/, $F[0]); print "$bsegs[-2]\t$F[1]";' < gtdbtk_bins/clr3.contigs.binsizes.tab > gtdbtk_bins/clr3.contigs.binsizes.fmt.tab


conda activate /KEEP/rumen_longread_metagenome_assembly/gtdbtk

mkdir gtdbtk_output
for i in clr3; do mkdir gtdbtk_output/${i}.contigs; sbatch -N 1 -n 30 --mem=700000 -p priority-mem -q msn-mem --wrap="gtdbtk classify_wf --genome_dir gtdbtk_bins/${i}.contigs --out_dir gtdbtk_output/${i}.contigs --cpus 30 --pplacer_cpus 1"; done

cat gtdbtk_output/clr3.contigs/gtdbtk.bac120.summary.tsv gtdbtk_output/clr3.contigs/gtdbtk.ar122.summary.tsv | perl -ne 'chomp; @F = split(/\t/); if($F[0] =~ /^user_genome/){next;} print "$F[0]\t$F[1]\n";' > gtdbtk_output/clr3.contigs/clr3.concat_taxonomy.tab

for i in clr3; do echo $i; perl -ne 'chomp; @F = split(/\t/); $F[1] =~ s/\s/_/g; print "$F[1]\n";' < gtdbtk_output/${i}.contigs/${i}.concat_taxonomy.tab > gtdbtk_output/${i}.contigs/${i}.tax.list; done

### MASH recalculation
for i in clr3; do echo $i; sbatch ../mash_sketch_bins.pl gtdbtk_bins/${i}.contigs $i gtdbtk_bins/${i}_k21_s100000_combined; done

cp ../gtdbtk_bins/flye4_k21_s100000_combined.msh ./gtdbtk_bins/
for i in clr3; do echo $i; sbatch -N 1 -n 2 --mem=35000 -p priority -q msn --dependency=afterok:5661415 --wrap="~/rumen_longread_metagenome_assembly/binaries/mash-Linux64-v2.0/mash dist -v 0.1 gtdbtk_bins/flye4_k21_s100000_combined.msh gtdbtk_bins/${i}_k21_s100000_combined.msh > gtdbtk_bins/flye4_${i}_distance.tab"; done

for i in clr1 clr2; do echo $i; cp ../gtdbtk_bins/flye4_${i}_* ./gtdbtk_bins/; cp ../gtdbtk_bins/${i}_*.msh ./gtdbtk_bins/; done

for i in clr3; do echo $i; perl -MFile::Basename -lane 'if($F[2] < 0.1){$s = basename($F[0]); $r = basename($F[1]); $s =~ s/\..+$//; $r =~ s/\..+$//; print "$s\t$r\t$F[2]";}' < gtdbtk_bins/flye4_${i}_distance.tab > gtdbtk_bins/flye4_${i}_filtreformat_distance.tab; done

for i in `ls gtdbtk_bins/clr3.contigs/*.fna`; do name=`basename $i`; echo $name; done > gtdbtk_bins/clr3.scaffolds.list
```

The following is a method to combine mash level distances between bins and their taxonomic affiliations.

```bash
conda activate /KEEP/rumen_longread_metagenome_assembly/seaborn
sbatch -N 1 -n 2 -p priority -q msn --mem=45000 --wrap="python3 ~/python_toolchain/metagenomics/mashSparseToSortMatrix.py -r ../b3c_flye4_dastool/flye4.scaffolds.list -q gtdbtk_bins/clr3.scaffolds.list -d gtdbtk_bins/flye4_clr3_distance.tab -o gtdbtk_bins/flye4_clr3_distance.matrix"


for i in assembly/clr3.contigs.fa; do echo $i; sbatch -N 1 -n 70 -p priority -q msn --mem=155000 -t 3-0 --wrap="minimap2 -ax asm20 -t 35 -R '@RG\tID:CCS\tSM:CCS' $i /lustre/project/rumen_longread_metagenome_assembly/sheep_poop/sheep_poop_sequel1_CCS.fasta.gz /lustre/project/rumen_longread_metagenome_assembly/sheep_poop/sheep_poop_sequelII_CCS.fasta.gz | samtools sort -@ 35 -T $i.tmp -o $i.ccs.bam -"; done

for i in clr3; do echo $i; perl -e 'use File::Basename; while(<>){chomp; @s = split(/\t/); $r = basename($s[0]); $t = basename($s[1]); $r =~ s/\.fna//; $t =~ s/\.fna//; if($s[2] < 0.1){print "$r\t$t\t$s[2]\n";}}' < gtdbtk_bins/flye4_${i}_distance.tab > gtdbtk_bins/flye4_${i}_associations.tab; done

for i in clr3; do echo $i; perl combine_bin_tax_and_pairing.pl gtdbtk_output/flye4.contigs/gtdbtk.bac120.summary.tsv gtdbtk_output/flye4.contigs/gtdbtk.ar122.summary.tsv gtdbtk_output/${i}.contigs/gtdbtk.bac120.summary.tsv gtdbtk_output/${i}.contigs/gtdbtk.ar122.summary.tsv gtdbtk_bins/flye4_${i}_associations.tab gtdbtk_output/flye4_${i}_assoc_tax.tab; done
```

The above creates an organized, centralized table that shows comparisons among assemblies with their taxonomic affiliations! Very handy for finding associations between different samples in a qualitative way.

#### Viral association analysis

Using lists of identified viral contigs, we can attempt to find hosts for them using long-read overlaps and Hi-C sequence links. This is the pipeline I created to try to find these associations.

```bash
# viruses
source activate /KEEP/rumen_longread_metagenome_assembly/seaborn/
mkdir viruses
for i in clr3; do echo $i; perl -ne '@s = split(/\t/); if($s[9] eq "Viruses"){print "$s[0]\t$s[1]\n";}' < blobtools/table.${i}.contigs.blobDB.table.txt > assembly/${i}.viral.contigs.list; done

for i in clr3; do echo $i; sbatch --nodes=1 --mem=30000 --ntasks-per-node=4 -p priority -q msn -J ${i}_virus --wrap="python3 ~/rumen_longread_metagenome_assembly/binaries/RumenLongReadASM/viralAssociationPipeline.py -a assembly/${i}.contigs.fa -b blobtools/table.${i}.contigs.blobDB.table.txt -i /lustre/project/forage_assemblies/sheep_project/complete_flye/clr3_rerun/mapping/${i}.contigs/hic/hic_Sau3AI.bam -v assembly/${i}.viral.contigs.list -l /lustre/project/rumen_longread_metagenome_assembly/sheep_poop/sheep_poop_sequelII_CCS.fasta -m minimap2 -o viruses/${i}.contigs.vassoc"; done

perl -lane 'if($F[0] eq 'VirusCtg'){next;}else{print "$F[1]\t$F[0]\t$F[3]";}' < viruses/clr3.contigs.vassoc.final.tab > viruses/clr3.contigs.vassoc.assoc.tab

# Master table
perl -ne 'chomp; @F = split(/\t/); if($_ =~ /^##/){next;} for($x = 0; $x < scalar(@F); $x++){$F[$x] =~ s/\s+/_/g;} print join("\t", @F[0..2, 4..9, 12,15,18,21,24]) . "\n";' <blobtools/table.clr3.contigs.blobDB.table.txt > clr3.mastertable.template.tab
python3 ~/python_toolchain/metagenomics/addJSONColumnsToTable.py -j clr3_data_files_03_2021.json -o clr3_master_table_03_2021.tab -t clr3.mastertable.template.tab
```