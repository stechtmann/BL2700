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
```{BASH}
grep "eC_number=" Ch_DSM.gff | cut -f9 | cut -f1,2 -d ';'| sed 's/ID=//g'| sed 's/;eC_number=/\t/g' > PROKKA.Ch_DSM.ec
```
### Extract all of the genes with COGs
```{BASH}
grep 'COG' Ch_DSM.tsv | cut -f 1,6 -d $'\t'
```

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
