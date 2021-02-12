# In class work
Protein folding chaperones assist proteins in proper folding.  Due to the essential role of protein folding chaperones, they are found universally across the tree of life.  One of the most important chaperones is the Chaperonin (CPN) also known as the heat shock protein 60.  In this exercise we are going to use BLAST to study the similarity between CPN from diverse domains of life by studying the diversity of chaperones from an intracellular symbiont of a hyperthermophilic archaeon.  The symbiont (Nanoarchaota equitans) lives within the cells of Igniococcus spp.  It has adapted for growth as a symbiont and has streamlined it's genome.

## Web BLAST activity
1. Perform a blastp with NP_418567.1 (Protein sequence for the *E. coli* chaperonin (GroEL)) as your query against the reference protein (RefSeq) database  
1. Perform a blastp with NP_418567.1 as your query against the reference protein (RefSeq) database searching only Bacteria   
1. Perform a blastp with NP_418567.1 as your query against the reference protein (RefSeq) database searching only Archaea   
1. Perform a blastp with NP_418567.1 as your query against the reference protein (RefSeq) database searching only Eukarya
1. Perform a blastp with NP_418567.1 as your query against the reference protein (RefSeq) database searching only *Homo sapiens*

Record the following results for each search
1. Note overall quality of the hits.  
1. Are any of the top 100 hits non-significant?
1. Out of the top 100 hits what is the highest scoring hit?
1. Out of the top 100 hits what is the lowest scoring hit?

What conclusions can be drawn from these results?  

## Command line BLAST activity
### Part 1 - Install blast tools
#### Obtain and install Miniconda
- Log on to colossus

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

#### Install Programs for the next few weeks

```{BASH}
conda create -n homology -c bioconda -c conda-forge clustalo quicktree \
             muscle phyml modeltest-ng fasttree raxml blast entrez-direct
```

#### Activate environment
```{BASH}
conda activate homology
```

## Part 2 - Command line

### Set up
- Navigate to your in class directory and make a new directory for todays work.
```{BASH}
cd Desktop/BL2700_data
mkdir BLAST
```
- Change into the new directory
```{BASH}
cd BLAST
```

### E-utilities Overview and Download data
- Download the fasta of annotated protein sequences for the genome of Nanoarchaeota equitans using `efetch`

`efetch` is part of the NCBI package of command line tools called e-utilities (https://www.ncbi.nlm.nih.gov/books/NBK25497/).  These utilities are ways of interacting with the NCBI databases over the command line.  While it's possible to just navigate to the NCBI website, find the data file that you're interested in, download it, move it to colossus and then use it, e-utilities allows for more rapid access to this data.  Additionally, it allows for you to extract multiple entries from the database with one command.

Two major commands in e-utilities are 'esearch' and 'efetch'.  'esearch' is like typing the query into the search box of NCBI. 'efetch' will download the file based on a query.

```{BASH}
esearch -db protein -query "hemoglobin" 
esearch -db protein -query "Lokiarchaeota tubulin" 
```
`efetch` can be used to download things.  This can be based on accession number. By default `efetch` will print to the screen so we will use the `>` to send the output to a file for us to use.

```BASH
efetch -id AE017199.1 -db sequences -format fasta_cds_aa > N.equitans.fasta
```

It's possible to pipe commands in e-utilties together to output all of the entries that match a query

```{BASH}
esearch -db protein -query "Lokiarchaeota tubulin" | efetch -format fasta
```

### BLAST Activity

#### Making the database
- Make a blastable database of protein sequences from the unknown Lokiarchaeote protein sequences
```BASH
makeblastdb -in N.equitans.fasta -dbtype prot -out Nano_db
```


#### Download the queries
- Use wget to download the `CPN_BL.fasta` from the github page
```BASH
wget https://raw.githubusercontent.com/stechtmann/BL2700/master/data/all_HSP.fasta
```

#### Perform the BLAST
- Perform a blastp against the Lokiarchaeote protein db with the `CPN_BL.fasta` as your query  
```BASH
blastp -db Nano_db -query all_HSP.fasta -out HSP_BLAST.txt -outfmt 7
```

#### Interpret the results
- Look at your results
```{BASH}
less HSP_BLAST.txt
```

- Which heat shock proteins have homologs in the Nanoarchaeota genome?
- Are there any paralogs?
- Explain your answer
