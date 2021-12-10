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

## Set your working directory to the directory where the data is found.
- Session
- Set working directory
- Choose Directory

## Read in data into your environment
```{R}
counts<-readRDS("seqtab.nochim.rds")
metadata<-read_csv("cb_metadata4.csv") #note: file changed 12/9/21
taxa<-read_csv("taxa.rds")
```

# Prepare data for phyloseq
This section outlines some pre-processing steps to prepare the data for use in the phyloseq package.

## Edit metadata
We will add rownames to the data to prepare it for use with the phyloseq package.

```{R}
head(metadata)
metadata2 <- metadata %>%
  mutate(SampleID = sampleid) %>%.        #make a copy of the sample ID column
  column_to_rownames(var = "sampleid")    #change one of the copies of the sample ID column to rownames
head(metadata2)
```

## Edit sequence table
We will remove the large sequence names (column names) since they will not be needed in phyloseq.

```{R}
counts[1:5,1:5]
colnames(counts) <- NULL                #remove column names
counts2 <- as.matrix(counts)            #convert into matrix format
counts2[1:5,1:5]
```

## Edit taxa table
We will also remove the sequence names from the taxa table. 

```{R}
taxa_table[1:5,1:5]
taxa_t<-t(taxa)                          #transpose taxa table
colnames(taxa_t) <- NULL                 #remove column names
taxa_table<-t(taxa_t)                    #un-transpose taxa table
taxa_table <- as.matrix(taxa_table)      #convert to matrix format
taxa_table[1:5,1:5]
```

## Make sure data dimensions match up
To build a phyloseq object the 

```{R}
dim(metadata2)
dim(counts2)
dim(taxa)
```

## Format data for phyloseq
```{R}
samdata = sample_data(metadata2)
seqtab = otu_table(counts2, taxa_are_rows = FALSE)
taxtab = tax_table(taxa_table)
```

# Building the phyloseq object

## Combine the three components into a single phyloseq object
```{R}
ps = phyloseq(otu_table(seqtab), tax_table(taxtab), sample_data(samdata))
ps
```

## Normalize the data in the phyloseq object
Sequencing data usually has some samples with very high numbers of sequences and some samples with very low numbers of sequences. This block of code will normalize the number of sequences across all of our samples to make them more easily comparable. 

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

# Explore the phyloseq object

## Look at how many samples of each type we have
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

# Alpha Diversity
Alpha diversity is a method of measuring how many different organisms we have in a sample. The more different types of organisms we have, the higher the alpha diversity. Consider an example where we think about students' names instead of organisms:

For example: A class has two groups of students working each working on a group project. Group 1 has five students and their names are: Bill, Ashley, Sophia, Mike, and Cody. Group 2 also have five students and their names are: Bob, Mary, Cody, Cody, and Cody. In this example, Group 1 has higher alpha diversity since we have 5 different names, compared to Group 2 where there are only 3 different names present.

There are many different metrics for measuring alpha diversity. In this example so far we have only considered "richness" (number of different species). Many alpha diversity metrics also consider "evenness" (the proportion of the total community made up by each species).

If we consider evenness in the above example, Group 1 has five students each with a unique name (the community is very even). Group 2 also has five students, but three of the students are named Cody (the community is uneven and dominated by one name). 

# Making an alpha diveristy plot

## Save a vector of colors
```{R}
sample_colors <- c("#CBD588", "#5F7FC7")
```

## Make a violin plot of alpha diversity
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
We can also do statistics to see if there is a statistically significant difference in diversity between the bilge biofilm and bilge water.

## Create data frame with the information we need to do some statistics
```{R}
richness <- sub_ps %>%
  estimate_richness(measures = c("Observed")) %>%           #specify which measures
  rownames_to_column(var = "SampleID") %>%                  #add column name to SampleID column
  left_join(metadata2, by = "SampleID")
head(richness)
```
## Perform Kruskal Wallis test
```{R}
kruskal.test(Observed ~ swab_type, data = richness) 
```

# Beta Diversity
Beta diversity is a metric for determining how similar communities are to one another. Instead of assessing the diversity in one sample at a time, we consider how similar the organisms in one community are to another community. Alpha diversity is often called "within sample diversity" and Beta diversity is called "between sample diversity". We do this by estimating the "distance" between communities (or samples). 

Consider the groups from the example above. How similar do you think these "communities" are?
Group 1: Bill, Ashley, Sophia, Mike, and Cody
Group 2: Bob, Mary, Cody, Cody, and Cody

Now consider a third group:
Group 3: Sophia, Sophia, Mike, Bill, and Mary
Do you think Group 1 or Group 2 is more similar to this third group?

# Make a PCoA Plot
We use PCoA plots to visualize this concept for microbial communities.

## Make ordination
This step mathematically calculates the distance between each sample
```{R}
ord <- ordinate(
  physeq = sub_ps, 
  method = "PCoA", 
  distance = "bray"
)
```
## Make the plot
```{R}
plot_ordination(
  physeq = sub_ps,                                                  #phyloseq object
  ordination = ord)+                                                #ordination
  geom_point(aes(fill = swab_type), size = 5, shape = 21) +         #sets fill color to sampletype
  scale_fill_manual(values = sample_colors) +                       #specify colors
  theme_classic()                                                   #changes theme, removes grey background
```

# Beta Diversity Statistics

## PERMANOVA
```{R}
bray <- phyloseq::distance(sub_ps, method = "bray")
sam <- data.frame(sample_data(sub_ps))
adonis2(bray ~ swab_type, data = sam)
```

# Make a taxa plot
This is a way of visualizing the individual organisms that make up our samples

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
