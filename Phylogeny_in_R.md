# In Class Activity.

## Download the data
We're working with a data set of alkane monooxygenase genes.  The fasta file can be found [here](https://raw.githubusercontent.com/stechtmann/BL2700/master/data/alkB2.fasta)

## Install packages in R

We're going to be using the msa, Biostrings, phangorn, ape, and sequinr packages
```R
if (!requireNamespace("BiocManager", quietly = TRUE))
  install.packages("BiocManager")
BiocManager::install("Biostrings")
BiocManager::install("msa")
install.packages("ape")
install.packages("phangorn")
install.packages("seqinr")
```

## Initialize the installed packages
```R
library(ape)
library(phangorn)
library(seqinr)
library(msa)
library(tidyverse)
```

## Import data into R
```R
alkB<-readAAStringSet("alkB2.fasta")
```

## Perform multiple sequence alignment

### Perform alignment

```R
alkBMuscleAlignment <- msa(alkB, "Muscle",verbose=TRUE)
```
### Convert into a type of data that can work with phangorn
```R
alkB_phyDat <- as.phyDat(alkBMuscleAlignment,"AA")
```

## Perform NJ phylogeny

### Calculate distance for each sequence 
```R
aa_dist <- dist.ml(alkB_phyDat, model="LG")
```
### Construct NJ tree
```R
alkB_NJ  <- NJ(aa_dist)
```
### Perform bootstrapping

#### Set up the function for bootstrapping
```R
fun <- function(x) NJ(dist.ml(x))
```

#### Perform bootstrapping
```R
bs_nj <- bootstrap.phyDat(alkB_phyDat,bs=1000, fun)
```

#### Plot bootstrapped tree

```R
plotBS(alkB_NJ,type="phylogram", bs_nj, main="Neighbor Joining")
```

## Perform Maximum Likelihood Phylogeny

### Determine the best model and parameters for maximum likelihood
```R
mt <- modelTest(alkB_phyDat,model = c("WAG", "JTT", "LG", "Dayhoff", "cpREV", "mtmam", "mtArt", "MtZoa", "mtREV24", "VT", "RtREV", "HIVw", "HIVb", "FLU", "Blosum62", "Dayhoff_DCMut", "JTT_DCMut"))
```

### Observe the output from model test
```R
print(mt)
```

### Sort the output based on Akaike information criterion (AIC)
```R
mt%>%
  arrange(AICc)
```

### Determine the best model based on AICc
```R
bestmodel <- mt$Model[which.min(mt$AICc)]
```

### Create an environmental variable with the model test output
```R
env <- attr(mt, "env")
```

### Get a starting point for construction of the ML tree model
```R
fitStart <- eval(get(bestmodel, env), env)
```
### Use the parameters determined to fit the model to the data from the sequences
```R
fit <- optim.pml(fitStart, rearrangement = "stochastic",
                 optGamma=TRUE, optInv=TRUE, model="LG")
```

### Perform bootstrapping on the fit maximium likelihood tree

```R
bs <- bootstrap.pml(fit, bs=100, optNni=TRUE, multicore=TRUE)
```
If using a Windows computer remove `multicore=TRUE`

### Plot the boostrapped tree
```R
plotBS(midpoint(fit$tree), bs, p = 50, type="phylogram")
```

#### You can look at all of the bootstrapped trees individually
```R
plot(bs)
```

