#1/19/22 - Test run circos plot rule to see what output is.

```bash

srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

module load python_3/3.6.6 miniconda/3.6

conda activate /KEEP/rumen_longread_metagenome_assembly/themis_fuller/

cd /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM

git pull

sbatch -N 1 -n 2 --mem=25000 -t 2-0 -p priority -q msn snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -t 2-0 --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" -R run_busco circos_plot
```

job id 7050214 is running (building remote environment 2:18pm)

--Need to add parameters for circos_plot rule in cluster.json. The default job name and stdout have the wildcard.sample which does not exist for this particular job.

Added params for circos_plot rule in cluster.json and running script again

job id 7087963 started ~11:40am

-- Corrected error with f string but now there seems to be  a problem with the minimum range value.. Will have to tease this apart tonight/tomorrow.



--This required a lot of troubleshooting.
It ran multiple functions on classes before returning the error. I had to debug and trace back each function until I found what wasn't being written and then determine why it wasn't writing in the values

Found error is in calling s[5]. This should likely be s[1]?? in readPAF? s[5] is + or - strand

-- With script running I am getting an error with circos. Not sure how to fix but seems that some packages are not installing properly. Trying to force add them.

-- After symbolic link workaround it ran but smaller chromosome isn't showing any alignment. I think cutoff is either indexing at 0 or llist only has one input for some reason. Will troubleshoot what llist returns in total and also what the cutoff calculates too.

-- in logging.info or debug print all of llist, value of cutoff, and targets_to_plot. Likely need to create an if else statement to make cutoff the last value in llist unless it's above a threshold.

-- Also need to find way to automatically add workaround into a rule to properly install perl modules

-- Made edits to cutoff logic so last value will be returned unless list is greater than 200. If so then the 200th value will be returned.

--cd $CONDA_PREFIX takes me to active conda environment folder in linux. Will use this in attempt to add workaround in automatically. (5afc38a1 value in conda folder)

--1/25/22

Everything seems to be generating correctly. Now can edit webpage script to incorporate svg or png file

--Made edits to create_webpage rule and script. Will review tomorrow and run to visualize output.