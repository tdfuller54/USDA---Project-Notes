#2/23/22
###Ceres file transfer and clean up in new directory structure.

#REMEMBER TO REDIRECT .conda SYMBOLIC WHEN CP IS COMPLETE

```bash
srun -N 1 -n 1 --mem=36000 -t 1-0 -p priority -q msn --pty bash

--in /project/forage_assemblies/

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="cp -avr /OLD/project/forage_assemblies/tfuller/ /project/forage_assemblies/ && echo \"forage_assemblies tfuller copied successful\" || echo \"something didn't complete right\""

job id 7237970

--in /project/rumen_longread_metagenome_assembly/

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="cp -avr /OLD/project/rumen_longread_metagenome_assembly/tfuller/ /project/rumen_longread_metagenome_assembly/ && echo \"rumen_longread_metagenome_assembly tfuller copied successful\" || echo \"something didn't complete right\""

job id 7237971
```

#Not only .conda but will need to check all symbolic links. not sure if they will redirect to the /OLD/ directory or not. It is possible I will need to redirect them to the locations in /OLD/ or copy them myself to what would be their correct location in the new directories if possible.

```bash
--try to capture error
sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="cp -avr /OLD/project/forage_assemblies/tfuller/ /project/forage_assemblies/ 2> copy.log && echo \"forage_assemblies tfuller copied successful\" || echo \"something didn't complete right\""

job id 7238020

--tar the folder into new directory

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="tar vcfz tfuller.tar.gz /OLD/project/forage_assemblies/tfuller/ && echo \"forage_assemblies tfuller copied successful\" || echo \"something didn't complete right\""

job id - 7238027

--start tar for rumen_longread directory as well

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="tar vcfz tfuller.tar.gz /OLD/project/rumen_longread_metagenome_assembly/tfuller/ && echo \"rumen_longread_metagenome_assembly  tfuller copied successful\" || echo \"something didn't complete right\""

job id 7238029
```

cp errors for forage_assemblies examples

cp: cannot create hard link ‘/project/forage_assemblies/tfuller/Themis-ASM/.snakemake/conda/a62f7339/lib/libffi.so.8.1.0’ to ‘/project/forage_assemblies/tfuller/Themis-ASM/.snakemake/conda/5ca3d7c7/lib/libffi.so.8.1.0’: Operation not permitted

cp: cannot create regular file ‘/project/forage_assemblies/tfuller/Themis-ASM/busco/MedTr/busco_summary.txt’: Permission denied

##2/24/22

--Extract tar files

```bash
--in forage_assemblies
-first remove incomplete tfuller directory

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="rm -rf tfuller/ && echo \"incomplete directory removed\" || echo \"something didn't complete right\""

job id 7241025

-now extract tar file
sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="tar xvzf tfuller.tar.gz && echo \"forage_assemblies tfuller extraction successful\" || echo \"something didn't complete right\""

job id 7241029

--in rume_longread_metagenome_assembly
-first remove incomplete tfuller directory

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="rm -rf tfuller/ && echo \"incomplete directory removed\" || echo \"something didn't complete right\""

job id 7241024

-now extract tar file
sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="tar xvzf tfuller.tar.gz && echo \"rumen tfuller extraction successful\" || echo \"something didn't complete right\""

job id 7241030


--Cancelled untar because directory structure is still intact

in each forage_assemblies and rumen_longread_metagenome_assembly create tfuller directory


--first rumen_longread_metagenome_assembly

mkdir tfuller

--remove incomplete directory
sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="rm -rf OLD/ && echo \"incomplete directory removed\" || echo \"something didn't complete right\""

job id 7241033


--extract files in proper directory while removing repeated parent directories

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="tar xvzf tfuller.tar.gz -C tfuller/ --strip-components=4 && echo \"rumen tfuller extraction successful\" || echo \"something didn't complete right\""

job id 7241036


--second forage_assemblies

mkdir tfuller

--remove incomplete directory
sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="rm -rf OLD/ && echo \"incomplete directory removed\" || echo \"something didn't complete right\""

job id 7241039


--extract files in proper directory while removing repeated parent directories

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="tar xvzf tfuller.tar.gz -C tfuller/ --strip-components=4 && echo \"forage tfuller extraction successful\" || echo \"something didn't complete right\""

job id 7241041
```

this might take awhile.. Once done might want to check how .snakemake calls environments if even possible? Hopefully it recognizes new location but if not this all might be a waste...

Remember about .conda symbolic link in home directory that still need to eventually be redirected as well.

Remember that all symbolic links in unzipped tars will also need to be redirected and original files may need to be transferred over out of "OLD/"

--Looks like hard links in the tar file caused an error. Will try to use --hard-dereference


```bash
--tar the folder into new directory

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="tar vcfz tfuller2.tar.gz /OLD/project/forage_assemblies/tfuller/ --hard-dereference && echo \"forage_assemblies tfuller copied successful\" || echo \"something didn't complete right\""

job id 7241423 

--start tar for rumen_longread directory as well

sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="tar vcfz tfuller2.tar.gz /OLD/project/rumen_longread_metagenome_assembly/tfuller/ --hard-dereference && echo \"rumen_longread_metagenome_assembly  tfuller copied successful\" || echo \"something didn't complete right\""

job id 7241420







sbatch -N 1 -n 2 --mem=50000 -p priority -q msn --wrap="cp -avr /OLD/project/forage_assemblies/tfuller/Themis-ASM/* /project/forage_assemblies/tfuller/Themis-ASM/ && echo \"forage_assemblies tfuller copied successful\" || echo \"something didn't complete right\""

job id 7242518 (In Themis-ASM directory)






