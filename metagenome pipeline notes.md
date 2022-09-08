All things metagenome

Note that LIB101996_R1&2 files are the reads used to align to the assembly and assembly.fasta is the assembly file. Both of these are in /project/forage_assemblies/tfuller/


--GTDB rule downloads the database, then identifies, then aligns, then classifies... What do all these do and what do all of them return???

--identify
	-uses dastool bins (binning/{assembly}/DASTool/output/DASTool_results_DASTool_bins/)
	-uses gtdb database
--align
	-just uses output of identify
--classify
	-uses output of align
	-uses dastool bins



-checkm uses binning/{assembly}/DASTool/output/DASTool_results_scaffolds2bin.tsv as well as DASTool_results_DASTool_bins/ for input


-Don't checkm, DASTool and GTDB return the same things though? should the respective inputs be changed and maybe only use gtdb classifications for final output??


--ADD relative abundance and quantification of species


-Should structure some things based on data analysis from Derek's recent manuscript. This could be a good starting point for things to incorporate, visualization, and possibly even give ideas of extra features to add based on what I think might be useful.


