## Install important packages for all command line phylogeny activities.
```{BASH}
conda create -n phylogeny -c bioconda -c conda-forge clustalo quicktree \
             muscle phyml modeltest-ng fasttree
```

## Navigate to the appropriate directory and make a new directory for today's work.
```{BASH}
cd Desktop/BL2700
mkdir trees
cd trees
```

## Get the data
```{BASH}
wget https://raw.githubusercontent.com/stechtmann/BL2700/master/data/HSP20.fasta
```

## Activate the conda environment
```{BASH}
conda activate phylogeny
```

## Construct a Neighbor joining tree
### Perform a muscle alignment with Clustal and output as a stockholm format
```{BASH}
clustalo -i HSP20.fasta -o Clustal.st --outfmt st
```

### Construct a neighbor joining tree with 1000 bootstraps
```{BASH}
quicktree -boot 1000 Clustal.st > test.nwk
```

### Visualize the tree
Use WinSCP or FileZilla to transfer your file to your local computer and visualize on phylogeny.fr

To use these programs you will need to enter the following information.
  - Server: colossus.it.mtu.edu
  - Username: Your MTU username
  - Password: Your MTU password
  - Port: 22

## Construct a Maximum likelihood tree

### Use Muscle to create a multiple sequence alignment
```{BASH}
muscle -in HSP20.fasta -out HSP20_Muscle.fa -phyiout HSP20_phyi.out -physout HSP20_phys.out
```
### Learn the best model for the data that we're using
```{BASH}
modeltest-ng -i HSP20_phys.out -o HSP20_models -t ml -d aa 
```
### Construct a maximum likelihood tree with the appropriate model
```{BASH}
phyml -i HSP20_phyi.txt -d aa -m XXXXX -f XXXX -v XXXX -a XXXX -c XXX -o XXXX 
```
This is an example command for the maximum likelihood tree building method
- `phyml` is the command to be run
- `-i` specifies the input alignment (this must be an interleved phylip format)
- `-d` specifies the data type (in this case we have amino acids so it shoud be aa)
- `-m` specifies the model determined by modeltest to be the one with the best score (log likelihood (lnL) and AIC).
- `-f` this parameter species the how the frequency of amino acids should be considered.  This could be determined emperically(`e`) by looking at the data supplied or through modeling the data (`m`).
- `v` this parameter specifies the proportion of invariable sites. This could be a fixed value or be estimated by phyml based on the data (`e`)
- `a` this is the shape parameter for the gamma distribution.  By including this in the command we are using the gamma distribution in our model.
- `c` number of relative substitution rate categories.
- `o` This parameter indicates what should be optimized by the maximum likelihood model.
  - params=tlr : tree topology (t), branch length (l) and rate parameters (r) are optimised.
  - params=tl  : tree topology and branch length are optimised.
  - params=lr  : branch length and rate parameters are optimised.
  - params=l   : branch length are optimised.
  - params=r   : rate parameters are optimised.
  - params=n   : no parameter is optimised.


### Construct a maximum likelihood tree with the appropriate model and fast aproximate likelihood support (SH-aLRT) for branches
```{BASH}
phyml -i HSP20_phyi.txt -d aa -m LG -f e -v e -a e -c 4 -o tlr -b -4
```
### Construct the maximum likelihood tree with SH support using fasttree

### Use Muscle to create a multiple sequence alignment
```{BASH}
muscle -in HSP20.fasta -out HSP20_Muscle.aln 
```
```{BASH}
fasttree -wag -gamma HSP20_Muscle.aln > HSP20_fasttree.tre
