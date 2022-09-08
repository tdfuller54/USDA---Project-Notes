#Use echo to write things to a file and awk to fill second column of a tsv with sequential numbers and cat to concatenate one tsv on top of other

```
echo -e "contig\tbin" > header.tsv writes a tab separated (\t) file with the word contig then the word bin


awk -F "\t" '{$2="bin."++i;}1' OFS="\t" test.tsv > test1.tsv. -F says separator (\t) tab. $2 goes through column 2. bin.i++ writes bin.1, bin.2, etc. OFS also is separator. awk goes through test.tsv but must write new file test1.tsv


cat header.tsv test1.tsv > assembly_scaffold2bins.tsv

test1 is appended to bottom of header and written to assembly_scaffold2bins.tsv
```

#Look into how this works but essentially remove the last column from tsv

```
-remove last column by making space delimited file
awk 'NF{NF-=1};1' Huws_16S_colonisation_OTU_abundance_Table.txt > test_table.txt

Think NF refers to last column automatically

-convert space delimited to tab delimited
awk -v OFS='\t' '{ $1=$1; print }' test_table.txt > test2.txt`
```

#Look into value in a column and print if its above a certain threshold

```
awk '$2 > 20  {print $2}' <filename> 
```

#check size of directory or file

```
du -hsc --apparent-size for directory

ls -sh for file
```

#if column 2 equals the value awk prints it and wc -l returns the number of lines or total times column 2 equals that
```
awk -F "\t" '{if($2=="concoct.0") print $2}' concoct.scaffolds2bin.tsv | wc -l
```

srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash