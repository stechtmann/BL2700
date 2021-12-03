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

Count table -
Metadata table -

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

**Laura I don't know if your data is all in csvs this is just a guess**

```{R}
counts<-read_csv("counts.csv")
metadata<-read_csv("metadata.csv")
taxa<-read_csv("taxa.csv")
```

# Laura Add next steps

