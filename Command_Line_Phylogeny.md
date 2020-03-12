## Install important packages for all command line phylogeny activities.
```{BASH}
conda create -n phylogeny -c bioconda -c conda-forge clustalo quicktree \
             muscle phyml modeltest-ng
```

## Get the data
```{BASH}
wget https://raw.githubusercontent.com/stechtmann/BL2700/master/data/Group_III_CPN.txt
```

## Activate the conda environment
```{BASH}
conda activate phylogeny
```

## Construct a Neighbor joining tree
### Perform a muscle alignment with Clustal and output as a stockholm format
```{BASH}
clustalo -i Group_III_CPN.txt -o Clustal.st --outfmt st
```

### Construct a neighbor joining tree with 100 bootstraps
```{BASH}
quicktree -boot 1000 Clustal.st > test.nwk
```

### Visualize the tree
Use WinSCP to transfer your file to your local computer and visualize on phylogeny.fr

## Construct a Maximum likelihood tree
### Learn the best model for the data that we're using
```{BASH}
modeltest-ng -i Clustal.fa -o Clustal_models -t ml -d aa -p 5 
```

### Construct a maximum likelihood tree with the appropriate model



