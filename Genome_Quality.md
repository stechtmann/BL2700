## Install needed programs
#### Download
```{BASH}
conda create -n assembly -c bioconda -c conda-forge sra-tools fastqc=0.11.5 \
             trimmomatic=0.36 spades=3.11.1 quast=5.0.2 \
             bowtie2=2.2.5 prokka java-jdk=8.0.112 --yes
```
#### Download the file to retrieve your sequencing files from google drive

#### Activate
```{BASH}
conda activate assembly
```

#### Download gdown
```{BASH}
pip install gdown
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
mkdir Quality
cd Quality
gdown https://drive.google.com/uc?id=17GXqsTYY7JGi92Ijuz5qGELdMJaGMYgK
gdown https://drive.google.com/uc?id=1YnsR_zdZ9aqkIaODCk242hXdiz82RWyK
```

## Assess the quality of the raw reads

```{BASH}
fastqc C.hydro_DSMZ_R1.fastq.gz
```

```{BASH}
fastqc C.hydro_DSMZ_R2.fastq.gz
```

## Check the fastqc output using filezilla or WinSCP
You will need to open WinSCP and either transfer the files to your desktop or click on the .html file then view/edit


# Start of second day

## Log in and activate your environment
Log into colossus.
Open Filezilla

Activate your environment
```{BASH}
conda activate assembly
```
Navigate to your data
```{BASH}
cd data/Genomics/Quality
```

## Trim low quality reads

To trim low quality reads we're going to use a sliding window trimmer known as trimmomatic.

To set up this command you have to modify the `ILLUMINACLIP` path to do this run
```{BASH}
pwd
```
On my login the following is returned
```{BASH}
/home/campus14/smtechtm/data/Genomics/Quality
```
The first part is the path to you home directory
```{BASH}
/home/campus14/smtechtm/
```
yours should look something like this but the campus number and your username should be different.

In the commmand below replace the 'PATHTOHOME' with the path to your home directory.

```{BASH}
trimmomatic PE C.hydro_DSMZ_R1.fastq.gz C.hydro_DSMZ_R2.fastq.gz Ch_pair_R1.fastq Ch_unpair_R1.fastq Ch_pair_R2.fastq Ch_unpair_R2.fastq ILLUMINACLIP:PATHTOHOME/miniconda3/pkgs/trimmomatic-0.36-6/share/trimmomatic-0.36-6/adapters/NexteraPE-PE.fa:2:30:10:2 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
```
`trimmomatic` - name of the command
`PE` - paired end data (every molecule of DNA is sequenced from both ends)
input - Forward (R1) and Reverse (R2) reads
output - there are four outputs
  1. forward paired
  1. forward unpaired - no high quality reverse reads  
  1. reverse paired   
  1. reverse unpaired - no high quality forward reads  

`ILLUMINACLIP` - Removes sequences that were added as part of the library preparation that are not part of the genome being sequenced needs the path to fasta file that contains the sequence for the adaptors used.  
`LEADING` - removes the first bases for each read  
`TRAILING` - removes the last bases from the each read  
`SLIDINGWINDOW` - Quality trimming for each read 4:15 (window size of four basepairs and average quality cut off of 15)  
`MINLEN` - minimum length of the read  

## Check the quality of trimmed reads with fastq

```{BASH}
fastqc Ch_pair_R1.fastq
```
