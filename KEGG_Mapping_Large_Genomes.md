If your genome has more than 2500 genes, the KEGG Mapper will not accept the full genome.  To work around this, you can split the fasta file into two files and then run KEGG Mapper on both files.  Then concatenate the output and reconstruct the full metabolic model.  Follow the below steps for this.

## Add pyfasta to your assembly environment in conda

Log on to colossus.

```{BASH}
conda install -c bioconda pyfasta -n assembly
conda activate assembly
```

## Use pyfasta to split the amino acid sequence fasta file (.faa) into two files of equal size

```{BASH}
pyfasta split -n 2 inputfile.faa
```
Then transfer both files to your computer.

## Perform KEGG Mapping

1. Navigate to [KEGG Mapper](https://www.kegg.jp/kegg/tool/annotate_sequence.html)
1. Upload your first sequence file
1. Select Pseudoalteromonas as your Family/Genus
1. Execute the mapping.
1. Under Annotation Data select download.
1. Name the file appropriately

Perform the same steps for the second file

## Combine the output files.

1. Open the first downloaded file in Notepad or TextEdit
1. Open the second file in Notepad or TextEdit
1. Copy the contents of the second file and paste them at the bottom of the first file.
1. Save this file as a new file.

## Construct the Meatbolic Map

1. Navigate to the [KEGG Reconstruct pathway program](https://www.genome.jp/kegg/tool/map_pathway.html)
1. Upload the combined file from the previous step.
1. Execute.

