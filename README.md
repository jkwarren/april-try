# april-try
Ogden Example 4
================
Jessica Warren

# Ogden4

Modifying the input parameter and transition function files we’ve been
using in order to match Ogden as closely as possible.

## Goal of Ogden4

-   see the pattern between the observed and the predicted numbers for
    larvae, nymphs, and adults
-   show that the this model gives the same results as the Ogden plots
    shown below

<img src="https://raw.githubusercontent.com/dallenmidd/IxPopDyMod/master/data-raw/ogden2005/make_ogden_fig4/ogden_fig4a.png" alt="Ogden 4a" width="572"/>

<img src="https://raw.githubusercontent.com/dallenmidd/IxPopDyMod/master/data-raw/ogden2005/make_ogden_fig4/ogden_fig4b.png" alt="Ogden 4b" width="572"/>

<img src="https://raw.githubusercontent.com/dallenmidd/IxPopDyMod/master/data-raw/ogden2005/make_ogden_fig4/ogden_fig4c.png" alt="Alt text" width="572"/>

## identified differences between Ogden and this implementation:

-   Ogden has density dependent reduction in fecundity of egg-laying
    females
-   Ogden handles host finding differently
    -   host finding probability from Ogden is weekly, and diff for
        adults vs larvae and nymphs. Weekly prob is (.0089 \* 200 ^
        .515) = 0.13 for larvae and nymphs, (0.06 \* 20 ^ .515) =
        0.2806608 for adults. We’ve been using daily probability of
        4e-4… my probability math is rusty, not sure how we’d convert.
    -   Ogden uses Fig 3 curve to determine prob of active questing, vs
        our briere function approach - input weather data… I poked
        around a bit online for the weather data from the statio in
        Ontario they use, but no luck

## installation

Install the package needed with:

    install.packages("IxPopDyMod")
    library(IxPopDyMod)
    Library(readr)

    library(tidyverse)
    pacman::p_load(gridExtra)

## Create the tables

This puts together the tables for:

-   predicted larvae `pl`
-   predicted nymphs `pn`
-   predicted adults `pa`
-   observed larvae `ol`
-   observed nymphs `on`
-   observed adults `oa`

<!-- -->

    pl <- read_csv('data-raw/ogden2005/make_ogden_fig4/predicted_larvae.csv',
                   col_names = c('jday', 'n')) %>% mutate(group = 'pl')
    pn <- read_csv('data-raw/ogden2005/make_ogden_fig4/predicted_nymphs.csv',
                   col_names = c('jday', 'n')) %>% mutate(group = 'pn')
    pa <- read_csv('data-raw/ogden2005/make_ogden_fig4/predicted_adults.csv',
                   col_names = c('jday', 'n')) %>% mutate(group = 'pa')
    ol <- read_csv('data-raw/ogden2005/make_ogden_fig4/observed_larvae.csv',
                   col_names = c('jday', 'n')) %>% mutate(group = 'ol')
    on <- read_csv('data-raw/ogden2005/make_ogden_fig4/observed_nymphs.csv',
                   col_names = c('jday', 'n')) %>% mutate(group = 'on')
    oa <- read_csv('data-raw/ogden2005/make_ogden_fig4/observed_adults.csv',
                   col_names = c('jday', 'n')) %>% mutate(group = 'oa')

#### If Error:`'data-raw/ogden2005/make_ogden_fig4/predicted_larvae.csv' does not exist in current working directory` appears

1.  Go to IxPopDyMod
2.  Go to -\> data-raw
3.  -\> ogden2005
4.  -\> make_ogden_fig4
5.  find the file needed
    -   ex. “predicted_larvae.csv”
6.  click here
7.  click on “permalink”
8.  `read_csv('` paste here `',`…

### Looking At What This Does

`col_names = c('jday', 'n')) %>% mutate(group =' '`

`col_names` specifies what columns to make with the data. Here, `jday`
(Julian Date) and `n` () are what we want to assign to the data already
in the `.csv`

`%>% mutate(group = '')` is saying mutate, or create a new table while
preserving the old one, and add on the column group that labels these
rows as on of the variable names (`on`, `pn`, ect.).

### Seeing What Was Made

If we enter these tables, R will show us the tibbles that were made

    pl 
    pn 
    pa 
    ol 
    on 
    oa

#### Example

showing `pa`

    pa
    #> # A tibble: 46 × 3
    #>     jday         n group
    #>    <dbl>     <dbl> <chr>
    #>  1  15.6 -0.00383  pa   
    #>  2  32.1 -0.00273  pa   
    #>  3  46.9 -0.00273  pa   
    #>  4  61.1 -0.00273  pa   
    #>  5  75.4 -0.00273  pa   
    #>  6  93.3  0.00383  pa   
    #>  7  98.7  0.0344   pa   
    #>  8 125.   0.0202   pa   
    #>  9 154.  -0.000546 pa   
    #>  10 129.   0.0115   pa   
    #> # … with 36 more rows

## Putting Together The Tibbles Into One

    # merge all data
    df <- rbind(pl, pn, pa, ol, on, oa) %>%
      arrange(jday, group) %>%
      mutate(jday = round(jday))

`rbind` takes all the groups and “glues” them together so they end up in
the in the same columns : jday, n, group. Then this is put into the
variable `df` (for data frame).

\########so how does it arrange by group?##################

`arrange` takes the data and arranges the data in the columns by `jday`
from least to greatest and then by group.

lastly, we `mutate` `jday` so that the day is rounded to a whole number

## Making the Figures

The data in the tibble df is now broken up into figures: `fig4a`,
`fig4b`, `fig4c`, to compare each stages predicted and observed values

    fig4a <- df %>%
      filter(group %in% c('ol', 'pl')) %>%
      ggplot(aes(jday, n, color = group)) +
      geom_point() +
      geom_line()
      
    fig4b <- df %>%
      filter(group %in% c('on', 'pn')) %>%
      ggplot(aes(jday, n, color = group)) +
      geom_point() +
      geom_line()
      
    fig4c <- df %>%
      filter(group %in% c('oa', 'pa')) %>%
      ggplot(aes(jday, n, color = group)) +
      geom_point() +
      geom_line()
      

### What Each Part is Doing

looking at fig4a as an example…

`filter` filters out everything besides the data in groups `ol` and `pl`

`ggplot` sets up a plot with the data

`aes(jday, n, color = group)` is under “aesthetics” where we specify to
the ggplot command which variables we what, where, and how they are
shown. Under the general command`aes(x,y,...)`:

-   `jday`
    -   regularly represented, as the x
-   `n`
    -   regular, as the y
-   `group`
    -   assigns a color to the type in group
    -   ex. a color to `ol` and `pl` respectively

next is `+`, used to express the data how we want on the
ggplot.`geom_point()` sets up the data as points on the plot. `+` then
`geom_line()` draws lines between the points on the graph

## Visualizing the Data

Under the gridExtra package we can arrange the `fig4a`, `fig4b`, and
`fig4c` to show up as three separate graphs in the plots tab.

    fig4 <- gridExtra::grid.arrange(fig4a, fig4b, fig4c)

With these graphs, we can see that they match up to the actual Ogden
example 4 graphs and that this program replicates them.
