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
- Download the fasta of annotated protein sequences for the the unknown Lokiarchaeote by using `wget`
```BASH
wget https://raw.githubusercontent.com/stechtmann/BL4300-5300/master/data/In_class/Loki_protein.faa
```

- Make a blastable database of protein sequences from the unknown Lokiarchaeote protein sequences
```BASH
makeblastdb -in Loki_protein.faa -dbtype prot -title Loki_protein
```

- Use wget to download the `CPN_BL.fasta` from the github page
```BASH
wget https://raw.githubusercontent.com/stechtmann/BL4300-5300/master/data/In_class/CPN_BL.fasta
```

- Perform a blastp against the Lokiarchaeote protein db with the `CPN_BL.fasta` as your query  
```BASH
blastp -db Loki_protein.faa -query CPN_BL.fasta -out CPN_BLAST.txt -outfmt 7
```

- Which of the query proteins is the best hit?  
- Which of the Loki proteins is the closest homolog of the query sequences?  
- Are the same Loki proteins the same best hits for the query sequences?
- Does the Lokiarchaeote have any CPNs.
