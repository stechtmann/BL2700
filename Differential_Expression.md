# Useful resources
[R for Data Science](https://r4ds.had.co.nz/)
[R Graphics Cookbook](http://www.cookbook-r.com/Graphs/)

# In class Exercise
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
