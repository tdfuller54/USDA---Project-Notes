sbatch -N 1 -n 2 --mem=200000 -p priority -q msn --wrap="zcat HV30Vetch_S1_L001_R1_001.fastq.gz | paste - - - - | cut -f 2 | tr -d '\n' | wc -c
job id 7866306
18742874985

sbatch -N 1 -n 2 --mem=200000 -p priority -q msn --wrap="zcat HV30Vetch_S1_L001_R2_001.fastq.gz | paste - - - - | cut -f 2 | tr -d '\n' | wc -c"
job id 7866326
18735546641

sbatch -N 1 -n 2 --mem=200000 -p priority -q msn --wrap="zcat HV30Vetch_S1_L002_R1_001.fastq.gz | paste - - - - | cut -f 2 | tr -d '\n' | wc -c"
job id 7866327
18551970892


sbatch -N 1 -n 2 --mem=200000 -p priority -q msn --wrap="zcat HV30Vetch_S1_L002_R2_001.fastq.gz | paste - - - - | cut -f 2 | tr -d '\n' | wc -c"
job id 7866346
18544562348

sbatch -N 1 -n 2 --mem=200000 -p priority -q msn --wrap="zcat HV30Vetch_S1_L003_R1_001.fastq.gz | paste - - - - | cut -f 2 | tr -d '\n' | wc -c"
job id 7866347
18400151756

sbatch -N 1 -n 2 --mem=200000 -p priority -q msn --wrap="zcat HV30Vetch_S1_L003_R2_001.fastq.gz | paste - - - - | cut -f 2 | tr -d '\n' | wc -c"
job id 7866348
18395609701

sbatch -N 1 -n 2 --mem=200000 -p priority -q msn --wrap="zcat HV30Vetch_S1_L004_R1_001.fastq.gz | paste - - - - | cut -f 2 | tr -d '\n' | wc -c"
job id 7866349
18222742339


sbatch -N 1 -n 2 --mem=200000 -p priority -q msn --wrap="zcat HV30Vetch_S1_L004_R2_001.fastq.gz | paste - - - - | cut -f 2 | tr -d '\n' | wc -c"
job id 7866350
18218034316

Total 147,811,492,978
