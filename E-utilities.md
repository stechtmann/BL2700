## Installing E-utilies

#### Navigate to your home directory
```{BASH}
cd ~
```

#### Install e-utilities
```{BASH}
/bin/bash
perl -MNet::FTP -e \
  '$ftp = new Net::FTP("ftp.ncbi.nlm.nih.gov", Passive => 1);
   $ftp->login; $ftp->binary;
   $ftp->get("/entrez/entrezdirect/edirect.tar.gz");'
gunzip -c edirect.tar.gz | tar xf -
rm edirect.tar.gz
builtin exit
export PATH=${PATH}:$HOME/edirect >& /dev/null || setenv PATH "${PATH}:$HOME/edirect"
./edirect/setup.sh
```

## Searching for particular entries in database

Search the pubmed database for hemoglobin

```{BASH}
esearch -db pubmed -query 'hemoglobin'
```

Search the nucleotide database for accession number XM_008768694.2
```{BASH}
esearch -db nucleotide -query 'XM_008768694.2'
```
## Fetching data from NCBI

Fetch the genbank file for accession number XM_008768694.2

```{BASH}
efetch -db sequences -format gb -id 'XM_008768694.2'
```

With e-utilities it's very easy to get the protein sequence for an mRNA

```{BASH}
efetch -db sequences -format fasta_cds_aa -id 'XM_008768694.2'
```

You can also use e-utilities to extract all of the protein sequences encoded for in a genome

```{BASH}
efetch -db sequences -format fasta_cds_aa -id 'FO203512.1'
```

## Combining commands

If you want to search for all of the sequences that match a query you can combine esearch and efetch

```{BASH}
esearch -db protein -query "Lokiarchaeota tubulin" | efetch -format fasta
```

