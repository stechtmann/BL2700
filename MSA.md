## Obtain the data
We're working with the [alkB sequence file](https://raw.githubusercontent.com/stechtmann/BL2700/master/data/alkB.txt)

## Obtain the R package `msa`

```{R}
BiocManager::install("msa")
library(msa)
library(Biostrings)
```

## Import the `.fasta` file for building the msa

```{R}
Sequences<-readAAStringSet("alkB.fasta.txt")
```

## Perform a ClustalW alignment

```{R}
myClustalWAlignment <- msa(Sequences, "ClustalW", verbose=TRUE)
```

## Perform a Muscle Alignment

```{R}
myMuscleAlignment <- msa(Sequences, "Muscle",verbose=TRUE)
```

## View the alignments

```{R}
print(myClustalWAlignment,show="complete",type="upperlower", thresh=c(50, 50))
print(myMuscleAlignment,show="complete",type="upperlower", thresh=c(50, 50))
```

## Save your alignment as a file
```{BASH}
sink("ClustalAlignment.aln")
print(myClustalWAlignment,show="complete",type="upperlower", thresh=c(50, 50))
sink()
```


## Alternative ways of printing MSA
It's possible to use a command in the msa package to print the MSA in pretty way that involves using a LaTeX compiler

```{R}
msaPrettyPrint(myClustalWAlignment, output="asis", y=c(164, 213),
               subset=c(1:6), showNames="none", showLogo="none",
               consensusColor="ColdHot", showLegend=FALSE,
               askForOverwrite=FALSE)
```
This will make a `.tex` file in the directory that can be compiled with a LaTeX compiler into a pretty image.
