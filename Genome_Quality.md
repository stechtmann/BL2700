## Install needed programs
#### Download
```{BASH}
conda create -n assembly -c bioconda -c conda-forge sra-tools fastqc=0.11.5 \
             trimmomatic=0.36 spades=3.11.1 quast=5.0.2 \
             bowtie2=2.2.5 prokka java-jdk=8.0.112 --yes
```

#### Activate
```{BASH}
conda activate assembly
```

## Set up files

#### Symbolic link to the scratch space on colossus
The size of the data files that we are using for the rest of the class are very large.  Because the amount of storage that we can us is limited, we are going to use a portion of colossus that has no limit on the amount of storage that is available. 
To more easly access this portion of colossus we will set up something called a symbolic link.  A symbolic link looks and appears as a normal directory or file, but refers to a directory/file somehwere else on the computer.  This is simlar to a short cut that may exist on your windows desktop
```{BASH}
ln -s /scratch_30_day_tmp/yourusername/ data
```

We are going to be downloading test data from the Short Read Archive (SRA). We are working with data from *Carboxydothermus hydrogenoformans*. *Carboxydothermus hydrogenoformans* is a thermophilic bacterium that is able generate energy from carbon monoxide (CO). It is also unclear if this organism has the ability to form spores as a form of surviving harsh conditions.  In this class we are going to sequence the genome to identify how many types of genes it has that are associated with carbon monoxide use and if there are genes associated with sporulation.

#### Download raw fastq files
```{BASH}
cd data
mkdir Genomics
cd Genomics
fastq-dump SRR4095642 -t ~/data
```

## Assess the quality of the raw reads

```{BASH}
fastqc SRR4095642_R1.fastq.gz
```

```{BASH}
fastqc SRR4095642_R2.fastq.gz
```

## Check the fastqc output using filezilla or WinSCP

## Trim low quality reads
