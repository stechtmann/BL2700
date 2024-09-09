# Introduction to R

### Basic Commands in R.
You can use R to do basic calculations just like you can in excel.

```R
sqrt(17-1)
pi*2
```

You can also save numbers as data objects.
```R
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

```R
genome_lengths <- c(4.6, 3300, 50000)
genes<- c(4401, 20000, 32000)
species <- c("ecoli", "human", "corn")  
```

The `scan()` command allows you add the values field by field.

```R

chromosomes<-scan()
1: 1
2: 23
3: 20
5: 
Read 4 items
[1] 1 23 20
```

The 'read.csv()` command allows you to read an entire comma separated file from your computer into the R environment.
```R
rooms<-read.csv("rooms.csv")
```
You will need to make sure the section in the quotes is a valid **filepath** to where the file is stored on your computer.

### Exploring data in R

#### Viewing your dataset
Once you have your data imported, you can take a look at it in a few different ways.
The simplest is to just run enter the name of the dataset (or any other variable):
```R
rooms
```

If you want to see the whole thing at once in a new tab (RStudio):
```R
view(rooms)
```
You can also click on a dataset in the environment tab of RStudio, which will automatically call view().

If you want to only look at a portion of a dataset, or want to look at a dataset that isn't compatible with view():
```R
head(rooms)
# or
tail(rooms)
```
head() gives the first few rows of the input, tail() gives the last few. The amount of rows/lines can be specified in the command.
```R
head(rooms, n=1)
# this will give only the first line
```
#### Removing NAs
Sometimes your dataset will have some missing datapoints for various reasons, which will get filled in with a special value of `NA`. There are a variety of ways to approach the problem of NAs, but if your dataset just so happens to have some blank rows, they can be removed with na.omit()
```R
na.omit(rooms)
# and if you want to actually save the removal of NAs...
rooms <- na.omit(rooms)
```
Other more complicated cases of NAs may require different solutions.

#### Calculating basic stats about data.

When running calculations in base R, the `$` specifies the column that you want to use in a dataset.

For example `rooms$Writing_Instruments` performs a calculation with the column `Writing_Instruments` in the `rooms` dataframe.

Mean - Average
```R
mean(rooms$Writing_Instruments)
```
Median - the central number of a data set. Arrange data points from smallest to largest and locate the central number. 
```R
median(rooms$Writing_Instruments)
```
Mode - The mode is the number in a data set that occurs most frequently.
```R
mode(rooms$Writing_Instruments)
# Wait that's not right
# In R, mode() gives the datatype, not the statistical mode
```
Standard Deviation - A measure of the amount of variation or dispersion of a set of values.
```R
sd(rooms$Writing_Instruments)
```
#### Subsetting datasets
some times you want to only use particular rows of a dataframe.  To do this you can subset a dataframe into other dataframes that only include the rows that you're interested in working with.

```R
classrooms<-subset(rooms, Room_Type=="classroom")
```
This command can be read as follows.
Create a new object `classrooms` by `subset`ing the dataframe `rooms` by only selecting rows where the value in the column `Room_Type` is an absolute match (`==`) to the word `classroom`.  

#### Functions
Sometimes you want to perform a calculation that is not present in R.  You can write these functions yourself.

Standard error is a common metric used in statistics, but is not present in base R.  Standard error is standard deviation divided the square root of the number of observations. (se = sd/sqrt(n))

```R
std.error<- function(x) {
	sd(x)/sqrt(length(x))
    # you can put multiple lines in a function
    # if you want
}
```
You can then recall the `std.error` function.

```R
std.error(rooms$Writing_Instruments)
```
Functions can also be very helpful in condensing large, frequently used blocks of code; making them much more manageable.

### Packages in R

Packages are sets of functions written by other people that can be recalled and used to analyze data.

#### Install Packages
```R
install.packages("tidyverse")
install.packages("vegan")
```

#### Load Packages
```R
library(tidyverse)
```

### Bonus Section: Loops
Sometimes running calculations and code line by line just isn't enough, and you just need more ~~power~~ automation. For this, loops can be a very useful tool to make R much more than a basic calculator.

#### If else
Technically not a loop, the If-else conditional provides the basic framework for other loops. It checks if a given statement is true or false, and performs a calculation based on the result:
```R
# saving the average cups and chairs as variables for later
avgcups <- mean(rooms$Cups)
avgchairs <- mean(rooms$Chairs)

if (avgcups > avgchairs) {
	print("There are more cups than chairs!")
} else {
	print("There are not more cups than chairs!") 
}
```
You can extend this with more layered ifs and elses to cover more conditions. If you're going crazy with If statements, keep in mind that they are evaluated sequentially. The example above might be a more useful comparison like so:
```R
if (avgcups > avgchairs) {
	print("There are more cups than chairs!")
} else if (avgcups < avgchairs){
	print("There are fewer cups than chairs!") 
} else if (avgcups == avgchairs){
	print("There are the same number of cups and chairs!
} else {
	print("Something went wrong, thanks defensive programming!")
}
```
#### While loops
While loops execute the code inside them *while* their initial condition remains true. The following example loop continues to repeat as long as `i` is less than 5. Since each loop also increases `i` by one, the loop will execute 5 times.
```R
i <- 0
while (i < 5){
	print(i)
    i <- i + 1
}
```
It is entirely (and easily) possible to create infinite loops. If this happens, you can escape it by clicking the stop sign button in the top right of the RStudio console.
#### For loops
For loops repeat once *for* each element in a given sequence. This can be useful for tasks like modifying each element of a list. 
```R
j <- c("help", "I", "live", "in", "a", "vector")

for (x in j) {
	print(x)
}
```
