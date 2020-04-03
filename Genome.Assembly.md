## Data
Use the quality filtered data from the quality filtering lab day.

```{BASH}
cd data/Genomics
```

Because the files that we are using are so large, it would take about 30 minutes to run each of these assembly commands.  In order to demonstrate these commands in a way that doesn't take a very long time, we will only perform these on a subset of the reads.

We will use the first million reads from each of the quality filtered R1 and R2 files.  **Note: You should not do this for the reads from the genomes that you've been given as this is throwing out useful data.  We are just doing this for the sake of time for class**

```{BASH}
head -n 4000000 Ch_pair_R1.fastq > Ch_R1_trim.fastq
```
Do the same with the R2 file
```{BASH}
head -n 4000000 Ch_pair_R2.fastq > Ch_R2_trim.fastq
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
bowtie2-build C.hydrogenoformans_Z2901.fasta C.hydro
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

