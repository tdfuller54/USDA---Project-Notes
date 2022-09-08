#Week of 4-4-22

Caught up on emails. Reviewed notes from last themis run and where things were left off.

Note used 24k and 24w and asm5 params for mapLentil_GrassPea.paf in final run that worked

Everything else completed within the snakemake workflow

Worked on catching up aglearn training courses. Only completed one before the website was closed for an update though...

#To Do or things to think about

###Themis
-Wanted to run themis with a premade configuration file instead of a long command line with multiple options. This would require changing the themisASM.py script but I think everything else should be the same once changed.
	-Would also need to change the read.me file to accurately describe how to run themis and explain how to structure the config file

-Down the road adding features in themis to show stats based on each set of input reads alone would be nice. It currently aligns them all and merges them all into one bam file.

-Also would want to consider option for just one assembly. Granted this takes away several features of themis but this would possibly have some utility for anyone that wants a quick quality assessment of an assembly.

###metagenome pipeline
-Need to restructure the metagenome project pipeline. Some things aren't written within the pipeline because I was testing the best way to analyze the data and troubleshoot issues.
	-eukdetect did not work in pipeline but I believe I can create a workaround if need be. However this is not necessarily a great tool and would require a lot of additions to the config file and script edits.
	-checkm and gtdb output need to be interpreted and presented in a reasonable fashion.

-Need to look into relative abundance and quantification to incorporate that data

-Should structure some things based on data analysis from Derek's recent manuscript. This could be a good starting point for things to incorporate, visualization, and possibly even give ideas of extra features to add based on what I think might be useful.

###Other things

-Finish tweaking CV and send to Derek

-Think about writing outline for themis manuscript or working on figure/s

-Coursera courses to consider. Should take at least one!

	

-YES
	1. genomic data science and clustering (bioinformatics V) - possibly take all bioinformatics series
	-YES but as a second course
	-offered by UC San Diego

	2. Gut Check: Exploring Your Microbiome
	-really only interested in one part of this

-MAYBE
	1. **applied machine learning in python (this is actually course 3 of a 5 course series - applied data science with python). This would be most relevant to ML but all 5 together go from basic python to actual ML implementation.
	-uses scikit learn and ~36 hours. Introduces scikit learn in a tutorial and goes over data dimensionality, clustering, supervised, and unsupervised predictive modeling. university of michigan

	2. *machine learning with python (Actually part of certificate course IBM data science) - Larger course might prove more useful since it is very in depth about everything data science related. Might be too long though???
	- 22 hours long. Has general intro to ML then works with real world examples using scikit learn and scipy. IBM offered.
	
	3. **IBM intro to machine learning
	- multipart course that uses python. Goes over ML introduction then types of supervised and unsupervised models. This could be broad but very useful. this is the 1st 4 courses of the coursera IBM Machine Learning Professional Certificate. Cannot find this offered through USDA but may very well be worth asking for it to be added to take the last 2 courses using deep learning and time series analyses
	
	4. AI for scientific research and machine learning models in science
	-python. both taught by learnquest. Actually use health data and AI for scientific research uses genome sequence data of covid-19 variants. Might be able to take both of these.
	
	3. Deep Neural Networks with Pytorch
	- 31 hours of material. Seems to start with very basics and build up into different types of more complex neural networks
	
	4. Python for data science AI and development
	-Good basic python intro but only if I can take whatever freely and have time

-Probably not
	1. exploratory data analysis for machine learning
	-I think uses python???
	-Really more intro to it and talks about gathering and preprocessing data.. not bad but not what I'm looking for

	2. IBM applied AI
	- really thorough course that uses some python but I think more IBM watson AI. Would be interesting but not for me currently.
	
	3. Deep Learning with pytorch
	- all 4 are based on image identification

	4. advanced ML & signal processing
	-scikit & sparkML. Seems to be more advanced. uses sensor on phone to read vibrations as a test dataset. Would be interesting but likely beyond my scope of interest