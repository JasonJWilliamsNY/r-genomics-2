---
layout: lesson
root: .
title: Aggregating and analyzing data with dplyr
author: Data Carpentry contributors
---

## Learning Objectives

- understand what an R package is
- understand how to use `dplyr` to manipulate and clean data

## Lesson

### Packages
Packages in R are basically sets of additional functions that let you do more stuff in R. The functions we've been using, like `str()`, come built into R; packages give you access to more functions. You need to install a package and then load it to be able to use it. 

Installing packages: 

```{r, eval = FALSE, purl = FALSE}
install.packages("dplyr") ## install

library("dplyr")          ## load
```

You might get asked to choose a [CRAN](https://cran.r-project.org/) mirror -- this is basically asking you to choose a site to download the package from. The choice doesn't matter too much; we recommend choosing the RStudio mirror.

You only need to install a package once per computer, but you need to load it every time you open a new R session and want to use that package.

### A package for data manipulation

Bracket subsetting is handy, but it can be cumbersome and difficult to read, especially for complicated operations. Enter `dplyr`, a package for making data manipulation easier. 

**Note:** on the genomics workshop cloud machines we have already installed the package for you.

### What is dplyr?

The package `dplyr` is a fairly new (2014) package that tries to provide easy tools for the most common data manipulation tasks. It is built to work directly with data frames. It uses data frames for both input and output. The thinking behind it was largely inspired by the package `plyr` which has been in use for some time but suffered from being slow in some cases. `dplyr` addresses this by porting much of the computation to C++. An additional feature is the ability to work with data stored directly in an external database. The benefits of doing this are that the data can be managed
natively in a relational database, queries can be conducted on that database, and only the results of the query returned.

This addresses a common problem with R in that all operations are conducted in memory and thus the amount of data you can work with is limited by available memory. The database connections essentially remove that limitation in that you can have a database of many 100s GB, conduct queries on it directly and pull back just what you need for analysis in R.

### Using some of dplyr's functions

We're going to learn some of the most common `dplyr` functions: `select()`, `filter()`, `arrange`, `mutate()`, `group_by()`, and `summarize()`. 

To select columns of a data frame, use `select()`. The first argument to this function is the data frame (`metadata`), and the subsequent arguments are the columns to keep.

```{r, results = 'hide', purl = FALSE}
select(metadata, sample, clade, cit, genome_size)
```

To choose rows, use `filter()`:

```{r, purl = FALSE}
filter(metadata, cit == "plus")
```

You can create new variables with `mutate()`. These new variables can be computed from one or more of the other variables, for example to do unit conversions or find the ratio of values in two columns.

To create a new column of genome size in bp:

```{r, purl = FALSE}
mutate(metadata, genome_bp = genome_size*1e6)
```

What does `arrange()` do? What does `dplyr::select()` do?

### Some other functions that are useful accompanying the core dplyr functions

Sometimes it can be inconvenient for the results to be displayed to the console. The `head()` function helps with this. Or `glimpse()` from the dplyr function. Its nice to not always have to think about controlling our output in this way though. The `tbl_df()` function helps with this. 

By default, all **R functions operating on vectors that contains missing data will return NA**. It's a way to make sure that users know they have missing data, and make a conscious decision on how to deal with it. When dealing with simple statistics like the mean, the easiest way to ignore `NA` (the missing data) is to use `na.rm=TRUE` (`rm` stands for remove). Can we do this using the filter command though? `is.na()` is a function that determines whether something is an `NA` value. The `!` symbol negates it, so we're asking for everything that is not an `NA`. 


### Pipes: Chaining multiple steps

But what if you wanted to select and filter? There are three ways to do this: use intermediate steps, nested functions, or pipes. With the intermediate steps, you essentially create a temporary data frame and use that as input to the next function. This can clutter up your workspace with lots of objects. You can also nest functions (i.e. one function inside of another).  This is handy, but can be
difficult to read if too many functions are nested as the process from inside out. The last option, pipes, are a fairly recent addition to R. Pipes let you take the output of one function and send it directly to the next, which is useful when you need to many things to the same data set.  Pipes in R look like `%>%` and are made available via the `magrittr` package installed as part of `dplyr`.

```{r, purl = FALSE}
metadata %>%
  filter(cit == "plus") %>%
  select(sample, generation, clade)
```

In the above we use the pipe to send the `metadata` data set first through `filter`, to keep rows where `cit` was equal to 'plus', and then through `select` to keep the `sample` and `generation` and `clade` columns. When the data frame is being passed to the `filter()` and `select()` functions through a pipe, we don't need to include it as an argument to these functions anymore.

If we wanted to create a new object with this smaller version of the data we could do so by assigning it a new name:

```{r, purl = FALSE}
meta_citplus <- metadata %>%
  filter(cit == "plus") %>%
  select(sample, generation, clade)%>%
  print(n=100)
```

### Challenge 

1. Using pipes, subset the data to include rows where the clade is 'Cit+'. Retain columns  `sample`, `cit`, and `genome_size.`


### Split-apply-combine data analysis and the summarize() function

Many data analysis tasks can be approached using the "split-apply-combine" paradigm: split the data into groups, apply some analysis to each group, and then combine the results. `dplyr` makes this very easy through the use of the `group_by()` function. `group_by()` splits the data into groups upon which some operations can be run. For example, if we wanted to group by citrate-using mutant status and find the number of rows of data for each status, we would do:

```{r, purl = FALSE}
metadata %>%
  group_by(cit) %>%
  summarise(n())
```

`group_by()` is often used together with `summarize()` which collapses each group into a single-row summary of that group. `summarize()` can then be used to apply a function such as those that compute simple stats to give an overview for the group. 

In the above code `n()` is a summary function that provides a count for each group. There are several different summary statistics that can be generated from specific columns of our data. The R base package provides many built-in functions such as `mean`, `median`, `min`, `max`, and `range`.  So to view mean `genome_size` by mutant status:

```{r, purl = FALSE}
metadata %>%
  group_by(cit) %>%
  summarize(mean_size = mean(genome_size))
```

You can group by multiple columns too:

```{r, purl = FALSE}
metadata %>%
  group_by(cit, clade) %>%
  summarize(mean_size = mean(genome_size))

```

You can also summarize multiple variables at the same time:

```{r, purl = FALSE, eval=FALSE}
metadata %>%
  group_by(cit, clade) %>%
  summarize(mean_size = mean(genome_size),
            min_generation = min(generation))

```

### More Resources:

[Handy dplyr cheatsheet](http://www.rstudio.com/wp-content/uploads/2015/02/data-wrangling-cheatsheet.pdf)

*Much of this lesson was copied or adapted from Jeff Hollister's [materials](http://usepa.github.io/introR/2015/01/14/03-Clean/)*

