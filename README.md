
<!-- README.md is generated from README.Rmd. Please edit that file -->

# ggswim

<p align="center">

<img src="man/figures/ggswim.png" alt="ggswim hex logo" width="150" align="right"/>

</p>
<!-- badges: start -->

[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://lifecycle.r-lib.org/articles/stages.html#experimental)
[![R-CMD-check](https://github.com/CHOP-CGTInformatics/ggswim/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/CHOP-CGTInformatics/ggswim/actions/workflows/R-CMD-check.yaml)
[![Codecov test
coverage](https://codecov.io/gh/CHOP-CGTInformatics/ggswim/branch/main/graph/badge.svg)](https://app.codecov.io/gh/CHOP-CGTInformatics/ggswim?branch=main)
<!-- badges: end -->

The ggswim package provides a convenient set of commands to easily
create “swimmer” plots.

## Installation

ggswim is in its very early stages, but in active development! Expect to
see many changes in the near future to the API and documentation.

You can install the development version of ggswim like so:

``` r
devtools::install_github("CHOP-CGTInformatics/ggswim")
```

## Background

ggswim provides developers with a set of commands that make it easy to
create beautiful swimmer plots using the architecture they may already
be familiar with in ggplot2. The main functions that power ggswim are
wrapped around core ggplot2 functions, leveraging the ability to add
layers at a whim for whatever the developer’s display requires.

The ggswim package reduces the complexity behind making reproducible
legend displays that correctly show points of interest and are key to
telling the story of the swimmer plot.

Let’s show a quick example of how this works below!

## A Sample Data Set

ggswim comes packaged with a sample dataset called `patient_status`,
which is a list containing 3 tibbles:

- `patient_status`
- `adverse_events`
- `medication_administration`

At present, a little bit of some data manipulation is required to get
`patient_status` from a typically expected structure to one that is
ready for swimming:

``` r
library(ggswim)
library(ggplot2)

patient_data <- ggswim::patient_data

patient_status <- patient_data$patient_status
adverse_events <- patient_data$adverse_events
medication_administration <- patient_data$medication_administration

patient_status <- patient_status |>
  tidyr::pivot_longer(c(time_start, time_end)) |>
  dplyr::mutate(.by = subject_id,
                time_sorting = sum(value)) |> 
  dplyr::arrange(cohort, time_sorting) |>
  dplyr::mutate(subject_id = factor(subject_id, levels = unique(subject_id)))
```

Now that the data preparation is out of the way, we’re ready to swim!

``` r
  ggswim(data = patient_status,
         mapping = aes(x = value,
                       y = subject_id,
                       fill = cohort)) +
  add_marker(data = adverse_events,
             mapping = aes(x = time_of_event,
                           y = subject_id,
                           color = adverse_event_name,
                           shape = adverse_event_name),
             size = 5) +
  add_marker(data = medication_administration,
             mapping = aes(x = time_of_event,
                           y = subject_id,
                           label = medication,
                           color = name),
             label.size = NA, fill = NA, size = 5) +
  ggplot2::labs(x = "Time", y = "Subject ID", color = "Markers") +
  ggplot2::ggtitle("My Swim Plot") +
  ggplot2::theme_minimal() +
  ggplot2::scale_color_manual(
    name = "Markers",
    values = c("firebrick", "forestgreen", NA, NA, "purple")
  ) +
  ggplot2::scale_shape_manual(
    name = "Markers",
    values = c(19, 13, 9)
  ) +
  ggplot2::scale_fill_manual(
    name = "Lanes",
    values = c("steelblue1", "goldenrod1")
  )
```

<img src="man/figures/README-unnamed-chunk-3-1.png" width="100%" />

### Future Plans

We have a lot of work to do with ggswim! Future plans include:

- A full test suite
- Better checks for ggswim-compatible data structures
- Additional vignettes and improved documentation
