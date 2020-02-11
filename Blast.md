# In class work
Protein folding chaperones assist proteins in proper folding.  Due to the essential role of protein folding chaperones, they are found universally across the tree of life.  One of the most important chaperones is the Chaperonin (CPN) also known as the heat shock protein 60.  In this exercise we are going to use BLAST to study the similarity between CPN from diverse domains of life as well as study the CPN content of a novel organism recently discovered from the deep ocean - The Lokiarchaeotes - which are a member of a recently described group known as the Asgard Archaea.

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

- Which of the query proteins is the best hit?  
- Which of the Loki proteins is the closest homolog of the query sequences?  
- Are the same Loki proteins the same best hits for the query sequences?
- Does the Lokiarchaeote have any CPNs.
