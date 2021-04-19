# Useful resources
[R for Data Science](https://r4ds.had.co.nz/)  
[R Graphics Cookbook](http://www.cookbook-r.com/Graphs/)  
[Best Practices for RNASeq](https://genomebiology.biomedcentral.com/articles/10.1186/s13059-016-0881-8)  
[Differential Gene Expression - F1000](https://f1000research.com/articles/4-1521/v2)  

# In class Exercise
### Download data
https://bioconnector.github.io/workshops/data/airway_scaledcounts.csv  
https://bioconnector.github.io/workshops/data/airway_metadata.csv  
https://bioconnector.github.io/workshops/data/annotables_grch38.csv  

## R Overview

### Set up a project
- set up a new R project.
- Open a new R script

### Basic calculations
- Add 3 + 5
```{R}
3+5
```
- Multiply 200 times 4000
```{R}
200*4000
```
### Variables
- create a variable `y` which is 1000
```{R}
y<-1000
```
- create a variable `x` which is 5000
```{R}
x<-5000
```
- Multiply your two variables together
```{R}
x*y
```

### Vectors
##### Create a numerical vector
```{R}
glengths <- c(4.6, 3000, 50000) 
glengths
```

##### Create a character vector
```{R}
species <- c("ecoli", "human", "corn") 
species
```
### Dataframes
##### Create a dataframe
```{R}
df <- data.frame(species, glengths)
```
### Lists
### Create a list of the species vector, the dataframe, and the variable y
```{R}
list<- list(species, df, y)
list
```

### Installing packages
```{R}
install.packages(“readr”)
install.packages(“tidyverse”)
if (!requireNamespace("BiocManager", quietly = TRUE)) install.packages("BiocManager") 
BiocManager::install("DESeq2")
```
## Differential Abundance Activity

### Access your libraries
```{R}
library(readr)
library(tidyverse)
library(DESeq2)
```

### importing data into R.
```{R}
mycounts <- read_csv("data/airway_scaledcounts.csv") 
metadata <- read_csv("data/airway_metadata.csv")
anno <- read_csv("data/annotables_grch38.csv")
```
### Examine data
```{R}
head(mycounts)
head(metadata)
```
Determine the class of the object
```{R}
class(mycounts)
```

### Prepare data for DESeq
```{R}
mycounts <- as.data.frame(mycounts)
metadata <- as.data.frame(metadata)
```
Check that the column names of our count data (except the first, which is ensgene) are the same as the IDs from our colData
```{R}
names(mycounts)[-1]
metadata$id
names(mycounts)[-1]==metadata$id
all(names(mycounts)[-1]==metadata$id)
```
### Create DESeq Object
```{R}
dds.data <- DESeqDataSetFromMatrix(countData=mycounts, 
                              colData=metadata, 
                              design=~dex, 
                              tidy=TRUE)
dds.data
```

### Run DESeq
```{R}
dds <- DESeq(dds.data)
```

### Gather the results
```{R}
res <- results(dds, contrast=c("dex","control","treated"), tidy=TRUE)
res <- tbl_df(res)
res
```
### Work with the `res` file
```{R}
res2 <- arrange(res, padj)
```
How many significantly different genes?

```{R}
res3 <- filter(res2, padj<=0.05 & log2FoldChange>=2)
res4 <- filter(res2, padj<=0.05 & log2FoldChange<=-2)
```

### Join the annotations with results file
```{R}
res.anno <- res2 %>%
  inner_join(anno, by=c("row"="ensgene"))
```

# Plotting

# Basic plotting

DESeq has a basic plotting function `plotCounts` which allows you to plot the expression of a particular gene
```{R}
plotCounts(dds, gene="INSERTrowID", intgroup="dex")
```

You can return the raw data for this gene by adding `returnData=TRUE`
```{R}
plotCounts(dds, gene="INSERTrowID", intgroup="dex", returnData=TRUE)
```

This is not the prettiest plot.  Using ggplot2 you have much greater control over what is plotted and make many different types of plots

Example plot
```{R}
plotCounts(dds, gene="INSERTrowID", intgroup="dex", returnData=TRUE) %>%
  ggplot(aes(dex,count))+
  geom_boxplot(aes(fill=dex))+
  scale_y_log10() + 
  ggtitle("INSERT GENE NAME") 
```
### More fun with plotting PCA

Before plotting a PCA you must transform your count data:  Log transformation is often a common transformation to prepare data for PCA.

```{R}
rld=rlog(dds)
```
This is a basic PCA command
```{R}
plotPCA(rld, intgroup = "dex")
```
To have more control of of the plot you can extract the needed information  

This command extracts the PCA coordinates
```{R}
pcaData <- plotPCA(rld, intgroup=c("dex"), returnData=TRUE)
```
This command extracts the percent variance explained by each axis
```{R}
percentVar <- round(100 * attr(pcaData, "percentVar"))
```

This takes that data and uses ggplot to make a prettier plot
```{R}
ggplot(pcaData, aes(PC1, PC2, color=dex, shape=dex)) +
  geom_point(size=3) +
  xlab(paste0("PC1: ",percentVar[1],"% variance")) +
  ylab(paste0("PC2: ",percentVar[2],"% variance")) + 
  coord_fixed()+
  theme_bw()
```
