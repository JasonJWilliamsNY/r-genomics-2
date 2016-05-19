---
layout: lesson
root: .
title: More on data frames
author: Data Carpentry contributors
minutes: 30
---

## Learning Objectives
- understand the concept of a `data.frame`
- using sequences to index data
- know how to access any element of a `data.frame`


# What are data frames?

`data.frame` is the _de facto_ data structure for most tabular data and what we use for statistics and plotting.

A `data.frame` is a collection of vectors of identical lengths. Each vector represents a column, and each vector can be of a different data type (e.g., characters, integers, factors). The `str()` function is useful to inspect the data types of the columns.

A `data.frame` can be created by the functions `read.csv()` or `read.table()`, in other words, when importing spreadsheets from your hard drive (or the web).

By default, `data.frame` converts (= coerces) columns that contain characters (i.e., text) into the `factor` data type. Depending on what you want to do with the data, you may want to keep these columns as `character`. To do so, `read.csv()` and `read.table()` have an argument called `stringsAsFactors` which can be set to `FALSE`:

```{r, eval=FALSE, purl=FALSE}
some_data <- read.csv("data/Ecoli_metadata.csv", stringsAsFactors=FALSE)
```


You can also create `data.frame` manually with the function `data.frame()`. This function can also take the argument `stringsAsFactors`. Compare the output of these examples, and compare the difference between when the data are being read as `character` and when they are being as `factor`.

```{r, results='show', purl=TRUE}
example_data <- data.frame(animal=c("dog", "cat", "sea cucumber", "sea urchin"),
                           feel=c("furry", "furry", "squishy", "spiny"),
                           weight=c(45, 8, 1.1, 0.8))
str(example_data)
example_data <- data.frame(animal=c("dog", "cat", "sea cucumber", "sea urchin"),
                           feel=c("furry", "furry", "squishy", "spiny"),
                           weight=c(45, 8, 1.1, 0.8), stringsAsFactors=FALSE)
str(example_data)
```



### Indexing and sequences (within a vector)


If we want to extract one or several values from a vector, we must provide one or several indices in square brackets, just as we do in math. 


R indexes start at 1. Programming languages like Fortran, MATLAB, and R start counting at 1, because that's what human beings typically do. Languages in the C family (including C++, Java, Perl, and Python) count from 0 because that's simpler for computers to do.


Our metadata data frame has rows and columns (it has 2 dimensions), if we want to extract some specific data from it, we need to specify the "coordinates" we want from it. Row numbers come first, followed by column numbers (i.e. [row, column]).

```{r, purl=FALSE, eval=FALSE}
metadata[1, 2]   # first element in the 2nd column of the data frame
metadata[1, 6]   # first element in the 6th column
metadata[1:3, 7] # first three elements in the 7th column
metadata[3, ]    # the 3rd element for all columns
metadata[, 7]    # the entire 7th column
head_meta <- metadata[1:6, ] # metadata[1:6, ] is equivalent to head(metadata)
```

### Challenge

1. The function `nrow()` on a `data.frame` returns the number of rows. Use it,
   in conjuction with `seq()` to create a new `data.frame` called
   `meta_by_2` that includes every other row of the survey data frame
   starting at row 2 (2, 4, 6, ...)


```{r, purl=FALSE}
meta_by_2 <- metadata[seq(2, nrow(metadata), by=2), ]
```


### Indexing and sequences (within a `data.frame`)

For larger datasets, it can be tricky to remember the column number that
corresponds to a particular variable. (Are species names in column 5 or 7? oh, right... they are in column 6). In some cases, in which column the variable will be can change if the script you are using adds or removes columns. It's therefore often better to use column names to refer to a particular variable, and it makes your code easier to read and your intentions clearer.

You can do operations on a particular column, by selecting it using the `$` sign. In this case, the entire column is a vector. You can use
`names(metadata)` or `colnames(metadata)` to remind yourself of the column names. For instance, to extract all the strain information from our datasets:

```{r, eval=FALSE}
metadata$strain
```

In some cases, you may way to select more than one column. You can do this using the square brackets. Suppose we wanted strain and clade information:

```{r, eval=FALSE}
metadata[, c("strain", "clade")]
```

You can even access columns by column name _and_ select specific rows of interest. For example, if we wanted the strain and clade of just rows 4 through 7, we could do:

```{r, eval=FALSE}
metadata[4:7, c("strain", "clade")]
```



