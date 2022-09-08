#7/11/22
##Quick run of themis utilizing Lisa's script (modified for me) and data to see if I can get the environments to build and jobs to run successfully

Bio.Alphabet has been removed from Biopython. In many cases, the alphabet can simply be ignored and removed from scripts. In a few cases, you may need to specify the ``molecule_type`` as an annotation on a SeqRecord for your script to work correctly. Please see https://biopython.org/wiki/Alphabet for more information.
There was a problem installing BUSCO or importing one of its dependencies. See the user guide and the GitLab issue board (https://gitlab.com/ezlab/busco/issues) if you need further assistance.

doing test run on Lisa's data in Themis070122b after removing files. This has environments created already so this will see if at least some jobs finish...

--Still getting busco error about bio alphabet. Definitely need to figure out how to fix

####Start by making a directory within her folder to start a run and then copy over the shell script and run in testenv environment first

```bash

-in /project/forage_assemblies/analysis/LKsynteny

mkdir debug_tf

cp runthemis_tf.sh debug_tf/runthemis_tf.sh

-in debug_tf

git clone --recursive https://github.com/tdfuller54/Themis-ASM.git

conda activate /project/rumen_longread_metagenome_assembly/environments/testenv/

- Themis-ASM

sbatch ../runthemis_tf.sh

job id 7827232

-DID NOT WORK

-remove directory, clone again, and try again with themis_fuller2...

conda activate /project/rumen_longread_metagenome_assembly/environments/themis_fuller2

sbatch ../runthemis_tf.h

job id 7827269

-ERROR ABOUT SSL CERTIFICATES. DID NOT EXIST SO REINSTALLED

conda remove certifi

conda install certifi

--TRY AGAIN BOTH IN THEMIS_FULLER2 and TESTENV

mkdir debug_tf_testenv
 
cp runthemis_tf.sh debug_tf_testenv/runthemis_tf.sh

-in debug_tf_testenv

git clone --recursive https://github.com/tdfuller54/Themis-ASM.git

conda activate /project/rumen_longread_metagenome_assembly/environments/testenv/

- Themis-ASM

sbatch ../runthemis_tf.sh

job id 7827342

GIVES SAME ERROR AS LISA... MAY BE SOMETHING WITH MAMBA LIKELY???

--back in debug_tf

activate themis_fuller2

add the -r flag in runthemis_tf.sh

in Themis-ASM

sbatch ../runthemis_tf.sh

job id 7827349
```

oddly cache directory is where json files are stored that permission isn't allowed. However, the cache directory in themis_fuller2 is empty... could this be things that are cleared when a run succeeds to prevent other errors??? Why would they be written to begin with???

###Try the testenv one more time after removing all .json and .solv files in cache/

```
in debug_tf_testenv

add -r flag to runthemis_tf.sh

conda activate /project/rumen_longread_metagenome_assembly/environments/testenv/

in Themis-ASM

sbatch ../runthemis_tf.sh

job id 7827355

DIDNT WORK

CLEARED AND TRYING ONE MORE TIME

job id 7827931

STILL DIDNT WORK!!!
```

These are in the freebayes environment created by the themis_fuller2 test run (in conda-meta/ of environment directory)

bcftools1.10-h5d15f04_0

htslib-1.10.2-hd3b49d5_1

May want to try and generate an entirely new environment with and without mamba and see if the environments can be created then... Something with testenv clearly isn't happy still but themis_fuller2 seems to be working...

#8/26/22

```bash

conda create -p /project/rumen_longread_metagenome_assembly/environments/freebayes2 -c conda-forge -c anaconda -c bioconda -c defaults freebayes=1.3.1 numpy perl-base perl-list-util bcftools=1.10 parallel=20190522 "bedtools>=2.29" sed=4.7
```


The fix!!!

```bash

conda config --show channel_priority
conda config --set channel_priority disabled

```

ultimate test

```bash

conda create -p /project/rumen_longread_metagenome_assembly/environments/themis_fuller3 -c bioconda -y -c conda-forge mamba

conda activate /project/rumen_longread_metagenome_assembly/environments/themis_fuller3

mamba install -c bioconda merqury samtools bwa snakemake

pip3 install pysam


--in LKsynteny

cp -R Themis-ASM/ Themis-ASM_all

--in Themis-ASM_all

sbatch ../runthemisall.sh

job id 7917616
```


Wait and see...

Working but got an error for circos_plot. Will look into this Monday...


python /project/forage_assemblies/analysis/LKsynteny/Themis-ASM_all/scripts/pafCircosPlotter.py -p mapped/mapMedTruncatula_SubClover.paf -r fastas/MedTruncatula.fa -q fastas/SubClover.fa -o final/MedTruncatula_SubClover/circos_plot -c 10 -m 100000


Seems to run and shows logging info but either circos is not running or it is no longer capturing the output

running circos alone shows that perl submodules are not up to date. Will work on this later because my other circos environment works

Errors:

missing Statistics::Basic
  error Can't locate Statistics/Basic.pm in @INC 

missing Math::VecStat
  error Can't locate Math/VecStat.pm in @INC

missing Set::IntSpan
  error Can't locate Set/IntSpan.pm in @INC

missing Params::Validate
  error Can't locate Module/Implementation.pm in @INC

missing Math::Round
  error Can't locate Math/Round.pm in @INC 

missing Math::Bezier
  error Can't locate Math/Bezier.pm in @INC 


#8/29/22
####Made changes to circos.yaml and also created a log file to capture stderr and stdout of circos_plot rule. Now try to restart themis.

```bash

conda activate /project/rumen_longread_metagenome_assembly/environments/themis_fuller3

-in Themis-ASM_all

git pull

- LKsynteny
edit runthemisall.sh to include -r

-in Themis-ASM_all

sbatch ../runthemisall.sh

job id 7919406

still noted perl-gd and gd-polyline weren't present. Noticed thatjust installing perl-gd seems to install all other perl modules needed so made changes and running again

sbatch ../runthemisall.sh

job id 7919687

Still gets error so it looks like we possibly need to list each of the perl modules
```

perl-gd                              2.71-pl526he860b03_0 --> 2.76-pl5321h5b5514e_1


- perl-math-round
  - perl-statistics-basic
  - perl-math-vecstat
  - perl-set-intspan
  - perl-params-validate
  - perl-math-bezier

#8/30/22
####Added all perl modules to circos.yaml and am running again.

```bash
conda activate /project/rumen_longread_metagenome_assembly/environments/themis_fuller3

-in Themis-ASM_all

git pull

-in Themis-ASM_all

sbatch ../runthemisall.sh

job id 7920206
```


###Order just continued to change modules that were missing. Likely had things installed but perl couldn't find the location. switching back to strict channel_priority and removing all perl modules from yaml file is what ultimately fixed the issue. Just note that a user may need to bounce back and forth between strict and disabled. maybe just use disabled to generate the base environment but then switch to strict again before executing themis. Not sure if all themis environments will get built properly using strict. This is something that would need further testing

```bash

--NOTE- removed all non working circos environments from .snakemake/conda/

-in Themis-ASM_all

conda config --set channel_priority strict

git pull

sbatch ../runthemisall.sh

job id 7920411
