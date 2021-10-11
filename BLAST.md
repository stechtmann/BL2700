# Overview 
Today we're going to use R to make a blastable database from the list of protein sequences from a genome and then blast a set of genes involved in oil biodegradation against that database.

## Install required packages

This R package is one that has been written by a researcher but has not been deposited in either CRAN or Bioconductor.  To install this package we need to use developers tools.

```{R}
install.packages("devtools")
devtools::install_github("mhahsler/rBLAST")
```

You also need download the blast tools that are distributed by NCBI

1. Go to the blast tools [website](https://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/LATEST/).  Download the appropriate distribution for your operating system.  
2. Unzip the distribution
3. Note where you installed the package 

You will need to tell R where the blast programs are 

```{R}
Sys.setenv(PATH = paste(Sys.getenv("PATH"), "PATH_TO/ncbi-blast-2.12.0+/bin/", sep= .Platform$path.sep))
```
Check to see if you've install the BLAST programs correctly

```{R}
Sys.which("blastn")
```

## Initialize the required packages
```{R}
library(rBLAST)
library(tidyverse)
```

## Download the data for today's activity

## Make a blastable database
```{R}
makeblastdb("Oleispira_antarctica.fasta", dbtype="prot")
bl <- blast(db="Oleispira_antarctica.fasta",type="blastp")
```

## Import the the query data into R

```{R}
search<-readAAStringSet("hydrocarbons.txt")
```

## perform a BLAST search

```{R}
cl<-predict(bl,search)
```

## Examine the output
```{R}
cl
```

## Use dplyr to get only the useful portions from the `cl` dataframe 

```{R}
cl %>%
filter(E<0.05)
```


