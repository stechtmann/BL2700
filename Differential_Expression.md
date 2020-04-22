# Useful resources
[R for Data Science](https://r4ds.had.co.nz/)
[R Graphics Cookbook](http://www.cookbook-r.com/Graphs/)

## Installing packages
```{R}
install.packages(“readr”)
install.packages(“tidyverse”)
if (!requireNamespace("BiocManager", quietly = TRUE)) install.packages("BiocManager") 
BiocManager::install("DESeq2")
```
## Access your libraries
```{R}
library(readr)
library(tidyverse)
library(DESeq2)
```

## importing data into R.
```{R}
mycounts <- read_csv("DEG/airway_scaledcounts.csv") 
metadata <- read_csv("data/airway_metadata.csv")
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
res <- results(dds, tidy=TRUE)
res <- tbl_df(res)
res
```
### Work with the `res` file
```{R}
res2 <- arrange(res, padj)
```
How many significantly different genes?

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
