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
metadata<-read_csv("samples.transit.csv")
taxa<-read_csv("taxa.rds")
```

# Remove column names from the sequence table and convert to data frame
```{R}
colnames(counts) <- NULL
sequence_table <- as.data.frame(counts)
```

# Look at dimensions of metadata, sequence table, and taxa table
```{R}
dim(metadata)
dim(sequence_table)
dim(taxa)
```

# Make the phyloseq object
```{R}
samdata = sample_data(sdata2)
seqtab = otu_table(subset_all, taxa_are_rows = FALSE)
taxtab = tax_table(taxa)

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

# Make a vector of colors
```{R}
sample_colors <- c(
  "#CBD588", "#5F7FC7", "orange","#DA5724", "#508578", "#CD9BCD",
  "#AD6F3B", "yellow","#D14285", "#652926", "#C84248", "blue",
  "#8569D5", "#5E738F","#D1A33D", "#8A7C64", "#599861", "#CBD588", "#5F7FC7", "orange","#DA5724", "#508578", "#CD9BCD",
  "#AD6F3B", "#673770","#D14285", "#652926", "#C84248", 
  "#8569D5", "#5E738F","#D1A33D", "#8A7C64", "#599861"
)
```

# Make a violin plot of alpha diversity
```{R}
pps %>%                                                              #phyloseq object
  plot_richness(
    x = "Substrate",                                                #compare diversity of datatype
    measures = c("Observed", "Shannon")) +                           #choose diversity measures
  geom_violin(aes(fill = Substrate), show.legend = FALSE)+          #make violin plot, set fill aes to sampletype
  geom_boxplot(width=0.1) +                                          #add boxplot, set width
  theme_classic()+                                                   #change theme to classic
  xlab(NULL)+                                                        #no label on x-axis
  theme(axis.text.y.left = element_text(size = 20),                  #adjust y-axis text
        axis.text.x = element_text(size = 20, hjust = 1, vjust = 0.5, angle = 90),           #adjust x-axis label position
        axis.title.y = element_text(size = 20))+                     #adjust y-axis title
  theme(strip.text = element_text(face = "bold", size = 25))+        #adjust headings
  scale_fill_manual(values = sample_colors)+   #set fill colors
  # scale_x_discrete(                                                  #change x-axis labels
  # breaks = sample_types)+                   
  ggtitle("Alpha Diversity") +                                       #add title
  theme(plot.title=element_text(size = 25, face = "bold", hjust = 0.5)) #change title size, face and position

```

# Make a PCoA Plot
```{R}
#ordination
all_pcoa <- ordinate(
  physeq = pps, 
  method = "PCoA", 
  distance = "bray"
)

head(sdata)

sample_colors <- c(
  "black",   "darkcyan",     "orchid1",   "green",       "coral1",            "blue",
  "grey47",  "cyan",         "yellow",    "darkgreen",   "palegoldenrod",     "tan4",
  "grey77",  "darkblue",     "red",         "mediumpurple1",     "purple4",
  "white",   "dodgerblue",   "firebrick", "yellowgreen", "magenta", "#5F7FC7", "orange","#DA5724", "#508578", "#CD9BCD",
  "#D14285", "#652926", "#C84248", "blue",
  "#8569D5", "#5E738F","#D1A33D", "#8A7C64", "#599861", "#CBD588", "#5F7FC7","#DA5724", "#508578", "#CD9BCD",
  "#AD6F3B", "#673770","#D14285", "#652926", "#C84248", 
  "#8569D5", "#5E738F","#D1A33D", "#8A7C64", "#599861"
) 

#plot
plot_ordination(
  physeq = pps,                                                         #phyloseq object
  ordination = all_pcoa)+                                                #ordination
  geom_point(aes(fill = Substrate), size = 5, shape = 21) +     #sets fill color to sampletype
  #scale_shape_manual(values = c(21, 22, 23, 24, 25))+
  scale_fill_manual(values = sample_colors) +
  theme_classic() +                                                      #changes theme, removes grey background
  theme(                             
    legend.text = element_text(size = 20),                               #changes legend size
    legend.title = element_blank(),                                      #removes legend title
    legend.background = element_rect(fill = "white", color = "black"))+  #adds black boarder around legend
  theme(axis.text.y.left = element_text(size = 20),
        axis.text.x = element_text(size = 20),
        axis.title.x = element_text(size = 20),
        axis.title.y = element_text(size = 20))+
  guides(fill = guide_legend(override.aes = list(shape = 21)))           #fills legend points based on the fill command

```

# Make a taxa plot

## Remove mitochondria and chloroplasts from the data set
```{R}
justbacteria <- pps %>%
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
all <- phylumabundance %>%
  select(Phylum, Abundance, Stream, EcoRegion, Substrate) %>%
  group_by(Phylum, Substrate) %>%
  summarize(
    avg_abundance = mean(Abundance)
  ) %>%
  mutate(
    Phylum = as.character(Phylum),
    Phylum = ifelse(avg_abundance < 0.02, "< 2 %", Phylum)
  ) %>%
  group_by(Phylum, Substrate) %>%
  summarize(
    avg_abundance = sum(avg_abundance)
  )
head(all)
```

## Make the plot
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

#5) Create taxa plot!
ggplot(all)+
  geom_col(mapping = aes(x = Substrate, y = avg_abundance, fill = Phylum), position = "fill", show.legend = TRUE, color = "black")+
  #facet_grid(rows = vars(Environment), cols = vars(Transfer))+
  ylab("Proportion of Community") +
  scale_fill_manual(values = phylum_colors) +
  xlab(NULL)+
  theme_minimal()+
  theme(axis.text.y.left = element_text(size = 12),
        axis.text.x = element_text(size = 12, angle = 90, vjust = 0.5, hjust = 1),
        axis.title.y = element_text(size = 12),
        title = element_text(size = 25))

```
