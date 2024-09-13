## Overview

### Generalized ggplot command

```R
ggplot(data = data, aes(x = x_value, y=y_value)+
geom()
```

### Data

We're going to be working with the same dataset from the Tidyverse tutorial.  The [Buzzard et al 2015](https://besjournals.onlinelibrary.wiley.com/doi/10.1111/1365-2435.12579) dataset.  

Make sure that you have the data imported.

### Preparing the data to plot.

We're going to start off with making some basic plots.  We're trying to test the hypothesis that *Acacia collinsii* is more abundant *Alibertia edulis*

Extract the data for those two species
```R
diff <- Buzzard_clean2 %>%
  filter(spcode=="acacol"|spcode=="aliedu")
```

### Exploring the data with a histogram

Histograms are a representation of the distribution of the data.

To make a histogram in ggplot
```R
ggplot(data=diff, aes(Abund.n))+
  geom_histogram(bins=30)
```
the `bins` is how many groups is the data groups into

### Summarizing the data

To see if there is a difference in the mean abundance we will summarize the data and then plot the mean abundance.

#### Summarize the data by calculating the mean abundance.
```R
diff_summary <- diff %>%
  group_by(spcode)%>%
  summarize(mean_Abund = mean(Abund.n, na.rm = TRUE),
            sd_Abund = sd(Abund.n, na.rm = TRUE))
```            
#### Plot the mean abundance as a point plot
```R
ggplot(data=diff_summary, aes (x= spcode, y = mean_Abund))+
  geom_point()
```
This is a very basic plot so we can do things like color the points if we want to.
```R
ggplot(data=diff_summary, aes (x= spcode, y = mean_Abund))+
  geom_point(aes(color=spcode)
```
### Plot the mean data as a bar plot

```R
ggplot(data=diff_summary, aes (x= spcode, y = mean_Abund))+
  geom_bar(stat = "identity", aes(color = spcode, fill = spcode))
```

## Explore the distribution in the full dataset

Let's explore the difference across the whole dataset by creating box and whisker plot

```R
ggplot(data = diff, aes (x=spcode, y=Abund.n))+
  geom_boxplot()
```
You can also layer on top of this the points
```R
ggplot(data = diff, aes (x=spcode, y=Abund.n))+
  geom_boxplot()+
  geom_point()
```
If you don't like the points overlaying you can plot this as a jitter
```R
ggplot(data = diff, aes (x=spcode, y=Abund.n))+
  geom_boxplot()+
  geom_jitter()
```
## Looking at correlation study.

In Buzzard et al they made a plot of total biomass versus stand age to examine the relationship between the two.  Let's recreate this plot in ggplot.

### Generate data
```R
age_biomass<-Buzzard_clean2%>%
  select(age, biomass, Abund.n,basal.area)%>%
  group_by(age)%>%
  summarize(total_biomass=sum(biomass,na.rm = TRUE),
            total_Abund=sum(Abund.n, na.rm = TRUE),
            total_area=sum(basal.area, na.rm = TRUE))
```

### Plot a point plot of age versus biomass

```R
ggplot(data = age_biomass, aes(x=age, y= total_biomass))+
  geom_point()
```

### Add some more detail by including area and abundance in the data.
```R
ggplot(data = age_biomass, aes(x=age, y= total_biomass))+
  geom_point(aes(color=total_area, size=total_Abund))
```
