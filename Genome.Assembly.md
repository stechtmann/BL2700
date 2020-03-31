## Data
Use the quality filtered data from the quality filtering lab day.

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
bowtie2 -x C.hydro -1 Ch_pair_R1.fastq -2 Ch_pair_R2.fastq -S DSM.sam
```

## De novo genome assembly

#### Perform SPAdes denovo assembly on Quality trimmed data
```{BASH}
spades.py -k 21,51,71,91,111,127 --careful --pe1-1 Ch_pair_R1.fastq --pe1-2 Ch_pair_R2.fastq --pe1-s Ch_unpair_R1.fastq -o Ch_DSM_spades_output
```

#### Check Quality of the Assembly Using QUAST
```{BASH}
cd Ch_DSM_spades_output
quast contigs.fasta -o G15_Quast_contigs
```
