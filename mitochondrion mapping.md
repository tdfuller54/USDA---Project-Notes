#6/28/22
###Run minimap to map mitochondrion sequence of vsativa to vvillosa.

####Run samtools faidx to extract mitochondrion sequence from vsativa assembly

```bash
module load samtools

in Themis-ASM directory

samtools faidx fastas/CommonVetch.fa Mitochondrion -o cv_mitochondrion.fa
```

#### load dotplotly environment and run minimap using different -x parameters to compare all output

```bash

sbatch -N 1 -n 8 --mem=350000 -p priority -q msn --wrap="minimap2 -t 8 Reformatted_Vetch.fasta cv_mitochondrion.fa > mito/mapMitochondrion_default.paf 2> mito/mapMitochondrion_default.log"

job id 7796563

sbatch -N 1 -n 8 --mem=350000 -p priority -q msn --wrap="minimap2 -x map-hifi -t 8 Reformatted_Vetch.fasta cv_mitochondrion.fa > mito/mapMitochondrion_hifi.paf 2> mito/mapMitochondrion_hifi.log"

job id 7796564

sbatch -N 1 -n 8 --mem=350000 -p priority -q msn --wrap="minimap2 -x map-pb -t 8 Reformatted_Vetch.fasta cv_mitochondrion.fa > mito/mapMitochondrion_pb.paf 2> mito/mapMitochondrion_pb.log"

job id 7796565

sbatch -N 1 -n 8 --mem=350000 -p priority -q msn --wrap="minimap2 -x asm10 -t 8 Reformatted_Vetch.fasta cv_mitochondrion.fa > mito/mapMitochondrion_asm10.paf 2> mito/mapMitochondrion_asm10.log"

job id 7796566
```

  * The Python version is: Python3.8 from "/software/7/apps/miniconda/4.10.1/bin/python"
  * The NumPy version is: "1.21.4"

base is 3.7.. does it need 3.8???

in lib/python3.7/site-packages numpy says numpy-1.21.5???



