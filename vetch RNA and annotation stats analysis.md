#4/18/22
###Need to go over results from RNAQuast and see if the mapping files were stored or removed.

###If removed will need to look to see if the files can be saved with another run or actually run GMAP

####Need to work on annotation of predicted proteins. Start with what was done. What is this file exactly and how was it generated? Next use the databases from the paper to get protein predictions.

####some rudimentary annotation prediction with medicago annotation data.

gene/transcript annotations location: /project/forage_assemblies/assemblies/vetch_transcripts/HV_RNA_Seq_Annotation.xlsx

1. NCBI NR using DIAMOND e value 1x10^-5
2. SWISSPROT using DIAMOND e value 1x10^-5
3. interpro database and GO terms using interproscan
4. eggnog and KEGG and KO using eggnogmapper


####Based on output of rnaQUAST will rerun after modifying rnaQUAST.py to not remove the tmp directory. This should keep the gmap index and alignment files.

```bash
srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

module load python_3/3.6.6 miniconda/3.6 trinityrnaseq/2.11.0 blast+/2.11.0

>in /project/forage_assemblies/tfuller/

mkdir rquast2
cd rquast2/

(transcripts symlink)
ln -s /project/forage_assemblies/assemblies/vetch_transcripts/sahv.transcripts.fasta sahv.transcripts.fasta

(assembly symlink)
ln -s /project/forage_assemblies/assemblies/legume_comparison/ReformatedV.fa ReformatedV.fa

-get trinity summary stats

TrinityStats.pl sahv.transcripts.fasta > trinitystats.txt


conda activate /project/rumen_longread_metagenome_assembly/environments/rnaquast

pip3 install matplotlib
--this actually says requirements satisfied so I think its installed. will do other 2 to be safe

pip3 install gffutils

pip3 install joblib

>Need to edit rnaQUAST.py to comment out the code removing the tmp directory before executing to save the gmap index and alignment files

cd /project/rumen_longread_metagenome_assembly/environments/rnaquast/bin/

nano rnaQUAST.py

>commented out and saved. Now go back to folder and execute

cd /project/forage_assemblies/tfuller/rquast2

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="python /project/rumen_longread_metagenome_assembly/environments/rnaquast/bin/rnaQUAST.py \
--transcripts /project/forage_assemblies/tfuller/rquast2/sahv.transcripts.fasta \
--reference /project/forage_assemblies/tfuller/rquast2/ReformatedV.fa \
--output_dir /project/forage_assemblies/tfuller/rquast2/Vvillosa_rnaQUAST_results/"

job id 7683552
```

####For diamond search I first need to generate the amino acid sequence of the transcripts using TransDecoder

```bash
module load transdecoder

in /project/forage_assemblies/tfuller/

mkdir transdecoder

in /project/forage_assemblies/tfuller/transdecoder

ln -s /project/forage_assemblies/assemblies/vetch_transcripts/sahv.transcripts.fasta sahv.transcripts.fasta


sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="TransDecoder.LongOrfs -t sahv.transcripts.fasta"

job id 7683652

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="TransDecoder.Predict -t sahv.transcripts.fasta"

job id 7683671
```

####Next need to download ncbi nr database

```bash

in /project/forage_assemblies/tfuller/

mkdir nrdb

in /project/forage_assemblies/tfuller/nrdb

wget https://ftp.ncbi.nlm.nih.gov/blast/db/FASTA/nr.gz


>Taking way too long with gunzip so found pigz documentation to speed up

pigz -d nr.gz
mv nr nr.fa

in /project/forage_assemblies/tfuller/

mkdir swissp_db

in /project/forage_assemblies/tfuller/swissp_db

wget ftp://ftp.uniprot.org/pub/databases/uniprot/current_release/knowledgebase/complete/uniprot_sprot.fasta.gz

gunzip uniprot_sprot.fasta.gz
```

####NCBI-nr and swiss-prot databases are downloaded. Now ready to make the diamond databases from these and run diamond to compare transdecoder output to both databases.

##4/20/22
####Build diamond databases to run diamond search of transdecoder output against nr and swissprot

```bash
srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

module load python_3/3.6.6 miniconda/3.6

conda create -p /project/rumen_longread_metagenome_assembly/environments/diamond/ -c bioconda -c conda-forge mamba

conda activate /project/rumen_longread_metagenome_assembly/environments/diamond/

mamba install -c bioconda diamond

in /project/forage_assemblies/tfuller/nrdb

sbatch -N 1 -n 4 --mem=200000 -p priority -q msn --wrap="diamond makedb --in nr.fa -d nr"

job id 7684360

in /project/forage_assemblies/tfuller/swissp_db

sbatch -N 1 -n 4 --mem=200000 -p priority -q msn --wrap="diamond makedb --in uniprot_sprot.fasta -d swissprot"

job id 7684361

>This is done so start the database search

sbatch -N 1 -n 8 --mem=200000 -p priority -q msn --wrap="diamond blastp --db swissprot.dmnd --query /project/forage_assemblies/tfuller/transdecoder/sahv.transcripts.fasta.transdecoder.pep -o swissp_anno.tsv --outfmt 6 qseqid sseqid pident length mismatch gaps qstart qend sstart send evalue bitscore stitle --sensitive --threads 8 --top 1"

job id 7684425

COMPLETED SUCCESSFULLY!!
```
###4/21/22
####Start diamond search against built nr database now.

```bash
srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

module load python_3/3.6.6 miniconda/3.6

conda activate /project/rumen_longread_metagenome_assembly/environments/diamond/

in /project/forage_assemblies/tfuller/nrdb

sbatch -N 1 -n 8 --mem=200000 -p priority -q msn --wrap="diamond blastp --db nr.dmnd --query /project/forage_assemblies/tfuller/transdecoder/sahv.transcripts.fasta.transdecoder.pep -o nr_anno.tsv --outfmt 6 qseqid sseqid pident length mismatch gaps qstart qend sstart send evalue bitscore stitle --sensitive --threads 8 --top 1"

job id 7689199
```
###4/21/22
####Reformatting the assembly file to the shortened name and starting themis should only rerun rules for Vvillosa. This should create the fai and paf files needed appropriately and also automatically relabel various graphs.


```bash
srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

in /project/forage_assemblies/tfuller/Themis-ASM

python

test = []
with open('vetch_phase.assembly.fasta', "r") as text:
    for l in text:
        l = l.strip()
        #print(l)
        if l.startswith('>'):
            l = "_".join(l.split("_", 2)[:2])
            test.append(l)
        else:
            test.append(l)

fasta = "\n".join(test)

with open('Reformatted_Vetch.fasta', "w") as text2:
    text2.write(fasta)

NOTICED THAT UNSCAFFOLDED CONTIGS REMAINED IN THIS ONE AND SOME OF THESE WILL HAVE SAME NAME AFTER SHORTENING AS A RESULT EMAILED DEREK BUT WILL ATTEMPT TO START A THEMIS RUN ANYWAY

conda activate /KEEP/rumen_longread_metagenome_assembly/themis_fuller/


sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="python themisASM.py  \
	-a /project/forage_assemblies/tfuller/Themis-ASM/Reformatted_Vetch.fasta  \
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

job id 7689592
```
-Notes: Busco does not like the name duplication of the unscaffolded contigs. I think I will need to rename those in some way to get it to work but not sure how.

-Run_Merqury also returned error because the mv command will not overwrite an existing file. Can be fixed for now by deleting files to overwrite but may want to edit code to allow the overwrite to occur if anything is rerun in downstream application.

For whatever reason the rerun did not trigger align_reads and merge_bams rules to get re-executed. This actually let freebayes rule start earlier than it should have causing an error because the bwa_index rule had not finished.. Honestly not sure how this happened because output of bwa_index is input for align_reads and align_reads output is input for merge_bams and merge_bams output is input for freebayes. Found this error completely by chance.

-Will need to remove mapped/Vvillosa directory to get those rules to run I am guessing


###4/22/22
-Will rerun themis with original assembly file since stats from everything is what we want. Really we only want to run circos again. Need to figure out why only one assemblies contigs were being plotted.

--Quick test run of rnaquast. Not sure where database_metrics are being lost?

```bash
sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="python /project/rumen_longread_metagenome_assembly/environments/rnaquast/bin/rnaQUAST.py \
--test"

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="python /project/rumen_longread_metagenome_assembly/environments/rnaquast/bin/rnaQUAST.py \
--transcripts /project/forage_assemblies/tfuller/rnaquast/sahv.transcripts.fasta \
--reference /project/forage_assemblies/tfuller/rnaquast/ReformatedV.fa \
--labels Vvillosa --gene_mark \
--output_dir /project/forage_assemblies/tfuller/rnaquast/genemark_rnaQUAST_results/"

job id 7691199

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="python /project/rumen_longread_metagenome_assembly/environments/rnaquast/bin/rnaQUAST.py \
--transcripts /project/forage_assemblies/tfuller/rquast2/sahv.transcripts.fasta \
--reference /project/forage_assemblies/tfuller/rquast2/ReformatedV.fa \
--labels Vvillosa \
--output_dir /project/forage_assemblies/tfuller/rquast2/run2_rnaQUAST_results/"

job id 7691200

in minimap
sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="minimap2 -ax splice /project/forage_assemblies/tfuller/rnaquast/ReformatedV.fa /project/forage_assemblies/tfuller/rnaquast/sahv.transcripts.fasta > reformated_aln.sam"

job id 7691218

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="minimap2 -cx splice /project/forage_assemblies/tfuller/rnaquast/ReformatedV.fa /project/forage_assemblies/tfuller/rnaquast/sahv.transcripts.fasta > reformated_aln.paf"

job id 7691221

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="minimap2 -ax splice /project/forage_assemblies/tfuller/Themis-ASM/vetch_phase.assembly.fasta /project/forage_assemblies/tfuller/rnaquast/sahv.transcripts.fasta > vetch_aln.sam"

job id 7691222

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="minimap2 -cx splice /project/forage_assemblies/tfuller/Themis-ASM/vetch_phase.assembly.fasta /project/forage_assemblies/tfuller/rnaquast/sahv.transcripts.fasta > vetch_aln.paf"

job id 7691223


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

Said weird error about no environment. This mentioned the /KEEP/ folder. trying to remove conda and conda-archive folders in .snakemake to see if this helps?

did this in sbatch because it is taking forever. job id 7691259. Hopefully this works
```
--NOTE new rnaquast results are in rnaquast folder because of typo in job submission. I think I will need a GTF file though to get the database metrics to run??

#4/25/22
-Run rnaquast in same output folder since wanted to test theory of first run generating files needed for second run.

-Run rnaquast with unedited assembly file to try and align more predicted transcripts in rq3 folder

```bash
module load python_3/3.6.6 miniconda/3.6

conda activate /project/rumen_longread_metagenome_assembly/environments/rnaquast

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="python /project/rumen_longread_metagenome_assembly/environments/rnaquast/bin/rnaQUAST.py \
--transcripts /project/forage_assemblies/tfuller/rquast2/sahv.transcripts.fasta \
--reference /project/forage_assemblies/tfuller/rquast2/ReformatedV.fa \
--labels Vvillosa \
--output_dir /project/forage_assemblies/tfuller/rquast2/run2_rnaQUAST_results/"

job id 7692039

--BELOW IS ALIGNING TO FULL ASSEMBLY!

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="python /project/rumen_longread_metagenome_assembly/environments/rnaquast/bin/rnaQUAST.py \
--transcripts /project/forage_assemblies/tfuller/rquast2/sahv.transcripts.fasta \
--reference /project/forage_assemblies/tfuller/Themis-ASM/vetch_phase.assembly.fasta \
--labels Vvillosa \
--output_dir /project/forage_assemblies/tfuller/rq3/vetch_rnaQUAST_results/"

job id 7692134
```

-Start run of Themis again to get back merqury plots in directory

```bash
conda activate /project/rumen_longread_metagenome_assembly/environments/themis_fuller

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

job id 7692158

--SAME ERROR from Friday
found in the themis_fuller/bin folder in the environment that conda and conda-env had listed KEEP directories. Think this was trying to search for the original environment storage place to create new conda environments for the run. Changed with nano and now trying again

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

job id 7692197

gave a lot of errors. tried removing input for some and also found another reference to the KEEP folder being called. Fixed and will restart
```

##4/26/22
####Run GMAP with gff3 output to map transcripts to genome. This can then be converted to gtf format using AGAT from bioconda

```bash
srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

conda activate /project/rumen_longread_metagenome_assembly/environments/rnaquast



sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="gmap -D /project/forage_assemblies/tfuller/rq3/vetch_rnaQUAST_results/tmp -d vetch_phase.assembly.upper /project/forage_assemblies/tfuller/rquast2/sahv.transcripts.fasta --format=2 -t 16 -O > /project/forage_assemblies/tfuller/rq3/Vvillosa.gff"

job id 7693025

APPEARS TO BE WORKING

NEED to convert to gtf

mamba install agat

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="agat_convert_sp_gff2gtf.pl --gff /project/forage_assemblies/tfuller/rq3/Vvillosa.gff -o /project/forage_assemblies/tfuller/rq3/Vvillosa.gtf"

job ID 7693299

```
####Restart Themis again

```bash

conda activate /project/rumen_longread_metagenome_assembly/environments/themis_fuller

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

job ID 7693051

```
###Really tired of errors just from environment. will create new environment

```bash
module load python_3/3.6.6 miniconda/3.6

conda create -p /project/rumen_longread_metagenome_assembly/environments/themis_fuller2 -c bioconda -y -c conda-forge mamba

conda activate /project/rumen_longread_metagenome_assembly/environments/themis_fuller2

mamba install -c bioconda merqury samtools bwa snakemake

pip3 install pysam

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

job ID 7693148


WHOLE BUNCH OF STUFF RERUNNING. NEED BELOW NOTES FOR WHEN MINIMAP WITH LENTIL AND GRASSPEA FAIL!!!


>in forage_assemblies directory


cp mapLentil_GrassPea_Lkmer.paf /project/forage_assemblies/tfuller/Themis-ASM/mapped/

>in mapped directory

rm mapLentil_GrassPea.paf
mv mapLentil_GrassPea_Lkmer.paf mapLentil_GrassPea.paf


>in Themis directory

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /project/forage_assemblies/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /project/forage_assemblies/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" --touch

job id 7257460

>Test dryrun to see what still needs to be completed


sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /project/forage_assemblies/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /project/forage_assemblies/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -t 2-0 --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" --dryrun
```

Once gff2gtf is done run rnaquast using gtf file as input option and hopefully this will create database metrics file and everything else!!!

Not sure why jobs are "failed to launch requeued held" but need to let things run that can and then copy over mapLentil_GrassPea_Lkmer.paf file and use touch command to incorporate all files in timed order so won't rerun everything again. If things are still queued tomorrow maybe look into cancelling and using touch. Then if things are still getting held up try to figure out why!!!

		   7693206  priority snakejob tyson.fu PD       0:00      1 (launch failed requeued held)
           7693201  priority snakejob tyson.fu PD       0:00      1 (launch failed requeued held)
           7693200  priority snakejob tyson.fu PD       0:00      1 (launch failed requeued held)
           7693198  priority snakejob tyson.fu PD       0:00      1 (launch failed requeued held)
           7693197  priority snakejob tyson.fu PD       0:00      1 (launch failed requeued held)
           7693196  priority snakejob tyson.fu PD       0:00      1 (launch failed requeued held)
           7693195  priority snakejob tyson.fu PD       0:00      1 (launch failed requeued held)
           7693194  priority snakejob tyson.fu PD       0:00      1 (launch failed requeued held)
           7693193  priority snakejob tyson.fu PD       0:00      1 (launch failed requeued held)
           7693192  priority snakejob tyson.fu PD       0:00      1 (launch failed requeued held)
           7693191  priority snakejob tyson.fu PD       0:00      1 (launch failed requeued held)
           7693190  priority snakejob tyson.fu PD       0:00      1 (launch failed requeued held)
           7693188  priority snakejob tyson.fu PD       0:00      1 (launch failed requeued held)
           7693187  priority snakejob tyson.fu PD       0:00      1 (launch failed requeued held)
           7693186  priority snakejob tyson.fu PD       0:00      1 (launch failed requeued held)
           7693184  priority snakejob tyson.fu PD       0:00      1 (launch failed requeued held)
           7693182  priority snakejob tyson.fu PD       0:00      1 (launch failed requeued held)
           7693180  priority snakejob tyson.fu PD       0:00      1 (launch failed requeued held)
           7693171  priority snakejob tyson.fu PD       0:00      1 (launch failed requeued held)

--Last job submission in themis

[Tue Apr 26 20:50:23 2022]
rule align_reads:
    input: /project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L004_R1_001.fastq.gz, /project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L004_R2_001.fastq.gz, fastas/Pea.fa, fastas/Pea.fa.amb, fastas/Pea.fa.ann, fastas/Pea.fa.bwt, fastas/Pea.fa.pac, fastas/Pea.fa.sa, fastas/Pea.fa.fai
    output: mapped/Pea/lane4.bam
    log: logs/Pea/lane4_bwa.log
    jobid: 126
    wildcards: asm=Pea, sample=lane4
    threads: 8
    resources: mem_mb=67121, disk_mb=67121, tmpdir=/local/bgfs/tyson.fuller/7693148


        bwa mem -R '@RG\tID:lane4\tSM:lane4\tPL:ILLUMINA' -t 8 -M fastas/Pea.fa /project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L004_R1_001.fastq.gz /project/forage_assemblies/tfuller/Themis-ASM/HV30Vetch_S1_L004_R2_001.fastq.gz | samtools sort -o mapped/Pea/lane4.bam  - >> logs/Pea/lane4_bwa.log 2>&1
        samtools index -c mapped/Pea/lane4.bam

Submitted job 126 with external jobid 'Submitted batch job 7693301'.


##4/27/22
####Saw Themis was running and started held jobs. Also saw expected merqury errors. Will wait until remaining jobs finish and use the "touch" command and things above to try and get to run without reexecuting jobs.

####GTF file was generated so now will use the gtf file and run rnaquast to hopefully generate all needed data.

```bash

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="python /project/rumen_longread_metagenome_assembly/environments/rnaquast/bin/rnaQUAST.py \
--transcripts /project/forage_assemblies/tfuller/rquast2/sahv.transcripts.fasta \
--reference /project/forage_assemblies/tfuller/Themis-ASM/vetch_phase.assembly.fasta \
--gtf /project/forage_assemblies/tfuller/rq3/Vvillosa.gtf \
--labels Vvillosa \
--output_dir /project/forage_assemblies/tfuller/rq3/final_rnaQUAST_results/"

job ID 7693580

```

##4/28/22
####Assuming themis finished running transfer any output from busco and merqury that may be relevant to folder on Desktop. Also transfer files from trinity summary and rnaQUAST

```bash
srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

module load python_3/3.6.6 miniconda/3.6

conda activate /project/rumen_longread_metagenome_assembly/environments/themis_fuller2/



```

So rnaquast is done and gave some results but I am very skeptical about the results. Will include what I can and perhaps bring it up with Derek???

FOUND MATH ERROR IN NGX PLOT. MINOR BUT STILL WORTH CORRECTING


##4/29/22
####Just for kicks run rnaQUAST with gff file as input. Completely rebuild the gmap database and the gff file in new folder and run rnaquast in that folder

```bash

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="gmap_build -D /project/forage_assemblies/tfuller/gmap/ -d Vvillosa_genome /project/forage_assemblies/tfuller/Themis-ASM/vetch_phase.assembly.fasta"

job id 7697317

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="gmap -D /project/forage_assemblies/tfuller/gmap/ -d Vvillosa_genome /project/forage_assemblies/tfuller/rquast2/sahv.transcripts.fasta --format=2 -t 16 -O > /project/forage_assemblies/tfuller/gmap/Vvillosa.gff"

job id 7697517

sbatch -N 1 -n 2 --mem=150000 -p priority -q msn --wrap="python /project/rumen_longread_metagenome_assembly/environments/rnaquast/bin/rnaQUAST.py \
--transcripts /project/forage_assemblies/tfuller/rquast2/sahv.transcripts.fasta \
--reference /project/forage_assemblies/tfuller/Themis-ASM/vetch_phase.assembly.fasta \
--gtf /project/forage_assemblies/tfuller/gmap/Vvillosa.gff \
--gmap_index /project/forage_assemblies/tfuller/gmap/Vvillosa_genome \
--labels Vvillosa \
--output_dir /project/forage_assemblies/tfuller/gmap/rnaQUAST_results/"


job id 7701103



JUST WANT TO SEE WHAT -E FLAG DOES HERE

sbatch -N 1 -n 2 --mem=100000 -p priority -q msn --wrap="gmap -D /project/forage_assemblies/tfuller/rq3/vetch_rnaQUAST_results/tmp -d vetch_phase.assembly.upper /project/forage_assemblies/tfuller/rquast2/sahv.transcripts.fasta -E "cdna+introns" --format=2 -t 16 -O > /project/forage_assemblies/tfuller/rq3/Vvillosa2.gff"

job id 7697457


```

Adam Rivers for scinet?? Things on linkedin


amputee clinic
