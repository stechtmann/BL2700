# Overview 
In the next two classes we will look at how to use sequencing data along with statistics to understand microbial community composition.

As part of this class we will be working in RStudio again.

Open up RStudio and make a new script for this work.

# Install packages
Make sure to install the following packages prior to class on Wed Dec. 8

```{R}
install.packages(csv)
install.packages(vegan)
BiocManager::install("phyloseq")
```

# Download data

You will need to download the following data items.

Count table - https://github.com/stechtmann/BL2700/raw/master/data/seqtab.nochim.rds
Metadata table - https://raw.githubusercontent.com/stechtmann/BL2700/master/data/samples.transit.csv
Taxa table - https://github.com/stechtmann/BL2700/raw/master/data/taxa.rds

# Intialize environment 

Initialize all required packages

```{R}
library(tidyverse)
library(csv)
library(vegan)
library(phyloseq)
```
# Read data into your environment
Read your data in to your R environment.

## Set your working director to the directory where the data is found.
- Session
- Set working directory
- Choose Directory

```{R}
counts<-readRDS("seqtab.nochim.rds")
metadata<-read_csv("samples.transit.csv")
taxa<-read_csv("taxa.rds")
```

# Laura Add next steps

