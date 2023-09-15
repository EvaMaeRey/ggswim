
<!-- README.md is generated from README.Rmd. Please edit that file -->

# ggswim

<p align="center">

<img src="man/figures/ggswim.png" alt="ggswim hex logo" width="150" align="right"/>

</p>
<!-- badges: start -->

[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://lifecycle.r-lib.org/articles/stages.html#experimental)
[![R-CMD-check](https://github.com/CHOP-CGTInformatics/ggswim/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/CHOP-CGTInformatics/ggswim/actions/workflows/R-CMD-check.yaml)
<!-- badges: end -->

The ggswim package provides an easy set of commands to create “swimmer”
plots.

## Installation

You can install the development version of ggswim like so:

``` r
devtools::install_github("CHOP-CGTInformatics/ggswim")
```

## Background

At it’s core, the ggswim package leverages the already fantastic
capabilities of ggplot2 and seeks to preserve as much of the flexibility
and structure that it provides as possible. Therefore, when using ggswim
you will likely notice that much of the documentation and underlying
functionality wrap it! So what makes it different? ggswim provides some
opinionated decisions to steer plots and the data provided in a way that
makes swimmer plots easily accessible and easy to build upon.

Let’s start with some simple examples of how this works.

## A Sample Data Set

First we’ll define a few sets of data to work with:

- `patient_data`: a dataframe containing per-patient, record-level data
- `dose_data_a` & `dose_data_b`: a dataframe with two doses
  corresponding to multiple potential time points for the patients in
  `patient_data`
- `dose_type`: a dataframe with two drug distrbution method types
  identified using labels and names

``` r
set.seed(123)
patient_data <-
  tibble::tibble(
    id = 1:4,
    trt = rep_len(c("Drug A", "Drug B"), length.out = 4),
    time_to_last_followup = c(5,2,4,7),
    time_to_death = ifelse(id %% 2, time_to_last_followup, NA),
    end_time = c(5,2,4,7)
  ) |> dplyr::mutate(time_start = 0) |>
  tidyr::pivot_longer(cols = c(time_start, time_to_last_followup),
                      values_to = "time",
                      names_to = "treatment_group")

dose_data_a <- tibble::tibble(
  id2 = c(1,1,1,2,2,2,3,4,4,4),
  type = sample(c("Dose I", "Dose II"), 10, replace = TRUE),
  time = c(0,1.5,2,0,0.5,1,1.25,2,3,7)
)

dose_data_b <- tibble::tibble(
  id3 = c(1,1,2,3,4),
  type2 = sample(c("Dose III", "Dose IV"), 5, replace = TRUE),
  time = c(0.5, 0.75, 0.25, 3, 6)
)

dose_type <- tibble::tibble(
  id4 = c(1,2,3,4),
  label = c("💊", "💉", "💊", "💉"),
  name = c("Method A", "Method B", "Method A", "Method B"),
  time = c(.15, 0.1, 2.25, 5.5)
)
```

``` r
library(ggswim)
library(ggplot2)

p <- patient_data |> 
  ggswim(
    aes(y = id,
        x = time,
        fill = trt), width = 0.1
  ) +
  add_marker(data = dose_data_a,
             mapping = aes(
               x = time,
               y = id2,
               shape = type,
               color = type
             ), size = 3) +
  add_marker(data = dose_data_b,
             mapping = aes(
               x = time,
               y = id3,
               shape = type2,
               color = type2,
             ), size = 5) +
  add_marker(
    data = dose_type,
    mapping = aes(x = time,
        y = id4,
        label = label,
        color = name),
    label.size = NA, fill = NA, size = 5
  )

p +
  ggplot2::labs(x = "Time", y = "Subject ID", color = "Markers") +
  ggplot2::ggtitle("My Swim Plot") +
  ggplot2::theme_minimal() +
  ggplot2::scale_color_manual(name = "Markers",
                              values = c("firebrick", "tomato", "orange", "chartreuse2", NA, NA)) +
  ggplot2::scale_shape_manual(name = "Markers",
                              values = c(19,15,8,18)) +
  ggplot2::scale_fill_manual(name = "Lanes",
                             values = c("steelblue", "cyan", "skyblue", "steelblue4"))
```

<img src="man/figures/README-unnamed-chunk-3-1.png" width="100%" />

### Future Plans

We have a lot of work to do with ggswim! Future plans include:

- A full test suite
- Enforced checks
- Vignettes and documentation
