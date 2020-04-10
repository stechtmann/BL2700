## Perform Annotation on the de novo assembled genome
### Format the SPAdes assembly to be compatible with prokka
Prokka does not like long names as is the natural output of SPAdes.  To prepare our data for processing with prokka, we must change the names of the contigs.  We will use a unix filter known as `awk` to perform this change.
```{BASH}
cd Ch_DSM_spades_output/
awk '/^>/{print ">Ch_DSM_" ++i; next}{print}' < contigs.fasta > contigs_names.fasta
```
This command is performing the following function.
-  search for lines that start with `>`
-  replace the `>` with `>T1_`
-  after the `G15_` add a number with the first instance being 1 and each instance increasing by 1
-  read in `contigs.fasta`
-  output a new file `contigs_names.fasta

### Run the prokka pipeline on the `contigs_names.fasta` file.
```{BASH}
prokka --outdir Ch_DSM --prefix Ch_DSM --locustag Ch_DSM contigs_names.fasta
```

### Extract all of the genes with an EC Number
grep "eC_number=" Ch_DSM.gff | cut -f9 | cut -f1,2 -d ';'| sed 's/ID=//g'| sed 's/;eC_number=/\t/g' > PROKKA.Ch_DSM.ec

### Extract all of the genes with COGs
grep 'COG' Ch_DSM.tsv | cut -f 1,6 -d $'\t'
