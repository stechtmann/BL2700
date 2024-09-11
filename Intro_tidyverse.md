# Install tidyverse
```R
install.packages("tidyverse")
```

## Start tidyverse
```R
library(tidyverse)
```

## Get your data

Download the [Buzzard et al 2015 dataset](https://raw.githubusercontent.com/stechtmann/BL2700/master/data/Buzzard2015_data.csv) form [Buzzard et al 2015](https://besjournals.onlinelibrary.wiley.com/doi/10.1111/1365-2435.12579) Re-growing a tropical dry forest: functional plant trait composition and community assembly during succession

To download the file click `ctrl-s` on windows or `command-s` on a mac and select a place to save.  Make sure that you have the .csv extension

## Open up RStudio

### Import the data into R
We could use the `read.csv` function that is base R that we discussed on Wednesday in class.

We're going to import our data in a tidy manner using the `read_csv` function in tidyverse

```R
Buzzard<-read_csv("Desktop/Buzzard2015_data.csv")
```

### Clean up the data.

#### Select the data to use

The data has many more columns than we need to use.  We are only interested in the following information:
1. age
1. plot
1. genus
1. species
1. spcode
1. Abund.n
1. biomass
1. basal.area

All of the other columns we don't need.

We are going to use the `select` funtion to only select the columns that we want.

```R
Buzzard_clean<-select(Buzzard, age, plot, genus, species, spcode, Abund.n, biomass, basal.area)
```

### `filter`

The `filter` function in `dplyr` as part of `tidyverse` is similar to subset command. This will extract the rows that you want to keep.  It does this by keeping the rows that have a specific value in a specified column.

Let's keep all of the rows that match the species code - Byrcra

```R
filter(Buzzard_clean, spcode=="Byrcra")
```
We can also use logical statements to keep values that match one thing or another.  The "or" symbol is `|`  The "and" symbol is `&`.

Let's keep all of the rows from the following three species codes: Byrcra, roumon, and agomac

```R
filter(Buzzard_clean, spcode=="Byrcra"|spcode=="roumon"|spcode=="agomac")
```
### `mutate`

The mutate function will create a new column and populate it with a particular output.

Let's create a column called `days` that converts the `age` column which is in years by multiplying the age column by 365

```R
Buzzard_clean2 <- mutate(Buzzard_clean, days = (age*365))
```

### `group_by` and `summarize`

The `group_by` and `summarize` commands allow for summarizing statistics about the different groups in the dataset.

These two commands are often run in series and piped together to create a pipeline.  The pipe symbol (`%>%`) connects two tidy verse functions. It does this by sending the results from the left function (or object) into the first argument of the function on the right of the pipe.


Let's calculate the mean abundance of all of the species in the data set by grouping the entries by the `spcode` and summarize by calculating the `mean` of the `Abund.n` column


```R
Summary<-Buzzard_clean2 %>%
group_by(spcode)%>%
summarize(mean_abund=mean(Abund.n, na.rm = TRUE))
```

You can also summarize multiple columns as the same time

```R
Summary<-Buzzard_clean2 %>%
group_by(spcode)%>%
summarize(mean_abund=mean(Abund.n, na.rm = TRUE),
sd_abund=sd(Abund.n, na.rm = TRUE),
mean_biomass=mean(biomass, na.rm = TRUE),
sd_biomass=sd(biomass, na.rm = TRUE),
mean_area=mean(basal.area, na.rm = TRUE),
sd_area=sd(basal.area, na.rm = TRUE))
```


### `arrange`
To make displaying your data simpler, the `arrange` command can be used to order your data.

```R
Buzzard_clean2 %>%
group_by(spcode)%>%
summarize(mean_abund=mean(Abund.n, na.rm = TRUE))%>
arrange(mean_abund)
```
This orders the data by lowest to highest (ascending).  To order your data in a descending manner you can use `desc()`
```R
Buzzard_clean2 %>%
group_by(spcode)%>%
summarize(mean_abund=mean(Abund.n, na.rm = TRUE))%>
arrange(desc(mean_abund))
```
