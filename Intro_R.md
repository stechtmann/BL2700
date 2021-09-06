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

### Packages in R

Packages are sets of programs written by other people that can be recalled and used to analyze data.

#### Install Packages
```{R}
install.packages("tidyverse")
install.packages("vegan")
```

#### Load Packages
```{R}
library(tidyverse)
```

