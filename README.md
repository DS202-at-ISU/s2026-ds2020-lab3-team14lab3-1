
<!-- README.md is generated from README.Rmd. Please edit the README.Rmd file -->

# Lab report \#3 - instructions

Follow the instructions posted at
<https://ds202-at-isu.github.io/labs.html> for the lab assignment. The
work is meant to be finished during the lab time, but you have time
until Monday evening to polish things.

Include your answers in this document (Rmd file). Make sure that it
knits properly (into the md file). Upload both the Rmd and the md file
to your repository.

All submissions to the github repo will be automatically uploaded for
grading once the due date is passed. Submit a link to your repository on
Canvas (only one submission per team) to signal to the instructors that
you are done with your submission.

# Lab 3: Avenger’s Peril

## As a team

Extract from the data below two data sets in long form `deaths` and
`returns`

``` r
av <- read.csv("https://raw.githubusercontent.com/fivethirtyeight/data/master/avengers/avengers.csv", stringsAsFactors = FALSE)
head(av)
```

    ##                                                       URL
    ## 1           http://marvel.wikia.com/Henry_Pym_(Earth-616)
    ## 2      http://marvel.wikia.com/Janet_van_Dyne_(Earth-616)
    ## 3       http://marvel.wikia.com/Anthony_Stark_(Earth-616)
    ## 4 http://marvel.wikia.com/Robert_Bruce_Banner_(Earth-616)
    ## 5        http://marvel.wikia.com/Thor_Odinson_(Earth-616)
    ## 6       http://marvel.wikia.com/Richard_Jones_(Earth-616)
    ##                    Name.Alias Appearances Current. Gender Probationary.Introl
    ## 1   Henry Jonathan "Hank" Pym        1269      YES   MALE                    
    ## 2              Janet van Dyne        1165      YES FEMALE                    
    ## 3 Anthony Edward "Tony" Stark        3068      YES   MALE                    
    ## 4         Robert Bruce Banner        2089      YES   MALE                    
    ## 5                Thor Odinson        2402      YES   MALE                    
    ## 6      Richard Milhouse Jones         612      YES   MALE                    
    ##   Full.Reserve.Avengers.Intro Year Years.since.joining Honorary Death1 Return1
    ## 1                      Sep-63 1963                  52     Full    YES      NO
    ## 2                      Sep-63 1963                  52     Full    YES     YES
    ## 3                      Sep-63 1963                  52     Full    YES     YES
    ## 4                      Sep-63 1963                  52     Full    YES     YES
    ## 5                      Sep-63 1963                  52     Full    YES     YES
    ## 6                      Sep-63 1963                  52 Honorary     NO        
    ##   Death2 Return2 Death3 Return3 Death4 Return4 Death5 Return5
    ## 1                                                            
    ## 2                                                            
    ## 3                                                            
    ## 4                                                            
    ## 5    YES      NO                                             
    ## 6                                                            
    ##                                                                                                                                                                              Notes
    ## 1                                                                                                                Merged with Ultron in Rage of Ultron Vol. 1. A funeral was held. 
    ## 2                                                                                                  Dies in Secret Invasion V1:I8. Actually was sent tto Microverse later recovered
    ## 3 Death: "Later while under the influence of Immortus Stark committed a number of horrible acts and was killed.'  This set up young Tony. Franklin Richards later brought him back
    ## 4                                                                               Dies in Ghosts of the Future arc. However "he had actually used a hidden Pantheon base to survive"
    ## 5                                                      Dies in Fear Itself brought back because that's kind of the whole point. Second death in Time Runs Out has not yet returned
    ## 6                                                                                                                                                                             <NA>

Get the data into a format where the five columns for Death\[1-5\] are
replaced by two columns: Time, and Death. Time should be a number
between 1 and 5 (look into the function `parse_number`); Death is a
categorical variables with values “yes”, “no” and ““. Call the resulting
data set `deaths`.

Similarly, deal with the returns of characters.

Based on these datasets calculate the average number of deaths an
Avenger suffers.

## Individually

For each team member, copy this part of the report.

Each team member picks one of the statements in the FiveThirtyEight
[analysis](https://fivethirtyeight.com/features/avengers-death-comics-age-of-ultron/)
and fact checks it based on the data. Use dplyr functionality whenever
possible.

### FiveThirtyEight Statement

> Quote the statement you are planning to fact-check.

### Include the code

Make sure to include the code to derive the (numeric) fact for the
statement

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.6
    ## ✔ forcats   1.0.1     ✔ stringr   1.6.0
    ## ✔ ggplot2   4.0.1     ✔ tibble    3.3.1
    ## ✔ lubridate 1.9.4     ✔ tidyr     1.3.2
    ## ✔ purrr     1.2.1     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(readr)

# deaths
deaths <- av %>%
  select(Name.Alias, starts_with("Death")) %>%
  pivot_longer(
    cols = starts_with("Death"),
    names_to = "Time",
    values_to = "Death"
  ) %>%
  mutate(
    Time = parse_number(Time),
    Death = case_when(
      Death == "YES" ~ "yes",
      Death == "NO"  ~ "no",
      TRUE ~ ""
    ),
    Death = factor(Death, levels = c("yes", "no", ""))
  )

head(deaths)
```

    ## # A tibble: 6 × 3
    ##   Name.Alias                     Time Death
    ##   <chr>                         <dbl> <fct>
    ## 1 "Henry Jonathan \"Hank\" Pym"     1 "yes"
    ## 2 "Henry Jonathan \"Hank\" Pym"     2 ""   
    ## 3 "Henry Jonathan \"Hank\" Pym"     3 ""   
    ## 4 "Henry Jonathan \"Hank\" Pym"     4 ""   
    ## 5 "Henry Jonathan \"Hank\" Pym"     5 ""   
    ## 6 "Janet van Dyne"                  1 "yes"

``` r
#retruns
returns <- av %>%
  select(Name.Alias, starts_with("Return")) %>%
  pivot_longer(
    cols = starts_with("Return"),
    names_to = "Time",
    values_to = "Return"
  ) %>%
  mutate(
    Time = parse_number(Time),
    Return = case_when(
      Return == "YES" ~ "yes",
      Return == "NO"  ~ "no",
      TRUE ~ ""
    ),
    Return = factor(Return, levels = c("yes", "no", ""))
  )

head(returns)
```

    ## # A tibble: 6 × 3
    ##   Name.Alias                     Time Return
    ##   <chr>                         <dbl> <fct> 
    ## 1 "Henry Jonathan \"Hank\" Pym"     1 "no"  
    ## 2 "Henry Jonathan \"Hank\" Pym"     2 ""    
    ## 3 "Henry Jonathan \"Hank\" Pym"     3 ""    
    ## 4 "Henry Jonathan \"Hank\" Pym"     4 ""    
    ## 5 "Henry Jonathan \"Hank\" Pym"     5 ""    
    ## 6 "Janet van Dyne"                  1 "yes"

``` r
# average number of deaths
avg_deaths <- deaths %>%
  group_by(Name.Alias) %>%
  summarise(num_deaths = sum(Death == "yes")) %>%
  summarise(avg_num_deaths = mean(num_deaths))

avg_deaths
```

    ## # A tibble: 1 × 1
    ##   avg_num_deaths
    ##            <dbl>
    ## 1          0.546

The average number of deaths per Avenger is approximately 0.546. This
means that, on average, each Avenger has died a little more than half a
time based on the recorded data.

### Include your answer

Include at least one sentence discussing the result of your
fact-checking endeavor.

Upload your changes to the repository. Discuss and refine answers as a
team.

The proportion of Avengers who return after dying is approximately 0.64.
Since this is greater than 0.5, it shows that most Avengers do return
after dying. Therefore, the statement is supported by the data.

``` r
library(tidyverse)
library(readr)

# Add ID to match rows
av2 <- av %>% mutate(id = row_number())

# deaths long
deaths_long <- av2 %>%
  select(id, starts_with("Death")) %>%
  pivot_longer(
    cols = starts_with("Death"),
    names_to = "Time",
    values_to = "Death"
  ) %>%
  mutate(Time = parse_number(Time))

# returns long
returns_long <- av2 %>%
  select(id, starts_with("Return")) %>%
  pivot_longer(
    cols = starts_with("Return"),
    names_to = "Time",
    values_to = "Return"
  ) %>%
  mutate(Time = parse_number(Time))

# join and analyze
result <- deaths_long %>%
  left_join(returns_long, by = c("id", "Time")) %>%
  filter(Death == "YES") %>%
  summarise(prop_returned = mean(Return == "YES", na.rm = TRUE))

result
```

    ## # A tibble: 1 × 1
    ##   prop_returned
    ##           <dbl>
    ## 1         0.640
