#### [Unix Cheat Sheet](https://files.fosswire.com/2007/08/fwunixref.pdf)

# Command line activity

## Download file 
1. Download the [Loki_protein.fasta](https://raw.githubusercontent.com/stechtmann/BL2700/master/data/Loki_protein.fasta)  
1. Make a folder on your Desktop called BL2700_data
1. Place the Loki_protein.fasta in the BL2700_data folder.

** If using your own computer **
1. Log onto colossus
```{BASH}
cd Desktop
mkdir BL2700_data
cd BL2700_data
wget https://raw.githubusercontent.com/stechtmann/BL2700/master/data/Loki_protein.fasta
```

## Log on to colossus using putty

## Navigate to the BL2700_data
```{BASH}
cd Desktop
cd BL2700_data
```
## Look at the first few lines of this file.
```{BASH}
head Loki_protein.fasta
```

## Look at the last few lines of this file.
```{BASH}
tail Loki_protein.fasta
```

## List all of the sequence names from this genome
```{BASH}
grep ">" Loki_protein.fasta
```

## Count the number of sequences in this fasta file.
```{BASH}
grep -c ">" Loki_protein.fasta
```

## List all of the sequence names that are hypothetical proteins from this genome
```{BASH}
grep "hypothetical protein" Loki_protein.fasta
```

**On your own - Count all of the proteins that are hypothetical proteins.**

## Creat a file with all of the names of proteins with tranferase in the name
```{BASH}
grep "transferase" Loki_protein.fasta > T_names.txt
```
## Remove the `>` and gene IDs from the names of the transferase genes and save as a new file.
```{BASH}
cut -d ' ' -f 2- T_names.txt > T_only_names.txt
```

## Use `uniq` to determine if there are any duplicated genes in the same transerase group.
```{BASH}
uniq T_only_names.txt -c
```

## Sort the names of the transferases to be in order
```{BASH}
sort T_only_names.txt
```

## Use a pipe to connect the sort and uniq commands to determine if there are duplicate names
```{BASH}
sort T_only_names.txt | uniq -c
```
**How could you make this easier to identify which groups have more than one gene?**


**In groups - Use a series of pipe (`|`) to create a determine if which groups of dehydrogenases have more than one gene.**



