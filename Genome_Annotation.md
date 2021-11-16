## Perform Annotation on the de novo assembled genome
### Prepare your environment
```{BASH}
conda activate BL2700
cd data/
mkdir Annotation
cp Assembly/Ch_DSM_spades_output/contigs.fasta Annotation/
cd Annotation  
```
### Format the SPAdes assembly to be compatible with prokka
Prokka does not like long names as is the natural output of SPAdes.  To prepare our data for processing with prokka, we must change the names of the contigs.  We will use a unix filter known as `awk` to perform this change.
```{BASH}
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

### Take a look at the data from the output.

#### Navigate into the output directory
```{BASH}
cd Ch_DSM
```
#### Look at `.txt` file.
This file lists some of the key elements and how many of each of those elements are in this genome.

```{BASH}
head Ch_DSM.txt
```
#### Look at `.gff` file.
This file is the annotation file with information about how each of the elements were identified and if they were identified what their functional annotation is. This also includes some of the functional bin information like E.C. number.

```{BASH}
less Ch_DSM.gff
```
Once you're finished you can click `q` to exit less.

#### Look at `.tsv` file.
This is a table formatted annotation file with information about the gene such as COG and E.C. number

```{BASH}
head Ch_DSM.tsv
```
### Extract all of the genes with COGs
```{BASH}
grep 'COG' Ch_DSM.tsv | cut -f 1,6 -d $'\t'
```
The logic of this command is as follows.
- Use the `grep` command to search for all lines that COG in them
- use the `cut` command to only cut the locus tag and the COG number (fields 1 and 6).  Since this is a tab separated file, you can use the tab as the delimiter (`$'\t'`).

### Extract all of the genes with an EC Number
```{BASH}
cut -f 1,5 -d $'\t' Ch_DSM.tsv | sort -k 2 -nr >PROKKA.Ch_DSM.ec
```
The logic of this command is as follows.
- use the `cut` command to only cut the locus tag and the E.C. number field (fields 1 and 5).  Since this is a tab separated file, you can use the tab as the delimiter (`$'\t'`).
- Use the `sort` command to order the column with the E.C. numbers in it (column 2) so that you focus on the genes with E.C. numbers.  The `-nr` flag indicates that you want to sort it in reverse numerical order so that the genes without and E.C. number are at the bottom of the file.

### Construct a genome scale metabolic model

Use FileZilla to move the .faa file

Naviagate to [KEGG MAPPER](https://www.kegg.jp/kegg/tool/annotate_sequence.html)

Upload the file to KEGG Mapper and select Carboxydothermus hydrogenoformans as the Genus species.

If your genome is larger (>2500 genes), then you use use BLAST Koala.  It is the same program, but can accomodate genomes with more genes.

- Navigate to [BLAST KOALA](https://www.kegg.jp/blastkoala/)
- Upload you sequence.
- Enter the taxonomic group of Bacteria.
- Enter KEGG gene database of genus prokaryotes.
- Enter your email address.

### Identify antibiotic resistance genes in your genom

Use the .faa file from the previous step to identify antibiotic resistance genes.
Navigate to [CARD (Comprehensive Antibiotic Resistance Database)](https://card.mcmaster.ca/)
- Click on Analyze
- Click on RGI (Resistance Gene Identifier)
- Click on Choose File under upload fasta file (Choose the .faa file).
- Upload the .faa file.
- Make sure the following parameters are set
     - Select data - Protein sequences
     - Select criteria - Perfect and strict hits only.
     - Nudge ≥95% identity Loose hits to Strict: Exclude nudge
     - Sequence Quality: Low Quality 
