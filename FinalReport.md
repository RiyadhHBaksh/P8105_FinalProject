Connecting Political Views and Health Metrics
================
Riyadh Baksh (rhb2152), Anika Mitchell (am5088), Jeong Yun Yang (jy3306)
2024-12-03

``` r
library(tidyverse)
```

``` r
election =
  read_csv("data/election.csv") |>
  filter(party=="DEMOCRAT" | party=="REPUBLICAN") |>
  group_by(county_fips,county_name,state_po,year,party,totalvotes) |>
  summarize(
    votes = sum(candidatevotes)
  ) |>
  mutate(
    percent = votes/totalvotes,
    county_fips = sprintf("%05d",county_fips),
    year_county = paste(year,county_fips,sep="-")
  ) |>
  select(-totalvotes,-votes)
```

``` r
health =
  read_csv("data/health.csv") |>
  mutate(
    year = substr(yearspan,start=1,stop=4),
    county_fips = paste(statecode,countycode,sep=""),
    year_county = paste(year,county_fips,sep="-")
  ) |>
  filter(
    measurename == "Premature death" |
    measurename == "Uninsured adults" |
    measurename == "Primary care physicians" |
    measurename == "Flu vaccinations"
  ) |>
  select(year_county,year,county_fips,county,state,measurename,rawvalue) |>
  pivot_wider(
    names_from = "measurename",
    values_from = "rawvalue"
  ) |>
  janitor::clean_names() |>
  mutate(
    premature_death = scale(premature_death),
    uninsured_adults = scale(uninsured_adults),
    primary_care_physicians = scale(primary_care_physicians),
    flu_vaccinations = scale(flu_vaccinations)
  )
```