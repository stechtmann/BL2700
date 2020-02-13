# In class work
Protein folding chaperones assist proteins in proper folding.  Due to the essential role of protein folding chaperones, they are found universally across the tree of life.  One of the most important chaperones is the Chaperonin (CPN) also known as the heat shock protein 60.  In this exercise we are going to use BLAST to study the similarity between CPN from diverse domains of life by studying the diversity of chaperones from an intracellular symbiont of a hyperthermophilic archaeon.  The symbiont (Nanoarchaota equitans) lives within the cells of Igniococcus spp.  It has adapted for growth as a symbiont and has streamlined it's genome.

## Web BLAST activity (10 minutes)
1. Perform a blastp with NP_418567.1 (Protein sequence for the *E. coli* chaperonin (GroEL)) as your query against the reference protein (RefSeq) database  
1. Perform a blastp with NP_418567.1 as your query against the reference protein (RefSeq) database searching only Archaea   
1. Perform a blastp with NP_418567.1 as your query against the reference protein (RefSeq) database searching only Eukarya
1. Perform a blastp with NP_418567.1 as your query against the reference protein (RefSeq) database searching only *Homo sapiens*

Record the following results for each search
1. Note overall quality of the hits.  
1. Are any of the top 100 hits non-significant?
1. Out of the top 100 hits what is the highest scoring hit?
1. Out of the top 100 hits what is the lowest scoring hit?

What conclusions can be drawn from these results?  
Is the human TCP-1 homogous to the *E. coli* GroEL?

## Command line BLAST activity (30 minutes)
### Part 1 - Install blast tools
#### Obtain and install Miniconda
```{BASH}
cd ~
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```
- During the install, you will be asked to read the End User Agreement and accept the license
- You will also be asked if you want to initialize miniconda - say yes

- After installation is successful run the following command
```{BASH}
source ~/.bashrc
```

#### Install blast

```{BASH}
conda install -c bioconda blast
```

### Part 2 - Command line

- Log on to colossus
- Navigate to your in class directory and make a new directory for todays work.
- Change into the new directory
- Download the fasta of annotated protein sequences for the genome of Nanoarchaeota equitans using `efetch`
```BASH
efetch -id AE017199.1 -db sequences -format fasta_cds_aa > N.equitans.fasta
```

- Make a blastable database of protein sequences from the unknown Lokiarchaeote protein sequences
```BASH
makeblastdb -in N.equitans.fasta -dbtype prot -out Nano_db
```

- Use wget to download the `CPN_BL.fasta` from the github page
```BASH
wget https://raw.githubusercontent.com/stechtmann/BL2700/master/data/all_HSP.fasta
```

- Perform a blastp against the Lokiarchaeote protein db with the `CPN_BL.fasta` as your query  
```BASH
blastp -db Nano_db -query all_HSP.fasta -out HSP_BLAST.txt -outfmt 7
```

- Which heat shock proteins have homologs in the Nanoarchaeota genome?
- Are there any paralogs?
- Explain your answer
