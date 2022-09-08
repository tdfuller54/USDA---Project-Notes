#4/12/22
###Started going through the workflow for RNA seq analysis in the common vetch paper. This uses STAR to align the reads to the genome and then uses BRAKER to predict genes and transcripts. They returned:
- number of genes
- CDS + intron length average
- CDS length average
- exon length average
- intron length average
- exon count per gene average

###Derek sent email to inform me that transcripts were generated already using TRINITY. This should eliminate need for BRAKER and possibly STAR but do need to align the transcripts still with something.

transcripts fasta location: /project/forage_assemblies/assemblies/vetch_transcripts/sahv.transcripts.fasta

###Also started to do some rudimentary annotation prediction with medicago annotation data.

gene/transcript annotations location: /project/forage_assemblies/assemblies/vetch_transcripts/HV_RNA_Seq_Annotation.xlsx

#Based on this information Derek asked me to work on 3 things. These might best be done using different tools based on the pregenerated data than those used in the paper.

1.	Align the transcript fasta sequences to the Vetch reference genome to get coordinates for each?
	-I'm really thinking I want to use GMAP to map transcript fasta back to the genome assembly. Reporting only single top scoring alignment per sequence

2.	Calculate summary statistics on each transcript similar to table 7 of the Gigabyte journal article I just sent you? THIS INFO IS LISTED ABOVE

This should be retrieved by TrinityStats.pl
- number of genes


These need other tools maybe?
- CDS + intron length average (I think trinitystats returns CDS + UTRs only?)
- CDS length average
- exon length average
- intron length average
- exon count per gene average

3.	Associate gene terms (ie. GO, COG) with transcripts as per table 8 of the Gigabyte Journal article?
	-Uniprot?

Vetch genome assembly location: 
/project/forage_assemblies/assemblies/legume_comparison/ReformatedV.fa
/project/forage_assemblies/assemblies/legume_comparison/vetch_phase.assembly.fasta

