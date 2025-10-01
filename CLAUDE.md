# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an R-based data analysis project examining political violence in the United States using data from The Prosecution Project Dataset. The project analyzes prosecution data from 1990 to present, focusing on ideologically-motivated violent crimes and their outcomes.

## Development Environment

This project uses R with `renv` for dependency management:

- **R Version**: 4.5.1
- **Primary output**: Quarto document (`violence.qmd`) that renders to HTML
- The project uses `renv` to manage R package dependencies - the lockfile is `renv.lock`
- Key packages: tidyverse, janitor, RColorBrewer, lubridate
- General R guidance: use tidyverse packages such as dplyr, ggplot2, tidyr, readr, lubridate, forcats, and purrr for data manipulation and visualization. Use the fs package for file system operations.

## Common Commands

### Rendering the Analysis
```r
quarto render violence.qmd
```

This generates `violence.html` from the Quarto markdown file.

### Working with renv
```r
# Restore project dependencies
renv::restore()

# Update lockfile after installing new packages
renv::snapshot()

# Check project status
renv::status()
```

### Running R Code
The `xx.R` file contains exploratory and development code that is not part of the final report.

## Data Structure

### Data Files
- `data/tpp-2025-09-30-general.csv`: Main prosecution dataset
- `data/tpp-2025-09-30-capitol-protest.csv`: Capitol protest-specific data
- `data/tpp-2025-09-30-summer-fall-2020.csv`: Summer/fall 2020 protest data

### Key Data Processing Steps
1. **Data Cleaning**: The analysis removes cases without clear guilty verdicts (pending, not guilty, hung jury, etc.)
2. **US-only**: Filters for `location_country == "United States"`
3. **Ideological Focus**: Excludes crimes not politically motivated or with unclear motivation
4. **Violence Filter**: Focuses only on completed, planned, or attempted violent crimes (excludes threats)
5. **Duplicate Handling**: Removes duplicates where defendants appear multiple times for the same charge
6. **Death Counts**: Carefully handles cases where defendants are prosecuted at multiple jurisdictional levels to avoid double-counting deaths

### Custom Functions

**`jurisdiction_extract(string1, string2)`**: Determines jurisdiction level (Federal, State, District, City, County, Unknown) from the `jurisdiction` and `name_of_case` columns. Used to identify defendants prosecuted at multiple levels.

### Important Data Transformations

- **Date parsing**: The `date` column is cleaned and parsed with `lubridate::mdy()`
- **Ideology simplification**: Consolidates detailed ideological affiliations into broader categories (Leftist, Rightist, etc.) in `ideology_simple` column
- **Violence classification**: Creates `method_type` column categorizing criminal methods as "violent" or "non-violent"
- **Defendant appearance numbers**: Extracts appearance numbers from `full_legal_name` into separate column
- **Case name standardization**: Extensive string cleaning to standardize case name formats

## Architecture

This is a literate programming project built with Quarto:

- **violence.qmd**: Main analysis document containing R code chunks and narrative
- **xx.R**: Scratch file for exploratory analysis and function development (gitignored)
- **data/**: CSV files containing prosecution data
- **violence.html**: Rendered output
- **violence_files/**: Supporting files generated during rendering (plots, etc.)

The analysis workflow in `violence.qmd`:
1. Load and clean data
2. Filter to relevant cases (US, guilty verdicts, ideologically motivated, violent)
3. Handle duplicates and multi-jurisdiction prosecutions
4. Generate summary tables and visualizations by ideology and year
5. Calculate death counts with careful deduplication
6. Create trend charts showing prosecutions over time with presidential terms marked

## Notes

- The project analyzes politically-motivated prosecutions, not all violent crimes
- Data is structured by defendant, not by incident - multiple defendants can be associated with one incident
- Some defendants appear multiple times if prosecuted at multiple jurisdictional levels
- Death counts require careful handling to avoid overcounting in multi-jurisdiction cases
