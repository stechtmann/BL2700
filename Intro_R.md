# Introduction to R

### Basic Commands in R.
You can use R to do basic calculations just like you can in excel.

```{R}
sqrt(17-1)
pi*2
```

You can also save numbers as data objects.
```{R}
answer1 = 23 + 21 + 17 + (19/2)  
answer2 = sqrt(23)
answer3 = answer1 + (answer1 * answer2)
```
### Data Types In R
Variables can be contained in a number of different data structures
1. Vectors
3. Factors
4. Matrix
5. Dataframe
6. List


### Entering data into R

The `c()` command stands for combine can combine lists of comma separated values.

```{R}
genome_lengths <- c(4.6, 3300, 50000)
genes<- c(4401, 20000, 32000)
species <- c("ecoli", "human", "corn")  
```

The `scan()` command allows you add the values field by field.

```{R}

chromosomes<-scan()
1: 1
2: 23
3: 20
5: 
Read 4 items
[1] 1 23 20
```

The 'read.csv()` command allows you to read an entire comma separated file from your computer into the R environment.
```{R}
rooms<-read.csv("rooms.csv")
```

### Exploring data in R

#### Calculating basic stats about data.

When running calculations in base R, the `$` specifies the column that you want to use in a dataset.

For example `rooms$Writing_Instruments` performs a calculation with the column `Writing_Instruments` in the `rooms` dataframe.

Mean - Average
```{R}
mean(rooms$Writing_Instruments)
```
Median - the central number of a data set. Arrange data points from smallest to largest and locate the central number. 
```{R}
mean(rooms$Writing_Instruments)
```
Mode - The mode is the number in a data set that occurs most frequently.
```{R}
mode(rooms$Writing_Instruments)
```
Standard Deviation - A measure of the amount of variation or dispersion of a set of values.
```{R}
sd(rooms$Writing_Instruments)
```
#### Subsetting datasets
some times you want to only use particular rows of a dataframe.  To do this you can subset a dataframe into other dataframes that only includ the rows that you're interested in working with.

```{R}
classrooms<-subset(rooms, Room_Type=="classroom")
```
This command can be read as follows.
Create a new object `classrooms` by `subset`ing the dataframe `rooms` by only selecting rows where the value in the column `Room_Type` is an absolute match (`==`) to the word `classroom`.  

#### Functions
Sometimes you want to perform a calculation that is not present in R.  You can write these functions yourself.

Standard error is a common metric used in statistics, but is not present in base R.  Standard error is standard deviation divided the square root of the number of observations. (se = sd/sqrt(n))

```{R}
std.error<- function(x) sd(x)/sqrt(length(x))
```
You can then recall the `std.error` function.

```{R}
stderror(rooms$Writing_Instruments)
```

### Packages in R

Packages are sets of functions written by other people that can be recalled and used to analyze data.

#### Install Packages
```{R}
install.packages("tidyverse")
install.packages("vegan")
```

#### Load Packages
```{R}
library(tidyverse)
```

