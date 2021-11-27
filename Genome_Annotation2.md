# Data to be used

```{BASH}
cd ~/data
mkdir Annotation
cp Assembly/Ch_DSM_spades_output/contigs.fasta Annotation/
```

# Calling Genes with Prodigal
```{BASH}
prodigal -a Ch_DSM.faa -d Ch_DSM.fna -i contigs.fasta -o Ch_DSM -p single
```

# Using BLAST to annotate a genome (Extrinsic)
## Make a BLASTable database

### Download the Uniprot reviewed database
```{BASH}
wget https://ftp.uniprot.org/pub/databases/uniprot/current_release/knowledgebase/complete/uniprot_sprot.fasta.gz
```
### Unzip the `.fasta` file
```{BASH}
gunzip uniprot_sprot.fasta.gz 
```

### Make a blastable database
```{BASH}
makeblastdb -in uniprot_sprot.fasta -dbtype prot -out Uniprot_db
```
## Perform a BLASTp

```{BASH}
blastp -db Uniprot_db -query Ch_DSM.faa -out Ch_DSM_BLAST.txt -evalue 1e-30 -qcov_hsp_perc 90 -num_alignments 1 -outfmt 6 
```

# Run HMM

## Make an HMM

### Download the multiple sequence alignment

### Make the HMM using `hmmbuild`
```{BASH}
hmmbuild CooS.hmm CooS_aminoacids.aln
```
## Search the `.faa` file with the HMM using `hmmsearch`
```{BASH}
hmmsearch CooS.hmm Ch_DSM.faa > CooS_Ch_DSM.out
```
# Run the Prokka pipeline
## Format the SPAdes assembly to be compatible with prokka
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

## Run the prokka pipeline on the `contigs_names.fasta` file.
```{BASH}
prokka --outdir Ch_DSM --prefix Ch_DSM --locustag Ch_DSM contigs_names.fasta
```

## Take a look at the data from the output.

### Navigate into the output directory
```{BASH}
cd Ch_DSM
```
### Look at `.txt` file.
This file lists some of the key elements and how many of each of those elements are in this genome.

```{BASH}
head Ch_DSM.txt
```
### Look at `.gff` file.
This file is the annotation file with information about how each of the elements were identified and if they were identified what their functional annotation is. This also includes some of the functional bin information like E.C. number.

```{BASH}
less Ch_DSM.gff
```
Once you're finished you can click `q` to exit less.

### Look at `.tsv` file.
This is a table formatted annotation file with information about the gene such as COG and E.C. number

```{BASH}
head Ch_DSM.tsv
```
