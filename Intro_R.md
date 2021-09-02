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

### Data Types In R
Variables can be contained in a number of different data structures
1. Vectors
1. Factors
1. Matrix
1. Dataframe
1. Tibble
1. List

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

