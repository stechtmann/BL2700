# Pairwise alignments

## Install the Biostrings package
```{R}
if (!requireNamespace("BiocManager", quietly = TRUE))
  install.packages("BiocManager")

BiocManager::install("Biostrings")
```

## Download data

The data for this activity you can get from [here](https://raw.githubusercontent.com/stechtmann/BL2700/master/data/Hemoglobins.fasta)

## Import your data into R
Don't forget to set your working directory.
```{R}
library(Biostrings) # Initialize your libraries
Sequences <- readAAStringSet("Hemoglobins.fasta") # read in your data
```

## Perform the pairwise alignment

```{R}
align<-pairwiseAlignment(Sequences[1,], Sequences[2,],
                  substitutionMatrix = "BLOSUM50",
                  gapOpening = 0, gapExtension = 8, 
                  type="global")
```

You need to do the following
1. Specify the two input sequences from your fasta.  The numbers correspond to the order in fasta file.
2. `substitutionMatrix = "BLOSUM50"` - This specifies the substitutio nmatrix for the scoring.
3. `gapOpening = 0, gapExtension = 8` - This specifies the cost to open and extend a gap
4. `type=global` - This specifies if it's a global or a local alignment

## View your alignment
You can view a summary of your alignment by typing in 
```{R}
align
```
This summary will include the score of your alignment

You can also output the alignment as a text file
```{R}
writePairwiseAlignments(align,"out.txt")
```
Make sure to note.
1. The score
2. The number of gaps
3. The number of identical sequences in your alignment.
