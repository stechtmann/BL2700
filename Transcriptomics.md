## Create a new conda environment
```{BASH}
conda create -n transcriptomics -c bioconda fastqc=0.11.5 trimmomatic=0.36 samtools salmon
```


## Download the needed data

### Make new directory

```{BASH}
cd ~/data/
mkdir Transcriptomics
cd Transcriptomics
```

### Download the raw reads
```{BASH}
wget https://github.com/stechtmann/BL2700/raw/master/data/Gravity.tar.gz
tar xvf Gravity.tar.gz
```

### Download the reference transcriptome
```{BASH}
wget ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/001/215/GCF_000001215.4_Release_6_plus_ISO1_MT/GCF_000001215.4_Release_6_plus_ISO1_MT_rna.fna.gz
gunzip GCF_000001215.4_Release_6_plus_ISO1_MT_rna.fna.gz
```

### Download the annotation file (`.gff`)
```{BASH}
wget ftp://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/001/215/GCF_000001215.4_Release_6_plus_ISO1_MT/GCF_000001215.4_Release_6_plus_ISO1_MT_genomic.gff.gz
gunzip GCF_000001215.4_Release_6_plus_ISO1_MT_genomic.gff.gz
```

## Activate your environment
```{BASH}
conda activate transcriptomics
```

## Trim your data
```{BASH}
trimmomatic SE D.mel.rep1.fastq trim.D.mel.rep1.fastq ILLUMINACLIP:~/miniconda3/pkgs/trimmomatic-0.36-6/share/trimmomatic-0.36-6/adapters/TruSeq2-SE.fa:2:30:10:2 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
```

##  Create an index of your reference transcriptome
```{BASH}
salmon index -t GCF_000001215.4_Release_6_plus_ISO1_MT_rna.fna -i D.melanogaster
```

```{BASH} 
salmon quant -i D.melanogaster -l A -r trim.D.mel.rep1.fastq --validateMappings -o quants_rep1
```

## Repeat for all of the other files in your data set

This is the challenge and where for loops come in handy.  

A for loop is a logical expression that will help you perform the same process over and over again.

A simple for loop would be to just count how many sequences are in our files.  

This can start with understanding how you do this with one file

```{BASH}
grep -c '@SRR' D.mel.rep1.fastq
```

Now we can write a for loop to do this for all of our input data.

```{BASH}
for file in D.mel.rep*
do
grep -c '@SRR' $file
done
```

Now we can write a for loop for the trimming function.

```{BASH}
for file in D.mel.rep*
do
trimmomatic SE $file trim.$file ILLUMINACLIP:~/miniconda3/pkgs/trimmomatic-0.36-6/share/trimmomatic-0.36-6/adapters/TruSeq2-SE.fa:2:30:10:2 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
done
```
Now we can write a for loop for the mapping and quantifying
```{BASH}
for file in trim.D.mel*
do
salmon quant -i D.melanogaster -l A -r $file --validateMappings -o quants_$file
done
```



