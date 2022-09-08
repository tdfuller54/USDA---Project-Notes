#9/15/21. Add Eukdetect to snakemake HiFi workflow

-Eukdetect itself seems to be an independent snakemake workflow without a direct conda install

Instead this uses conda to create an environment with all the dependencies. then within that environment python install is used to install eukdetect. Likely need to create environment in snakemake then make a rule that installs eukdetect..

-Eukdetect has rules folder with rules file. This is snakemake file that executes everything it seems. This can be used to quickly add the rules for entire eukdetect execution. Will just need to generate environments for each if breaking up dependencies is simple. Bare minumum the environment.yaml can be used to make a massive environment to run each of these rules. NOTE that this will still need git repo and euk database downloaded but then can add necessary parameters in existing config file.

-***Reads are paired-end*** known because they are labeled r1 and r2. Not SE.

-Read lengths are both 150 based on one-liner on eukdetect site.. This is correct based on read length stats from atlas run I did in past

10/4/21
Noticed something right next to log about saying it didn't exist because snakemake moves to eukdetect folder to run this rule. Thought this could be error so wrote full directory to log in rule and this actually made it write to the log but the same error returned in the log as before. whether or not snakemake moves to the directory of the EukDetect folder or not it still give the "bad interpreter" error and nothing else when it knows where to write the log

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake --snakefile rules/eukdetect.rules --configfile euk_config.yml --cluster-config cluster.yml --cluster "sbatch -N 1 --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} -p priority -q msn" --use-conda --jobs 50 --verbose --latency-wait 60

--Running eukdetect independently seems to not work either. It actually creates a snakemake log but the log says "ESC" within the rules out of place..

--Running rules file as a snakemake command actually does work. This seems to suggest the rules script will execute properly. Can likely put this script into my snakemake workflow and add any necessary user input into my main config file.