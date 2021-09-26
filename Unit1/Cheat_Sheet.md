# Examples from GitHubs for Unit 1
## Calculating Statistics related to data distribution in base R

### Mean

```{R}
mean(rooms$Writing_Instruments)
```
### Median
```{R}
mean(rooms$Writing_Instruments)
```
### Standard Deviation
```{R}
sd(rooms$Writing_Instruments)
```
### Subsetting Data
```{R}
classrooms<-subset(rooms, Room_Type=="classroom")
```
### Load Packages
```{R}
library(tidyverse)
```
### Importing data with `tidyverse`
```{R}
Buzzard<-read_csv("Desktop/Buzzard2015_data.csv")
```
### Select in `tidyverse`
```{R}
Buzzard_clean<-select(Buzzard, age, plot, genus, species, spcode, Abund.n, biomass, basal.area)
```
### Filter in `tidyverse`
```{R}
filter(Buzzard_clean, spcode=="Byrcra")
```
### Mutate in `tidyverse`
```{R}
Buzzard_clean2 <- mutate(Buzzard_clean, days = (age*365))
```
### Group_by and summarize in `tidyverse`
```{R}
Summary<-Buzzard_clean2 %>%
group_by(spcode)%>%
summarize(mean_abund=mean(Abund.n, na.rm = TRUE))
```
### plotting histograms with `ggplot`
```{R}
ggplot(data=diff, aes(Abund.n))+
  geom_histogram(bins=30)
```
### plotting boxplots with `ggplot`
```{R}
ggplot(data = diff, aes (x=spcode, y=Abund.n))+
  geom_boxplot()
```
### plotting point plots (scatter plots) in `ggplot`
```{R}
ggplot(data=diff_summary, aes (x= spcode, y = mean_Abund))+
  geom_point()
```
### plotting point plots (scatter plots) in `ggplot` with colors
```{R}
ggplot(data=diff_summary, aes (x= spcode, y = mean_Abund))+
  geom_point(aes(color=spcode)
```
### plotting point plots (scatter plots) in `ggplot` with colors and shapes
```{R}
ggplot(data = age_biomass, aes(x=age, y= total_biomass))+
  geom_point(aes(color=total_area, size=total_Abund))
```
