## 7-14-21
#Print DAG of jobs executed by mgatlas within illumina_atlas_run_3 directory as a pdf


```
snakemake --snakefile /KEEP/rumen_longread_metagenome_assembly/mgatlas_fuller/lib/python3.6/site-packages/atlas/Snakefile --configfile /lustre/project/rumen_longread_metagenome_assembly/tfuller/illumina_atlas_run_3/config.yaml --profile cluster --
jobs 50 --dag | dot -Tpdf > dag.pdf
```

Not sure why I had to remove stone and dropbox package in order to remove snakemake.

I then reinstalled snakemake and found that mgatlas was also removed so I reinstalled that as well.

stone was never added back... maybe that was messing with snakemake?

The dashed outline represents rules that have been completed and have up to date outputs. Solid lines are those rules that have not been completed or up to date.