## Review
Data from DESeq

BaseMean - Average Expression  
Log2foldChange - log base 2 of the ratio of expression between two different treatment.  
pvalue - significance of difference between two conditions.  
adj.pvalue - pvalue adjusted for multiple comparisons.  

## Handeling Data with dplyr

```{R}
library(tidyverse)
library(DESeq2)
```
### Filter
Determine the number of significantly different genes using the tidyverse `filter` command.  `filter` is used to separate out parts of a dataframe that match a criteria

```{R}
res_sig<-filter(res, padj<=0.05)
```

### Mutate
Label genes that are significantly different using the tidyverse `mutate` command.  `mutate` is used to add a column to your dataframe and populate that column with data.  `ifelse` is used to specify a logical statement.

```{R}
res2<-mutate(res, ifelse(padj<=0.05, "significant", "non-significant")))
```

### Pipes and Summarize
```{R}
res %>%
  filter(log2FoldChange>=2)%>%
  summarise(mean(log2FoldChange))
```

## Basic plotting

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
## Volcano Plots

```{R}
ggplot(res)+
  geom_point(aes(x=log2FoldChange, y=-log10(padj)))
```

### Pettier Volcano Plot and handeling data with dplyr

```{R}
ggplot(res2)+
  geom_point(aes(x=log2FoldChange, y=-log10(padj),color=significance))
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
