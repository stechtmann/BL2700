# Introduction to R

### Basic Commands in R.
You can use R to do basic calculations just like you can in excel.

```{R}
23 + 7  
14 + 11 + (23*2)
sqrt(17-1)
pi*2
```

You can also save numbers as data objects.
```{R}
> answer1 = 23 + 21 + 17 + (19/2)  
> answer2 = sqrt(23)
> answer3 = answer1 + (answer1 * answer2)
```

### Entering data into R

The `c()` command stands for combine can combine lists of comma separated values.

```{R}
data1 <- c(1, 56, 72, 33)
glengths <- c(4.6, 3000, 50000)
species <- c("ecoli", "human", "corn") 
```

The `scan()` command allows you add the values field by field.

```{R}
> scan()
1: 23
2: 34
3: 56
4: 22
5: 
Read 4 items
[1] 23 34 56 22
```

The 'read.csv()` command allows you to read an entire comma separated file from your computer into the R environment.
```{R}
rooms<-read.csv("rooms.csv")
```

### Data Types In R
Variables can be contained in a number of different data structures
1. Vectors
3. Factors
4. Matrix
5. Dataframe
6. Tibble
7. List

### Packages in R

Packages are sets of programs written by other people that can be recalled and used to analyze data.

#### Install Packages
```{R}
install.packages(tidyverse)
```

#### Load Packages
```{R}
library(tidyverse)
```

