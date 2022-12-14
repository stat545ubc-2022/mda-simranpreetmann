Mini Data Analysis Milestone 2
================

*To complete this milestone, you can edit [this `.rmd`
file](https://raw.githubusercontent.com/UBC-STAT/stat545.stat.ubc.ca/master/content/mini-project/mini-project-2.Rmd)
directly. Fill in the sections that are commented out with
`<!--- start your work here--->`. When you are done, make sure to knit
to an `.md` file by changing the output in the YAML header to
`github_document`, before submitting a tagged release on canvas.*

# Welcome to your second (and last) milestone in your mini data analysis project!

In Milestone 1, you explored your data, came up with research questions,
and obtained some results by making summary tables and graphs. This
time, we will first explore more in depth the concept of *tidy data.*
Then, you’ll be sharpening some of the results you obtained from your
previous milestone by:

- Manipulating special data types in R: factors and/or dates and times.
- Fitting a model object to your data, and extract a result.
- Reading and writing data as separate files.

**NOTE**: The main purpose of the mini data analysis is to integrate
what you learn in class in an analysis. Although each milestone provides
a framework for you to conduct your analysis, it’s possible that you
might find the instructions too rigid for your data set. If this is the
case, you may deviate from the instructions – just make sure you’re
demonstrating a wide range of tools and techniques taught in this class.

# Instructions

**To complete this milestone**, edit [this very `.Rmd`
file](https://raw.githubusercontent.com/UBC-STAT/stat545.stat.ubc.ca/master/content/mini-project/mini-project-2.Rmd)
directly. Fill in the sections that are tagged with
`<!--- start your work here--->`.

**To submit this milestone**, make sure to knit this `.Rmd` file to an
`.md` file by changing the YAML output settings from
`output: html_document` to `output: github_document`. Commit and push
all of your work to your mini-analysis GitHub repository, and tag a
release on GitHub. Then, submit a link to your tagged release on canvas.

**Points**: This milestone is worth 55 points (compared to the 45 points
of the Milestone 1): 45 for your analysis, and 10 for your entire
mini-analysis GitHub repository. Details follow.

**Research Questions**: In Milestone 1, you chose two research questions
to focus on. Wherever realistic, your work in this milestone should
relate to these research questions whenever we ask for justification
behind your work. In the case that some tasks in this milestone don’t
align well with one of your research questions, feel free to discuss
your results in the context of a different research question.

# Learning Objectives

By the end of this milestone, you should:

- Understand what *tidy* data is, and how to create it using `tidyr`.
- Generate a reproducible and clear report using R Markdown.
- Manipulating special data types in R: factors and/or dates and times.
- Fitting a model object to your data, and extract a result.
- Reading and writing data as separate files.

# Setup

Begin by loading your data and the tidyverse package below:

``` r
library(datateachr) # <- might contain the data you picked!
library(tidyverse)
```

# Task 1: Tidy your data (15 points)

In this task, we will do several exercises to reshape our data. The goal
here is to understand how to do this reshaping with the `tidyr` package.

A reminder of the definition of *tidy* data:

- Each row is an **observation**
- Each column is a **variable**
- Each cell is a **value**

*Tidy’ing* data is sometimes necessary because it can simplify
computation. Other times it can be nice to organize data so that it can
be easier to understand when read manually.

### 1.1 (2.5 points)

Based on the definition above, can you identify if your data is tidy or
untidy? Go through all your columns, or if you have \>8 variables, just
pick 8, and explain whether the data is untidy or tidy.

<!--------------------------- Start your work below --------------------------->

The data I am using, and have used to complete Milestone 1 of this
project, is vancouver_trees. This data has 20 columns, thus I will focus
on the following 8 columns to answer this question: tree_id, genus_name,
species_name, root_barrier, neighbourhood_name, height_range_id,
diameter, date_planted. I have chosen to include the tree_id column
because it has a unique value for every row and can, thus, act as an
identifier variable if I need to join tibbles in future tasks. I have
chosen the other 7 variables because I have either already used them in
my analyses for Milestone 1 or I had thought of questions that could be
answered using those variables. Although I didn’t necessarily include
these questions in Milestone 1, I am keeping these variables,
particularly *root_barrier* and *genus_name*, in case I explore these
questions further in Milestone 2 of this project.

Based on the 8 variables listed previously, this data set is tidy due to
the fact that each row corresponds to an observation, each column
corresponds to a variable and each cell represents a value.

To further elaborate, untidy data sets may have values as column
headers. If, for example, height_range_id, which ranges from 0-10, was
spread across 11 columns, with each column being called
height_range_id=0, height_range_id=1, and so on, this would be
considered an untidy data set. Given that this isn’t the case with any
column, this data set is considered tidy.

Untidy data sets may also have multiple variables stored in one column.
For example, if the species_name and genus_name were combined into a
single column called scientific_name, this would be considered an untidy
data set. This is because the column scientific_name would contain two
pieces of information regarding the genus and species of the tree. It
would be much more helpful, in terms of data analysis, to have these
pieces of information be represented by two separate columns. However,
given that this isn’t the case with the *vancouver_trees* data set, it
can be considered tidy.

Another reason why a data set may be considered untidy is because
variables are stored in both rows and columns. With this data set, if
there was a column called name which contained the genus or species name
of a given tree, and another column called name_type which indicated
whether the name given in that row was the genus or species name, then
this data set would be considered untidy. Since none of the variables
are stored in rows, this data set is considered tidy.
<!----------------------------------------------------------------------------->

### 1.2 (5 points)

Now, if your data is tidy, untidy it! Then, tidy it back to it’s
original state.

If your data is untidy, then tidy it! Then, untidy it back to it’s
original state.

Be sure to explain your reasoning for this task. Show us the “before”
and “after”.

<!--------------------------- Start your work below --------------------------->

As described previously, my data is tidy. The first 5 rows of my data
are shown below.

``` r
vancouver_trees
```

    ## # A tibble: 146,611 × 20
    ##    tree_id civic_number std_st…¹ genus…² speci…³ culti…⁴ commo…⁵ assig…⁶ root_…⁷
    ##      <dbl>        <dbl> <chr>    <chr>   <chr>   <chr>   <chr>   <chr>   <chr>  
    ##  1  149556          494 W 58TH … ULMUS   AMERIC… BRANDON BRANDO… N       N      
    ##  2  149563          450 W 58TH … ZELKOVA SERRATA <NA>    JAPANE… N       N      
    ##  3  149579         4994 WINDSOR… STYRAX  JAPONI… <NA>    JAPANE… N       N      
    ##  4  149590          858 E 39TH … FRAXIN… AMERIC… AUTUMN… AUTUMN… Y       N      
    ##  5  149604         5032 WINDSOR… ACER    CAMPES… <NA>    HEDGE … N       N      
    ##  6  149616          585 W 61ST … PYRUS   CALLER… CHANTI… CHANTI… N       N      
    ##  7  149617         4909 SHERBRO… ACER    PLATAN… COLUMN… COLUMN… N       N      
    ##  8  149618         4925 SHERBRO… ACER    PLATAN… COLUMN… COLUMN… N       N      
    ##  9  149619         4969 SHERBRO… ACER    PLATAN… COLUMN… COLUMN… N       N      
    ## 10  149625          720 E 39TH … FRAXIN… AMERIC… AUTUMN… AUTUMN… N       N      
    ## # … with 146,601 more rows, 11 more variables: plant_area <chr>,
    ## #   on_street_block <dbl>, on_street <chr>, neighbourhood_name <chr>,
    ## #   street_side_name <chr>, height_range_id <dbl>, diameter <dbl>, curb <chr>,
    ## #   date_planted <date>, longitude <dbl>, latitude <dbl>, and abbreviated
    ## #   variable names ¹​std_street, ²​genus_name, ³​species_name, ⁴​cultivar_name,
    ## #   ⁵​common_name, ⁶​assigned, ⁷​root_barrier

In order to untidy my data, I have done the following:

1.  **column headers are values, not only names of variables**: I have
    taken the variable *height_range_id* and used pivot_wider() to
    spread it out over multiple columns, with the values being the
    diameter of the tree at breast height. Given that my third research
    question in Milestone 1 was exploring the relationship between
    height_range_id and diameter, it may, at first glance, seem like it
    would be helpful to have the data organized in this manner since
    we’re associating height_range_id with diameter. However, this is
    not the case. Since it is more natural to think about functional
    relationships between variables rather than observations, this data
    would be better off having two separate columns for height_range_id
    and diameter in order to answer research question \# 3 in the most
    efficient manner possible.

2.  **variables are stored in both rows and columns**: I have created a
    new column called *name_type* which indicates whether the name
    listed in the column called *name* is the genus or species of the
    tree. Previously, genus_name and species_name were in two separate
    columns. I have now combined this information into the column called
    *name*.

The untidy data can be seen below.

``` r
vancouver_trees <- vancouver_trees %>%
  pivot_wider(names_from = "height_range_id",
              values_from = "diameter") %>%
   pivot_longer(cols=c("genus_name", "species_name"),
                names_to = "name_type",
                values_to = "name")

vancouver_trees
```

    ## # A tibble: 293,222 × 29
    ##    tree_id civic_number std_st…¹ culti…² commo…³ assig…⁴ root_…⁵ plant…⁶ on_st…⁷
    ##      <dbl>        <dbl> <chr>    <chr>   <chr>   <chr>   <chr>   <chr>     <dbl>
    ##  1  149556          494 W 58TH … BRANDON BRANDO… N       N       N           400
    ##  2  149556          494 W 58TH … BRANDON BRANDO… N       N       N           400
    ##  3  149563          450 W 58TH … <NA>    JAPANE… N       N       N           400
    ##  4  149563          450 W 58TH … <NA>    JAPANE… N       N       N           400
    ##  5  149579         4994 WINDSOR… <NA>    JAPANE… N       N       4          4900
    ##  6  149579         4994 WINDSOR… <NA>    JAPANE… N       N       4          4900
    ##  7  149590          858 E 39TH … AUTUMN… AUTUMN… Y       N       4           800
    ##  8  149590          858 E 39TH … AUTUMN… AUTUMN… Y       N       4           800
    ##  9  149604         5032 WINDSOR… <NA>    HEDGE … N       N       4          5000
    ## 10  149604         5032 WINDSOR… <NA>    HEDGE … N       N       4          5000
    ## # … with 293,212 more rows, 20 more variables: on_street <chr>,
    ## #   neighbourhood_name <chr>, street_side_name <chr>, curb <chr>,
    ## #   date_planted <date>, longitude <dbl>, latitude <dbl>, `2` <dbl>, `4` <dbl>,
    ## #   `3` <dbl>, `5` <dbl>, `1` <dbl>, `6` <dbl>, `0` <dbl>, `7` <dbl>,
    ## #   `9` <dbl>, `8` <dbl>, `10` <dbl>, name_type <chr>, name <chr>, and
    ## #   abbreviated variable names ¹​std_street, ²​cultivar_name, ³​common_name,
    ## #   ⁴​assigned, ⁵​root_barrier, ⁶​plant_area, ⁷​on_street_block

Next, I will once again tidy my untidy data. The tidy data can be seen
below. Notice that it is identical to the initial version of the data I
started with.

``` r
vancouver_trees <- vancouver_trees %>%
  pivot_wider(names_from = "name_type",
              values_from = "name") %>%
  pivot_longer(cols = c("0","1","2","3","4","5","6","7","8","9","10"),
               names_to = "height_range_id",
               values_to = "diameter",
               names_transform = list(height_range_id = as.double),
               values_drop_na = TRUE)

vancouver_trees
```

    ## # A tibble: 146,611 × 20
    ##    tree_id civic_number std_st…¹ culti…² commo…³ assig…⁴ root_…⁵ plant…⁶ on_st…⁷
    ##      <dbl>        <dbl> <chr>    <chr>   <chr>   <chr>   <chr>   <chr>     <dbl>
    ##  1  149556          494 W 58TH … BRANDON BRANDO… N       N       N           400
    ##  2  149563          450 W 58TH … <NA>    JAPANE… N       N       N           400
    ##  3  149579         4994 WINDSOR… <NA>    JAPANE… N       N       4          4900
    ##  4  149590          858 E 39TH … AUTUMN… AUTUMN… Y       N       4           800
    ##  5  149604         5032 WINDSOR… <NA>    HEDGE … N       N       4          5000
    ##  6  149616          585 W 61ST … CHANTI… CHANTI… N       N       B           500
    ##  7  149617         4909 SHERBRO… COLUMN… COLUMN… N       N       6          4900
    ##  8  149618         4925 SHERBRO… COLUMN… COLUMN… N       N       6          4900
    ##  9  149619         4969 SHERBRO… COLUMN… COLUMN… N       N       3          4900
    ## 10  149625          720 E 39TH … AUTUMN… AUTUMN… N       N       3           700
    ## # … with 146,601 more rows, 11 more variables: on_street <chr>,
    ## #   neighbourhood_name <chr>, street_side_name <chr>, curb <chr>,
    ## #   date_planted <date>, longitude <dbl>, latitude <dbl>, genus_name <chr>,
    ## #   species_name <chr>, height_range_id <dbl>, diameter <dbl>, and abbreviated
    ## #   variable names ¹​std_street, ²​cultivar_name, ³​common_name, ⁴​assigned,
    ## #   ⁵​root_barrier, ⁶​plant_area, ⁷​on_street_block

<!----------------------------------------------------------------------------->

### 1.3 (7.5 points)

Now, you should be more familiar with your data, and also have made
progress in answering your research questions. Based on your interest,
and your analyses, pick 2 of the 4 research questions to continue your
analysis in the next four tasks:

<!-------------------------- Start your work below ---------------------------->

1.  **Which genus of trees, among the top 5 genera in Vancouver, has the
    greatest diameter on average?**

    *<span style="color: red;">Please Note: Research question \# 1 is
    equivalent to Research question \# 2 from milestone 1 of this
    project; however, some details have been added to the question to
    make it more specific</span>*

2.  **Which neighborhood in Vancouver has the greatest biodiversity?**

<!----------------------------------------------------------------------------->

Explain your decision for choosing the above two research questions.

<!--------------------------- Start your work below --------------------------->

1.  **Which genus of trees, among the top 5 genera in Vancouver, has the
    greatest diameter on average?**

I chose this research question because I want to explore whether there
is a relationship between the genera of a tree and its diameter. I
hypothesize that certain genera will have significantly greater
diameters (in inches at breast height) compared to their counterparts. I
am interested in seeing what characteristics of these trees allow them
to have a greater diameter. The first step to exploring this question,
though, is determining the genera with the greatest diameter on average.

2.  **Which neighborhood in Vancouver has the greatest biodiversity?**

As most other people, I absolutely love the outdoors and nature, and, as
such, would ideally like to reside in an area surrounded by it. However,
given the increasing trend towards gentrification in Vancouver, I would
like to explore whether more affluent neighbourhoods (i.e., Kerrisdale,
Point Grey), which typically cost much more to live in, are associated
with greater, lesser, or equal biodiversity to their counterparts, with
biodiversity being a combination of the total number of trees in a given
neighbourhood as well as the total number of distinct tree species in
that area.
<!----------------------------------------------------------------------------->

Now, try to choose a version of your data that you think will be
appropriate to answer these 2 questions. Use between 4 and 8 functions
that we’ve covered so far (i.e. by filtering, cleaning, tidy’ing,
dropping irrelevant columns, etc.).

<!--------------------------- Start your work below --------------------------->

**Function \# 1: Drop Irrelevant Columns**

For this function, I will drop all columns that are not important in my
data analysis moving forward.

``` r
vancouver_trees <- vancouver_trees %>%
  select(tree_id, genus_name, species_name, neighbourhood_name, height_range_id, diameter, date_planted)

print(vancouver_trees)
```

    ## # A tibble: 146,611 × 7
    ##    tree_id genus_name species_name neighbourhood_name heigh…¹ diame…² date_pla…³
    ##      <dbl> <chr>      <chr>        <chr>                <dbl>   <dbl> <date>    
    ##  1  149556 ULMUS      AMERICANA    MARPOLE                  2    10   1999-01-13
    ##  2  149563 ZELKOVA    SERRATA      MARPOLE                  4    10   1996-05-31
    ##  3  149579 STYRAX     JAPONICA     KENSINGTON-CEDAR …       3     4   1993-11-22
    ##  4  149590 FRAXINUS   AMERICANA    KENSINGTON-CEDAR …       4    18   1996-04-29
    ##  5  149604 ACER       CAMPESTRE    KENSINGTON-CEDAR …       2     9   1993-12-17
    ##  6  149616 PYRUS      CALLERYANA   MARPOLE                  2     5   NA        
    ##  7  149617 ACER       PLATANOIDES  KENSINGTON-CEDAR …       3    15   1993-12-16
    ##  8  149618 ACER       PLATANOIDES  KENSINGTON-CEDAR …       3    14   1993-12-16
    ##  9  149619 ACER       PLATANOIDES  KENSINGTON-CEDAR …       2    16   1993-12-16
    ## 10  149625 FRAXINUS   AMERICANA    KENSINGTON-CEDAR …       2     7.5 1993-12-03
    ## # … with 146,601 more rows, and abbreviated variable names ¹​height_range_id,
    ## #   ²​diameter, ³​date_planted

**Function \# 2: Make Height Range ID Column Into A Factor**

``` r
vancouver_trees <- vancouver_trees %>%
  mutate(height_range_id = factor(case_when(height_range_id == 0 ~ "0-10",
                                            height_range_id == 1 ~ "10-20",
                                            height_range_id == 2 ~ "20-30",
                                            height_range_id == 3 ~ "30-40",
                                            height_range_id == 4 ~ "40-50",
                                            height_range_id == 5 ~ "50-60",
                                            height_range_id == 6 ~ "60-70",
                                            height_range_id == 7 ~ "70-80",
                                            height_range_id == 8 ~ "80-90",
                                            height_range_id == 9 ~ "90-100",
                                            height_range_id == 10 ~ "100+")))

print(vancouver_trees)
```

    ## # A tibble: 146,611 × 7
    ##    tree_id genus_name species_name neighbourhood_name heigh…¹ diame…² date_pla…³
    ##      <dbl> <chr>      <chr>        <chr>              <fct>     <dbl> <date>    
    ##  1  149556 ULMUS      AMERICANA    MARPOLE            20-30      10   1999-01-13
    ##  2  149563 ZELKOVA    SERRATA      MARPOLE            40-50      10   1996-05-31
    ##  3  149579 STYRAX     JAPONICA     KENSINGTON-CEDAR … 30-40       4   1993-11-22
    ##  4  149590 FRAXINUS   AMERICANA    KENSINGTON-CEDAR … 40-50      18   1996-04-29
    ##  5  149604 ACER       CAMPESTRE    KENSINGTON-CEDAR … 20-30       9   1993-12-17
    ##  6  149616 PYRUS      CALLERYANA   MARPOLE            20-30       5   NA        
    ##  7  149617 ACER       PLATANOIDES  KENSINGTON-CEDAR … 30-40      15   1993-12-16
    ##  8  149618 ACER       PLATANOIDES  KENSINGTON-CEDAR … 30-40      14   1993-12-16
    ##  9  149619 ACER       PLATANOIDES  KENSINGTON-CEDAR … 20-30      16   1993-12-16
    ## 10  149625 FRAXINUS   AMERICANA    KENSINGTON-CEDAR … 20-30       7.5 1993-12-03
    ## # … with 146,601 more rows, and abbreviated variable names ¹​height_range_id,
    ## #   ²​diameter, ³​date_planted

**Function \# 3: Delete all rows without a Genus Name AND Diameter, if
there are any**

``` r
vancouver_trees <- vancouver_trees %>%
  drop_na(c(genus_name, diameter))

print(vancouver_trees)
```

    ## # A tibble: 146,611 × 7
    ##    tree_id genus_name species_name neighbourhood_name heigh…¹ diame…² date_pla…³
    ##      <dbl> <chr>      <chr>        <chr>              <fct>     <dbl> <date>    
    ##  1  149556 ULMUS      AMERICANA    MARPOLE            20-30      10   1999-01-13
    ##  2  149563 ZELKOVA    SERRATA      MARPOLE            40-50      10   1996-05-31
    ##  3  149579 STYRAX     JAPONICA     KENSINGTON-CEDAR … 30-40       4   1993-11-22
    ##  4  149590 FRAXINUS   AMERICANA    KENSINGTON-CEDAR … 40-50      18   1996-04-29
    ##  5  149604 ACER       CAMPESTRE    KENSINGTON-CEDAR … 20-30       9   1993-12-17
    ##  6  149616 PYRUS      CALLERYANA   MARPOLE            20-30       5   NA        
    ##  7  149617 ACER       PLATANOIDES  KENSINGTON-CEDAR … 30-40      15   1993-12-16
    ##  8  149618 ACER       PLATANOIDES  KENSINGTON-CEDAR … 30-40      14   1993-12-16
    ##  9  149619 ACER       PLATANOIDES  KENSINGTON-CEDAR … 20-30      16   1993-12-16
    ## 10  149625 FRAXINUS   AMERICANA    KENSINGTON-CEDAR … 20-30       7.5 1993-12-03
    ## # … with 146,601 more rows, and abbreviated variable names ¹​height_range_id,
    ## #   ²​diameter, ³​date_planted

**Function \# 4: Change Genus Name and Neighbourhood Name to a Factor**

``` r
vancouver_trees <- vancouver_trees %>%
  mutate(genus_name = factor(genus_name)) %>%
  mutate(neighbourhood_name = factor(neighbourhood_name))

print(vancouver_trees)
```

    ## # A tibble: 146,611 × 7
    ##    tree_id genus_name species_name neighbourhood_name heigh…¹ diame…² date_pla…³
    ##      <dbl> <fct>      <chr>        <fct>              <fct>     <dbl> <date>    
    ##  1  149556 ULMUS      AMERICANA    MARPOLE            20-30      10   1999-01-13
    ##  2  149563 ZELKOVA    SERRATA      MARPOLE            40-50      10   1996-05-31
    ##  3  149579 STYRAX     JAPONICA     KENSINGTON-CEDAR … 30-40       4   1993-11-22
    ##  4  149590 FRAXINUS   AMERICANA    KENSINGTON-CEDAR … 40-50      18   1996-04-29
    ##  5  149604 ACER       CAMPESTRE    KENSINGTON-CEDAR … 20-30       9   1993-12-17
    ##  6  149616 PYRUS      CALLERYANA   MARPOLE            20-30       5   NA        
    ##  7  149617 ACER       PLATANOIDES  KENSINGTON-CEDAR … 30-40      15   1993-12-16
    ##  8  149618 ACER       PLATANOIDES  KENSINGTON-CEDAR … 30-40      14   1993-12-16
    ##  9  149619 ACER       PLATANOIDES  KENSINGTON-CEDAR … 20-30      16   1993-12-16
    ## 10  149625 FRAXINUS   AMERICANA    KENSINGTON-CEDAR … 20-30       7.5 1993-12-03
    ## # … with 146,601 more rows, and abbreviated variable names ¹​height_range_id,
    ## #   ²​diameter, ³​date_planted

**Function \# 5: Arrange the rows first by alphabetical order of
genus_name, then by alphabetical order of neighbourhood name, and lastly
by decreasing diameter**

``` r
vancouver_trees <- vancouver_trees %>%
  arrange(genus_name, neighbourhood_name, desc(diameter))

print(vancouver_trees)
```

    ## # A tibble: 146,611 × 7
    ##    tree_id genus_name species_name neighbourhood_name heigh…¹ diame…² date_pla…³
    ##      <dbl> <fct>      <chr>        <fct>              <fct>     <dbl> <date>    
    ##  1   56770 ABIES      GRANDIS      ARBUTUS-RIDGE      70-80        30 NA        
    ##  2   56771 ABIES      GRANDIS      ARBUTUS-RIDGE      60-70        30 NA        
    ##  3  154997 ABIES      GRANDIS      ARBUTUS-RIDGE      40-50        24 NA        
    ##  4   56773 ABIES      GRANDIS      ARBUTUS-RIDGE      50-60        24 NA        
    ##  5  225364 ABIES      GRANDIS      ARBUTUS-RIDGE      50-60        23 NA        
    ##  6    7235 ABIES      GRANDIS      ARBUTUS-RIDGE      50-60        18 NA        
    ##  7  154254 ABIES      GRANDIS      ARBUTUS-RIDGE      20-30         3 1999-04-22
    ##  8  154255 ABIES      GRANDIS      ARBUTUS-RIDGE      20-30         3 1999-04-22
    ##  9  154263 ABIES      GRANDIS      ARBUTUS-RIDGE      20-30         3 1998-12-01
    ## 10  178611 ABIES      GRANDIS      ARBUTUS-RIDGE      20-30         3 1998-12-01
    ## # … with 146,601 more rows, and abbreviated variable names ¹​height_range_id,
    ## #   ²​diameter, ³​date_planted

<!----------------------------------------------------------------------------->

# Task 2: Special Data Types (10)

For this exercise, you’ll be choosing two of the three tasks below –
both tasks that you choose are worth 5 points each.

But first, tasks 1 and 2 below ask you to modify a plot you made in a
previous milestone. The plot you choose should involve plotting across
at least three groups (whether by facetting, or using an aesthetic like
colour). Place this plot below (you’re allowed to modify the plot if
you’d like). If you don’t have such a plot, you’ll need to make one.
Place the code for your plot below.

<!-------------------------- Start your work below ---------------------------->

``` r
vancouver_trees%>%
  ggplot(aes(fill = neighbourhood_name, x = height_range_id)) +
  geom_bar(position = "stack", alpha=0.5) +
  labs(x = "Height Range of Tree (ft)", y = "Number of Trees", fill = "Neighbourhood Name") +
  scale_x_discrete(guide = guide_axis(angle = 90)) +
  theme(axis.text.x = element_text(size=6), legend.title = element_text(size = 8), legend.text = element_text (size = 4), legend.key.size = unit(0.25, "cm"), legend.key.width = unit(0.25, "cm"))
```

![](mini-project-2_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

The plot I have chosen, and pasted above, is a modified version of the
plot I created for Exercise 2 of Task 2 in Milestone 1 of this project.
The purpose of the plot shown above is to determine which height range
is the most common for trees in Vancouver, while also seeing whether
certain height ranges are concentrated in specific neighborhoods of
Vancouver.

I absolutely love driving in neighborhoods where the trees from both
sides of the road meet to form a sort of canopy at the top. The
information presented in this graph would be useful in determining which
neighborhoods this is most likely to occur in. To get this information,
I would look at the very large height ranges and see which neighborhoods
these tree heights are predominant in.
<!----------------------------------------------------------------------------->

Now, choose two of the following tasks.

1.  Produce a new plot that reorders a factor in your original plot,
    using the `forcats` package (3 points). Then, in a sentence or two,
    briefly explain why you chose this ordering (1 point here for
    demonstrating understanding of the reordering, and 1 point for
    demonstrating some justification for the reordering, which could be
    subtle or speculative.)

2.  Produce a new plot that groups some factor levels together into an
    “other” category (or something similar), using the `forcats` package
    (3 points). Then, in a sentence or two, briefly explain why you
    chose this grouping (1 point here for demonstrating understanding of
    the grouping, and 1 point for demonstrating some justification for
    the grouping, which could be subtle or speculative.)

3.  If your data has some sort of time-based column like a date (but
    something more granular than just a year):

    1.  Make a new column that uses a function from the `lubridate` or
        `tsibble` package to modify your original time-based column. (3
        points)

        - Note that you might first have to *make* a time-based column
          using a function like `ymd()`, but this doesn’t count.
        - Examples of something you might do here: extract the day of
          the year from a date, or extract the weekday, or let 24 hours
          elapse on your dates.

    2.  Then, in a sentence or two, explain how your new column might be
        useful in exploring a research question. (1 point for
        demonstrating understanding of the function you used, and 1
        point for your justification, which could be subtle or
        speculative).

        - For example, you could say something like “Investigating the
          day of the week might be insightful because penguins don’t
          work on weekends, and so may respond differently”.

<!-------------------------- Start your work below ---------------------------->

**Task Number 1**: Produce a new plot that reorders a factor in your
original plot, using the `forcats` package.

``` r
vancouver_trees_ordered_factors <- vancouver_trees %>%
  mutate(height_range_id = fct_relevel(height_range_id, "100+", after = 10))

vancouver_trees_height_levels <- levels(vancouver_trees_ordered_factors$height_range_id)
print(vancouver_trees_height_levels)
```

    ##  [1] "0-10"   "10-20"  "20-30"  "30-40"  "40-50"  "50-60"  "60-70"  "70-80" 
    ##  [9] "80-90"  "90-100" "100+"

``` r
vancouver_trees_ordered_factors%>%
  ggplot(aes(fill = neighbourhood_name, x = height_range_id)) +
  geom_bar(position = "stack", alpha=0.5) +
  labs(x = "Height Range of Tree (ft)", y = "Number of Trees", fill = "Neighbourhood Name") +
  scale_x_discrete(guide = guide_axis(angle = 90)) +
  theme(axis.text.x = element_text(size=6), legend.title = element_text(size = 8), legend.text = element_text (size = 4), legend.key.size = unit(0.25, "cm"), legend.key.width = unit(0.25, "cm"))
```

![](mini-project-2_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

The graph I chose for this task plots the different categories of
**height_range_id** on the x-axis. However, as can be seen from the
initial graph, the order of this factor is not intuitive. The graph
shows, from left to right, “0-10”, “10-20”, “100+”, “20-30”, “30-40”,
“40-50”, “50-60”, “60-70”, “70-80”, “80-90”, “90-100”. The levels in
this factor represent the different categories that a tree can be placed
into depending on its height. Although this variable is a categorical
variable, the numbers have an inherent meaning to them. When looking at
a graph, we expect the numbers, if there are any, to increase from left
to right, regardless of whether they’ve been coded as integers or
factors. This makes it easier to conclude which height range is the most
common, as well as view the normal distribution of tree heights. As a
result, the levels of this factor must be reordered to place “100+” at
the end. The final order of the levels of this factor is as follows:
“0-10”, “10-20”, “20-30”, “30-40”, “40-50”, “50-60”, “60-70”, “70-80”,
“80-90”, “90-100”, “100+”. Unlike the previous order, this order shows
an intuitive, natural increase in the values represented by this factor.
<!----------------------------------------------------------------------------->

<!-------------------------- Start your work below ---------------------------->

**Task Number 2**: Produce a new plot that groups some factor levels
together into an “other” category (or something similar), using the
`forcats` package

The plot produced in Task Number 1 of this section shows the number of
trees in Vancouver that fall in the shown height ranges, as well as the
neighborhoods in which each height range is the most prominent. From the
graph, it is relatively easy to conclude that most trees in Vancouver
are between 20 and 30 feet high. There are, however, approximately 5000
trees that are 60-70 feet high! If I, for example, wanted to go have a
picnic under a tree that was this tall, I would first need to determine
which neighborhood has the greatest number of 60-70 feet high trees in
Vancouver. The graph shown above, however, isn’t very clear in showing
this information. Instead of showing discrete stacked plots, it shows a
slight rainbow gradient due to the large number of neighborhoods in
Vancouver.

In order to make this graph easier to read, I will keep the 5
neighborhoods in Vancouver which have the greatest number of trees as
discrete levels, while lumping all other neighborhoods into the “Other”
category.

``` r
vancouver_trees_lumped <- vancouver_trees %>%
  mutate(neighbourhood_name  = fct_infreq(neighbourhood_name)) %>%
  mutate(neighbourhood_name = fct_lump_n(neighbourhood_name, 5, other_level = "Other")) %>%
  mutate(neighbourhood_name = fct_relevel(neighbourhood_name, "Other", after = 5))

vancouver_trees_lumped%>%
  ggplot(aes(fill = neighbourhood_name, x = height_range_id)) +
  geom_bar(position = "stack", alpha=0.5) +
  labs(x = "Height Range of Tree (ft)", y = "Number of Trees", fill = "Neighbourhood Name") +
  scale_x_discrete(guide = guide_axis(angle = 90)) +
  theme(axis.text.x = element_text(size=6), legend.title = element_text(size = 8), legend.text = element_text (size = 4), legend.key.size = unit(0.25, "cm"), legend.key.width = unit(0.25, "cm"))
```

![](mini-project-2_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

From the graph above, I can, now, easily deduce that if I want to have
the greatest chance of finding a 60-70 foot tree to have a picnic under,
I should head to the Dunbar-Southlands neighborhood. This is one of the
5 neighborhoods in Vancouver with the greatest number of trees, and it
also takes up the greatest proportion of the bar labelled “60-70”, aside
from the “other” group of trees.
<!----------------------------------------------------------------------------->

# Task 3: Modelling

## 3.0 (no points)

Pick a research question, and pick a variable of interest (we’ll call it
“Y”) that’s relevant to the research question. Indicate these.

<!-------------------------- Start your work below ---------------------------->

**Research Question**: Which genus of trees, among the top 5 genera in
Vancouver, has the greatest diameter on average?

**Variable of interest**: diameter

<!----------------------------------------------------------------------------->

## 3.1 (5 points)

Fit a model or run a hypothesis test that provides insight on this
variable with respect to the research question. Store the model object
as a variable, and print its output to screen. We’ll omit having to
justify your choice, because we don’t expect you to know about model
specifics in STAT 545.

- **Note**: It’s OK if you don’t know how these models/tests work. Here
  are some examples of things you can do here, but the sky’s the limit.

  - You could fit a model that makes predictions on Y using another
    variable, by using the `lm()` function.
  - You could test whether the mean of Y equals 0 using `t.test()`, or
    maybe the mean across two groups are different using `t.test()`, or
    maybe the mean across multiple groups are different using `anova()`
    (you may have to pivot your data for the latter two).
  - You could use `lm()` to test for significance of regression.

<!-------------------------- Start your work below ---------------------------->

For the research question stated above, my null hypothesis is the
following: there is no difference in the diameter of trees in Vancouver
between different genera. In the previous milestone, I had plotted the
“Diameter of Tree at Breast Height (ft)” on the x-axis and the “Density”
on the y-axis of a density plot. From this, we could see that diameter
followed a normal distribution. As such, I will first perform a multiple
group comparison test known as a one-way analysis of variance (ANOVA) to
see if there is a statistically significant difference between the
groups.

``` r
vancouver_trees_top5 <- vancouver_trees %>%
  group_by(genus_name) %>%
  summarise(n=n()) %>%
  arrange(desc(n)) %>%
  slice(1:5)

vancouver_trees_top5genera <- vancouver_trees %>%
  filter(genus_name %in% vancouver_trees_top5$genus_name)

res_aov <- aov(diameter ~ genus_name, vancouver_trees_top5genera)

print(res_aov)
```

    ## Call:
    ##    aov(formula = diameter ~ genus_name, data = vancouver_trees_top5genera)
    ## 
    ## Terms:
    ##                 genus_name Residuals
    ## Sum of Squares      298791   6358208
    ## Deg. of Freedom          4     87013
    ## 
    ## Residual standard error: 8.548212
    ## Estimated effects may be unbalanced

<!----------------------------------------------------------------------------->

## 3.2 (5 points)

Produce something relevant from your fitted model: either predictions on
Y, or a single value like a regression coefficient or a p-value.

- Be sure to indicate in writing what you chose to produce.
- Your code should either output a tibble (in which case you should
  indicate the column that contains the thing you’re looking for), or
  the thing you’re looking for itself.
- Obtain your results using the `broom` package if possible. If your
  model is not compatible with the broom function you’re needing, then
  you can obtain your results by some other means, but first indicate
  which broom function is not compatible.

<!-------------------------- Start your work below ---------------------------->

To determine whether there is a statistically significant difference in
diameter of trees at breast height between trees in different genera, I
will now use the `broom` package to obtain the p-value associated with
the ANOVA.

``` r
broom::tidy(res_aov)
```

    ## # A tibble: 2 × 6
    ##   term          df    sumsq  meansq statistic p.value
    ##   <chr>      <dbl>    <dbl>   <dbl>     <dbl>   <dbl>
    ## 1 genus_name     4  298791. 74698.      1022.       0
    ## 2 Residuals  87013 6358208.    73.1       NA       NA

If we take a look at the *p.value* column in the tibble above, the
p-value associated with this test is 0. in this test, 0 represents a
value of 0.0001, which according to the 0.05 cut-off, is considered
statistically significant. According to this, we can conclude that,
based on this current data set, we are able to reject the null
hypothesis, and, therefore, the average diameter of trees belonging to
different genera in Vancouver are ***significantly different***.

Now that I gotten a statistically significant difference from the
hypothesis test above, I will be performing a post-hoc test (Tukey’s
Test) to determine which groups are different and to see which
particular genera has a significantly greater diameter, on average, than
others. This test is not done as part of a particular task; however, I
would like to finish answering my question, so I am including this extra
section!

``` r
res_tukey <- TukeyHSD(res_aov)

broom::tidy(res_tukey)
```

    ## # A tibble: 10 × 7
    ##    term       contrast         null.value estimate conf.low conf.high adj.p.va…¹
    ##    <chr>      <chr>                 <dbl>    <dbl>    <dbl>     <dbl>      <dbl>
    ##  1 genus_name FRAXINUS-ACER             0   -1.10    -1.40    -0.801    5.66e-14
    ##  2 genus_name PRUNUS-ACER               0    3.28     3.10     3.46     0       
    ##  3 genus_name QUERCUS-ACER              0    4.61     4.29     4.93     0       
    ##  4 genus_name TILIA-ACER                0    2.88     2.57     3.19     0       
    ##  5 genus_name PRUNUS-FRAXINUS           0    4.37     4.07     4.68     0       
    ##  6 genus_name QUERCUS-FRAXINUS          0    5.71     5.30     6.11     0       
    ##  7 genus_name TILIA-FRAXINUS            0    3.98     3.59     4.37     0       
    ##  8 genus_name QUERCUS-PRUNUS            0    1.33     1.01     1.66     1.78e-14
    ##  9 genus_name TILIA-PRUNUS              0   -0.395   -0.708   -0.0822   5.20e- 3
    ## 10 genus_name TILIA-QUERCUS             0   -1.73    -2.14    -1.32     0       
    ## # … with abbreviated variable name ¹​adj.p.value

``` r
vancouver_trees_top5genera %>%
  group_by(genus_name) %>%
  summarize(average_diameter = mean(diameter)) %>%
  ggplot(mapping = aes(x = genus_name, y = average_diameter)) +
  geom_col(col = "steelblue1", fill = "steelblue1", alpha = 0.5) +
  labs(x = "Name of Genus", y = "Average Diameter of Tree at Breast Height (ft)")
```

![](mini-project-2_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->
According to the graph shown above, the genus *Quercus* has the greatest
average diameter of trees at breast height, amongst the 5 most prevalent
genera in Vancouver. Based on the Tukey’s test performed earlier, we can
see that Querus trees have a significantly larger diameter than Acer (p
= 0.000000e+00), Fraxinus (p = 0.000000e+00), Prunus (p = 1.776357e-14),
Tilia (p = 0.000000e+00). As we can see, the p-values obtained here are
extremely small indicating very high statistical significance which I
can only hope to achieve in my thesis project results!
<!----------------------------------------------------------------------------->

# Task 4: Reading and writing data

Get set up for this exercise by making a folder called `output` in the
top level of your project folder / repository. You’ll be saving things
there.

## 3.1 (5 points)

Take a summary table that you made from Milestone 1 (Task 4.2), and
write it as a csv file in your `output` folder. Use the `here::here()`
function.

- **Robustness criteria**: You should be able to move your Mini Project
  repository / project folder to some other location on your computer,
  or move this very Rmd file to another location within your project
  repository / folder, and your code should still work.
- **Reproducibility criteria**: You should be able to delete the csv
  file, and remake it simply by knitting this Rmd file.

<!-------------------------- Start your work below ---------------------------->

The summary table used here is the *vancouver_trees_data_neighborhood*
table from the last exercise of Task 4.2.

``` r
vancouver_trees_data_neighborhood <- vancouver_trees %>%
  group_by(neighbourhood_name) %>%
  summarize(n=n())

write_csv(vancouver_trees_data_neighborhood, here::here("output", "summary.csv"))
```

<!----------------------------------------------------------------------------->

## 3.2 (5 points)

Write your model object from Task 3 to an R binary file (an RDS), and
load it again. Be sure to save the binary file in your `output` folder.
Use the functions `saveRDS()` and `readRDS()`.

- The same robustness and reproducibility criteria as in 3.1 apply here.

<!-------------------------- Start your work below ---------------------------->

``` r
saveRDS(res_aov, here::here("output", "ANOVA Results"))
```

<!----------------------------------------------------------------------------->

# Tidy Repository

Now that this is your last milestone, your entire project repository
should be organized. Here are the criteria we’re looking for.

## Main README (3 points)

There should be a file named `README.md` at the top level of your
repository. Its contents should automatically appear when you visit the
repository on GitHub.

Minimum contents of the README file:

- In a sentence or two, explains what this repository is, so that
  future-you or someone else stumbling on your repository can be
  oriented to the repository.
- In a sentence or two (or more??), briefly explains how to engage with
  the repository. You can assume the person reading knows the material
  from STAT 545A. Basically, if a visitor to your repository wants to
  explore your project, what should they know?

Once you get in the habit of making README files, and seeing more README
files in other projects, you’ll wonder how you ever got by without them!
They are tremendously helpful.

## File and Folder structure (3 points)

You should have at least four folders in the top level of your
repository: one for each milestone, and one output folder. If there are
any other folders, these are explained in the main README.

Each milestone document is contained in its respective folder, and
nowhere else.

Every level-1 folder (that is, the ones stored in the top level, like
“Milestone1” and “output”) has a `README` file, explaining in a sentence
or two what is in the folder, in plain language (it’s enough to say
something like “This folder contains the source for Milestone 1”).

## Output (2 points)

All output is recent and relevant:

- All Rmd files have been `knit`ted to their output, and all data files
  saved from Task 4 above appear in the `output` folder.
- All of these output files are up-to-date – that is, they haven’t
  fallen behind after the source (Rmd) files have been updated.
- There should be no relic output files. For example, if you were
  knitting an Rmd to html, but then changed the output to be only a
  markdown file, then the html file is a relic and should be deleted.

Our recommendation: delete all output files, and re-knit each
milestone’s Rmd file, so that everything is up to date and relevant.

PS: there’s a way where you can run all project code using a single
command, instead of clicking “knit” three times. More on this in STAT
545B!

## Error-free code (1 point)

This Milestone 1 document knits error-free, and the Milestone 2 document
knits error-free.

## Tagged release (1 point)

You’ve tagged a release for Milestone 1, and you’ve tagged a release for
Milestone 2.

### Attribution

Thanks to Victor Yuan for mostly putting this together.
