scp -r tyson.fuller@ceres-dtn.scinet.usda.gov:/lustre/project/rumen_longread_metagenome_assembly/tfuller/illumina_atlas_run_3.tar.gz tyson.fuller@atlas-dtn.hpc.msstate.edu:/project/rumen_longread_metagenome_assembly/tfuller/illumina_atlas_run_3.tar.gz && echo "scp done"  || echo "fail" > out.txt


for tar just used && echo "tar done". This worked.

**Note that not 100% sure where out.txt will be but should be in tfuller. Make sure not to overwrite if tar of atlas run works and trying to SCP that**

du -hsc --apparent-size for directory

ls -sh for file

-atlas interactive shell request
srun -A rumen_longread_metagenome_assembly -p atlas -q normal --pty bash