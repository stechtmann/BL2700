## Create a new conda environment
```{BASH}
conda create -n transcriptomics -c bioconda fastqc=0.11.5 trimmomatic=0.36 samtools salmon
```


## Download the needed data

### Download the raw reads
```{BASH}
wget https://github.com/stechtmann/BL2700/raw/master/data/D.m.rep.tar.gz
tar xvf D.m.rep.tar.gz
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
trimmomatic SE D.m.rep1.fastq D.m.rep1_trim.fastq ILLUMINACLIP:~/miniconda3/pkgs/trimmomatic-0.36-6/share/trimmomatic-0.36-6/adapters/TruSeq2-SE.fa:2:30:10:2 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
```

##  Create an index of your reference transcriptome
```{BASH}
salmon index -t GCF_000001215.4_Release_6_plus_ISO1_MT_rna.fna -i D.melanogaster
```

```{BASH} 
salmon quant -i D.melanogaster -l A -r D.m.rep1_trim.fastq --validateMappings -o quants_rep1
```


