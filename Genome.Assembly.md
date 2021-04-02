## Data
Use the quality filtered data from the quality filtering lab day.
### Navigate to your Genomics directory
```{BASH}
cd data/Genomics
```
### Make a new directory for your Assembly work
```{BASH}
mkdir Assembly
```
### Copy your quality filtered reads from your quality directory into the Assembly directory
The 'cp' command allows you to copy a file from one location to another.
Here we want to copy `Ch_pair_R1.fastq`, `Ch_pair_R2.fastq`,`Ch_unpair_R1.fastq`, and `Ch_unpair_R2.fastq`.  We could copy each on individiually.

Here we can use the wildcard `*` to make this process easier.  The wildcard `*` means everything that starts with or ends with something.  Here we will use `Ch_*` to indicate we want to do something with everything that starts with `Ch_` this will copy all of these files over to our new directory.

The structure of the `cp` command is `cp file.to.be.copied location.to.place.the.file`

```{BASH}
cp Quality/Ch_* Assembly
cd Assembly
```
## Screen Command
Since these commands take time to run, we will use a command called `screen` which will let us open mulltiple terminal windows on colossus.  This way we can run one command, detach from that screen, open another screen, and then run another command.

### Open a screen
```{BASH}
screen -S reference
```
You can then run the command that you want

```{BASH}
conda activate assembly
```
### Detach from the screen
To run other commands, you can then detach from the screen.

To detach from the screen you hold `ctrl` and `a` at the same time followed by `d`  

### Reattach to the screen
```{BASH}
screen -r reference
```

## Reference based genome assembly

### Get your reference
```{BASH}
efetch -id CP000141.1 -db sequences -format fasta > C.hydrogenoformansZ2901.fasta
```

### Generate index of the reference using bowtie
```{BASH}
bowtie2-build C.hydrogenoformansZ2901.fasta C.hydro
```

### Map the DSM reads to the reference
```{BASH}
bowtie2 -x C.hydro -1 Ch_R1_trim.fastq -2 Ch_R2_trim.fastq -S DSM.sam -X 1000
```

### Detach from the screen
hold down `ctrl` and `a` followed by `d`

## De novo genome assembly

### Open a new screen
```{BASH}
screen -S de_novo
```

#### Perform SPAdes denovo assembly on Quality trimmed data
```{BASH}
spades.py -k 21,51,71,91,111,127 --careful --pe1-1 Ch_R1_trim.fastq --pe1-2 Ch_R2_trim.fastq --pe1-s Ch_unpair_R1.fastq -o Ch_DSM_spades_output
```

#### Check Quality of the Assembly Using QUAST
```{BASH}
cd Ch_DSM_spades_output
quast contigs.fasta -o Ch_Quast_contigs
```

