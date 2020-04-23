## Review
Data from DESeq

BaseMean - Average Expression
Log2foldChange - log base 2 of the ratio of expression between two different treatment.
pvalue - significance of difference between two conditions
adj.pvalue - pvalue adjusted for multiple comparisons.

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
