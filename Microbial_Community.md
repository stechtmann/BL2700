# Overview 
In the next two classes we will look at how to use sequencing data along with statistics to understand microbial community composition.

As part of this class we will be working in RStudio again.

Open up RStudio and make a new script for this work.

# Install packages
Make sure to install the following packages prior to class on Wed Dec. 8

```{R}
install.packages("csv")
install.packages("vegan")
BiocManager::install("phyloseq")
```

# Download data

You will need to download the following data items.

Count table - https://github.com/stechtmann/BL2700/raw/master/data/seqtab.nochim.rds
Metadata table - https://raw.githubusercontent.com/stechtmann/BL2700/master/data/samples.transit.csv
Taxa table - https://github.com/stechtmann/BL2700/raw/master/data/taxa.rds

# Intialize environment 

Initialize all required packages

```{R}
library(tidyverse)
library(csv)
library(vegan)
library(phyloseq)
```
# Read data into your environment
Read your data in to your R environment.

## Set your working director to the directory where the data is found.
- Session
- Set working directory
- Choose Directory

## Read in data into your environment
```{R}
counts<-readRDS("seqtab.nochim.rds")
metadata<-read_csv("cb_metadata4.csv")
taxa<-read_csv("taxa.rds")
```

# Prepare data for phyloseq

## metadata
```{R}
head(metadata)
metadata2 <- metadata %>%
  mutate(SampleID = sampleid) %>%
  column_to_rownames(var = "sampleid") 
head(metadata2)
```

## sequence table
```{R}
counts[1:5,1:5]
colnames(counts) <- NULL
counts2 <- as.matrix(counts)
counts2[1:5,1:5]
```

## taxa table
```{R}
taxa_table[1:5,1:5]
taxa_t<-t(taxa)
colnames(taxa_t) <- NULL
taxa_table<-t(taxa_t)
taxa_table <- as.matrix(taxa_table)
taxa_table[1:5,1:5]
```

# Make sure data dimensions match up
```{R}
dim(metadata2)
dim(counts2)
dim(taxa)
```

# Format data for phyloseq
```{R}
samdata = sample_data(metadata2)
seqtab = otu_table(counts2, taxa_are_rows = FALSE)
taxtab = tax_table(taxa_table)
```

# Make the phyloseq object
```{R}
ps = phyloseq(otu_table(seqtab), tax_table(taxtab), sample_data(samdata))
ps
```

# Normalize the data
```{R}
samplesover1000_all <- subset_samples(ps, sample_sums(ps) > 1000)
any(taxa_sums(samplesover1000_all) == 0)
sum(taxa_sums(samplesover1000_all) == 0)
prune_samplesover1000_all <- prune_taxa(taxa_sums(samplesover1000_all) > 0, samplesover1000_all)
any(taxa_sums(prune_samplesover1000_all) == 0)
set.seed(81)
rarefy_samplesover1000_all <- rarefy_even_depth(prune_samplesover1000_all, rngseed= 81, sample.size = min(sample_sums(prune_samplesover1000_all)))
pps<- rarefy_samplesover1000_all
```

# Look at how many samples of each type we have
```{R}
count_samples1 <- sample_data(pps) %>%
  group_by(VARIABLE, VARIABLE) %>%
  summarise(
    count = n()
  )
View(count_samples1)
```

# Let's compare the microbial community of the bilge water and the bilge biofilm of the boat

## subset the phyloseq object
```{R}
sub_ps <- subset_samples(pps, swab_type == "bilgewater"|swab_type == "bilgebiofilm")
sub_ps
```

# Save a vector of colors
```{R}
sample_colors <- c("#CBD588", "#5F7FC7")
```

# Make a violin plot of alpha diversity
```{R}
sub_ps %>%                                                              #phyloseq object
  plot_richness(
    x = "swab_type",                                                    #compare diversity of datatype
    measures = c("Observed")) +                                         #choose diversity measures
  geom_violin(aes(fill = swab_type), show.legend = FALSE)+              #make violin plot, set fill aes to sampletype
  geom_boxplot(width=0.1) +                                             #add boxplot, set width
  theme_classic()+                                                      #change theme to classic
  scale_fill_manual(values = sample_colors)+                            #set fill colors
  ggtitle("Alpha Diversity") +                                          #add title
  theme(plot.title=element_text(size = 25, face = "bold", hjust = 0.5)) #change title size, face and position
```
# Alpha diversity statistics

## create data frame for statistics
```{R}
richness <- sub_ps %>%
  estimate_richness(measures = c("Observed")) %>%           #specify which measures
  rownames_to_column(var = "SampleID") %>%                  #add column name to SampleID column
  left_join(metadata2, by = "SampleID")
head(richness)
```
## perform Kruskal Wallis test
```{R}
kruskal.test(Observed ~ swab_type, data = richness) 
```


# Make a PCoA Plot

## make ordination
```{R}
ord <- ordinate(
  physeq = sub_ps, 
  method = "PCoA", 
  distance = "bray"
)
```
# Make the plot
```{R}
plot_ordination(
  physeq = sub_ps,                                                  #phyloseq object
  ordination = ord)+                                                #ordination
  geom_point(aes(fill = swab_type), size = 5, shape = 21) +         #sets fill color to sampletype
  scale_fill_manual(values = sample_colors) +                       #specify colors
  theme_classic()                                                   #changes theme, removes grey background
```

# PERMANOVA
```{R}
bray <- phyloseq::distance(sub_ps, method = "bray")
sam <- data.frame(sample_data(sub_ps))
adonis2(bray ~ swab_type, data = sam)
```


# Make a taxa plot

## Remove mitochondria and chloroplasts from the data set
```{R}
justbacteria <- sub_ps %>%
  subset_taxa(
    Kingdom == "Bacteria" &                   #only bacteria
      Family  != "mitochondria" &             #filter out mitochondria
      Class   != "Chloroplast"                #filter out chloroplasts
  )
justbacteria
```

## Convert phyloseq object into a data frame
```{R}
phylumabundance <- ps %>%
  tax_glom(taxrank = "Phylum") %>%                     # agglomerate at phylum level
  transform_sample_counts(function(x) {x/sum(x)} ) %>% # Transform to rel. abundance
  psmelt() %>%                                         # Melt to long format
  arrange(Phylum) 
head(phylumabundance)
```

## Summarize the most abundant taxa
```{R}
taxa_plot_data <- phylumabundance %>%
  dplyr::select(Phylum, Abundance, swab_type) %>%
  group_by(Phylum, swab_type) %>%
  summarise(
    avg_abundance = mean(Abundance)
  ) %>%
  mutate(
    Phylum = as.character(Phylum),
    Phylum = ifelse(avg_abundance < 0.02, "< 2 %", Phylum)
  ) %>%
  group_by(Phylum, swab_type) %>%
  summarise(
    avg_abundance = sum(avg_abundance)
  )
head(taxa_plot_data)
```

## Make the plot

## save a vector of colors
```{R}
phylum_colors <- c(
  "#CBD588", "#5F7FC7", "orange","#DA5724", "#508578", "#CD9BCD",
  "#AD6F3B", "#673770","#D14285", "#652926", "#C84248", "blue",
  "#8569D5", "#5E738F","#D1A33D", "#8A7C64", "#599861", "#CBD588", "#5F7FC7", "orange","#DA5724", "#508578", "#CD9BCD",
  "#AD6F3B", "#673770","#D14285", "#652926", "#C84248", 
  "#8569D5", "#5E738F","#D1A33D", "#8A7C64", "#599861", "#CBD588", "#5F7FC7", "orange","#DA5724", "#508578", "#CD9BCD",
  "#AD6F3B", "#673770","#D14285", "#652926", "#C84248", "blue",
  "#8569D5", "#5E738F","#D1A33D", "#8A7C64", "#599861", "#CBD588", "#5F7FC7", "orange","#DA5724", "#508578", "#CD9BCD",
  "#AD6F3B", "#673770","#D14285", "#652926", "#C84248", 
  "#8569D5", "#5E738F","#D1A33D", "#8A7C64", "#599861",
  "#CBD588", "#5F7FC7", "orange","#DA5724", "#508578", "#CD9BCD",
  "#AD6F3B", "#673770","#D14285", "#652926", "#C84248", "blue",
  "#8569D5", "#5E738F","#D1A33D", "#8A7C64", "#599861", "#CBD588", "#5F7FC7", "orange","#DA5724", "#508578", "#CD9BCD",
  "#AD6F3B", "#673770","#D14285", "#652926", "#C84248", 
  "#8569D5", "#5E738F","#D1A33D", "#8A7C64", "#599861","#CBD588", "#5F7FC7", "orange","#DA5724", "#508578", "#CD9BCD",
  "#AD6F3B", "#673770","#D14285", "#652926", "#C84248", "blue",
  "#8569D5", "#5E738F","#D1A33D", "#8A7C64", "#599861", "#CBD588", "#5F7FC7", "orange","#DA5724", "#508578", "#CD9BCD",
  "#AD6F3B", "#673770","#D14285", "#652926", "#C84248", 
  "#8569D5", "#5E738F","#D1A33D", "#8A7C64", "#599861"
)
```

## make the plot
```{R}
ggplot(taxa_plot_data)+
  geom_col(mapping = aes(x = swab_type, y = avg_abundance, fill = Phylum), position = "fill", show.legend = TRUE, color = "black")+
  ylab("Proportion of Community") +
  scale_fill_manual(values = phylum_colors) +
  theme_minimal()
```
