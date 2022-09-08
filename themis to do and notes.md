#1/3/22

Meeting notes
-templates were from open source twitter css  file.
-Look these up google html template or css template
-Incorporate tables to put figures and table side by side (within tables).

-tablines does separate the .tab file by the lines with dashes into a list that has each section of the table in it's indexes. These also have the "\n" already in them for separating rows.

-tables extension for markdown is same syntax as defined in extras

-smartys extension just allows some extra syntax for ascii symbols

-Ultimately mdlines is joined altogether into one large string. each list element has '\n' appended to the end so the large string can be parsed accordingly by markdown.markdown based on that.

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" themis_summary.zip

job id 6845374

--This started to rerun almost all rules. I think this is because symbolic links for assemblies are created and used for almost every rule so snakemake thinks it needs to re run every rule that uses those as input.

--note error in run_merqury when trying to move file to place that already exists. Deleted previous files. While waiting will look for other rules that use mv command.

tired of waiting. Will see where process is tomorrow and try restarting from there.

Also will work on putting plots and tables into html or markdown tables for webpage layout

#1/4/22

- can use -R or --forcerun to make snakemake rerun a rule even if output is there. this uses the rulename as input
- just use yesterdays syntax for now though since you deleted some files already

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" themis_summary.zip

job ID 6846142

jog completed, yay!

edited to make symbolic links not temporary. Now running again so they are there for future work.

sbatch -N 1 -n 2 --mem=25000 -p priority -q msn snakemake -s /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/themisSnakefile --jobs 50 -p --use-conda --cluster-config /lustre/project/rumen_longread_metagenome_assembly/tfuller/Themis-ASM/cluster.json --cluster "sbatch --nodes={cluster.nodes} --ntasks-per-node={cluster.ntasks-per-node} --mem={cluster.mem} --partition={cluster.partition} -q {cluster.qos} -o {cluster.stdout}" -R package_for_distrib

job id 6850193

package for distrib doesn't require fastas so deleted themis_summary_page.html and ran again

job id 6850706

This did successfully keep the symbolic links so hopefully all future force runs will be short

resizing did not work :(

--USE IDS TO MAKE STRING LENGTH OF NAMES STANDARD
--Might not need to do this. Some examples just show one dash around each | for separators


-done--Now that table rendering is working my main goal should be to generate 2 layouts. One should be rendered with figures side by side and tables below. The other should have tables next to figures. One like what I generated. The other like what is already in the themis schema.

-done--look up application notes and see what you can find for already existing articles like that to determine formatting.

Done-see new edit below--Add titles to each plot. This can possibly be done in the same cell. If this does not work then can make new row above that will include the titles.

Done--Reorganize where the tables go in the webpage.

Done--Need to somehow add a row at the very top of summary_table with file locations/names. Can try to piece together Derek's script but may need to ask him to walk me through it.

--Remove white borders around plots so that headers line up more evenly above them. Also try to keep the axis labels from getting cut off.

-rule ideogram_plot
-rule plot_frc
-rule ngx_plot
-rule plot_busco
-kmer plot is created entirely with script from merqury software. NOTE that run_sample_merqury doesn't get called. run_merqury is what gets called.

--Margin in header is 10 pixels each side. header size is 20px. Note current figure was 627.7px and total column was 669.7px. So maybe 1px extra times 2 for header and plot. kmer plot is 390px plus header is 20. Maybe make height 432-433px (390 for plot, 20 for header, 10+10 for margin on header, and 2-3 extra for spacing)?
