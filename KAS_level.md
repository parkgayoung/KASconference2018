KAS\_level
================
Gayoung Park
10/29/2018

I just wanted to make a plot for elevation of each site, and show is there is a relationship between the time of first occupation and elevation, and whether or not there are stemmed points.

We can see that the sites without stemmed points are earlier, and at high elevations. But then after stemmed points appear, the sites without stemmed points are only found at low elevations, and the stem point sites are at higher elevations. So it might suggest that the stemmed point was an important artefact for adapting to high elevations, especially into the period of the Last Glacial Maximum.

``` r
mydata_ages <- 
  mydata %>% 
  separate(C14.BP., into = c('age', 'error'),
          sep = "±") %>% 
  mutate(age_ka = parse_number(age) / 1000,
         error = parse_number(error)) %>% 
  mutate(has_sp = ifelse(!is.na(SP.), "yes", "no"))

ggplot(mydata_ages, 
       aes(x = age_ka,
           y = altitude.m._of_main_layer,
       color = as.factor(has_sp))) +
  geom_point(size = 3) +
  xlab("Age of occupation (ka)") +
  ylab("Elevation above sea level (m)") +
  geom_smooth(se = FALSE) +
  scale_colour_discrete(name = "Contains\nstemmed\npoints?") +
  theme_minimal(base_size = 16) 
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](KAS_level_files/figure-markdown_github/unnamed-chunk-1-1.png)

Using raw material information sheeet from KAS data sheets.

``` r
kasr_long <- 
  kasr %>% 
  gather(site, 
         count, 
         -X) %>% 
  group_by(site) %>% 
  mutate(percentage = count / sum(count, na.rm = TRUE) * 100,
         total = sum(count, na.rm = TRUE)) %>% 
  filter(!is.na(percentage)) %>% 
  # if percentage is <10%, call it 'other'
  mutate(raw_material = ifelse(percentage >= 10, as.character(X), "other")) %>% 
  mutate(axis_label = glue('{site}\n(n = {total})')) %>% 
  # join to get ages of the sites
  left_join(mydata_ages, 
            by = c('site' = 'site_name')) %>% 
  arrange(age_ka)
```

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning: Column `site`/`site_name` joining character vector and factor,
    ## coercing into character vector

``` r
kasr_long_plot <- 
ggplot(kasr_long, 
       aes(age_ka,
           percentage,
           fill = raw_material)) +
  geom_col() +
  xlab("Age of assemblage (ka)") +
  ylab("Percentage") +
  scale_fill_viridis_d(name = "Raw material type",
                       option = "C") +
  coord_cartesian(xlim = c(25, 45)) +
  theme_minimal(base_size = 16) 



kasr_long_plot
```

    ## Warning: Removed 2 rows containing missing values (position_stack).

![](KAS_level_files/figure-markdown_github/unnamed-chunk-2-1.png)

How does it look in context of global Oxygen isotope levels?? Not very interesting.

``` r
library(gsloid)

lisiecki2005_plot <- 
ggplot(lisiecki2005, 
       aes(Time,
           d18O)) +
  geom_line() +
  scale_x_continuous(limits = c(kasr_long_plot$coordinates$limits$x[1], 
                                kasr_long_plot$coordinates$limits$x[2]),
                     name = "x 1000 years ago") +
  scale_y_reverse(name = bquote(delta^18*O)) +
  theme_bw()

library(patchwork)
kasr_long_plot + 
  lisiecki2005_plot + 
  plot_layout(ncol = 1,
              heights = c(3, 1))
```

    ## Warning: Removed 2 rows containing missing values (position_stack).

    ## Warning: Removed 2094 rows containing missing values (geom_path).

![](KAS_level_files/figure-markdown_github/unnamed-chunk-3-1.png)

The following plot is representing the artifact composition of each sites to figure out what people did as they were occupying the sites.

``` r
kasa_long <-
  kasa %>%
  gather(site, 
         count, 
         -X) %>% 
  filter(!is.na(count)) %>% 
  group_by(site) %>% 
  mutate(percentage = count / sum(count, na.rm = TRUE) * 100,
         total = sum(count, na.rm = TRUE)) %>% 
  filter(!is.na(percentage)) %>% 
  # if percentage is <10%, call it 'other'
  mutate(artefact_type = ifelse(as.character(X) == 'stemmed_point',
                                'stemmed_point', 
                                ifelse(percentage >= 10, 
                                as.character(X), "other_tools"))) %>% 
  mutate(axis_label = glue('{site}\n(n = {total})'))  %>% 
  # join to get ages of the sites
  left_join(mydata_ages, 
            by = c('site' = 'site_name')) %>% 
  arrange(age_ka)
```

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning in mutate_impl(.data, dots, caller_env()): Vectorizing 'glue'
    ## elements may not preserve their attributes

    ## Warning: Column `site`/`site_name` joining character vector and factor,
    ## coercing into character vector

``` r
ggplot(kasa_long, 
       aes(age_ka, 
           percentage,
           fill = artefact_type)) +
  geom_col() +
  ylab("Percentage") +
  xlab("Age of assemblage (ka)") +
  labs(fill="Artifact type")+
  theme_minimal(base_size = 16) 
```

![](KAS_level_files/figure-markdown_github/unnamed-chunk-4-1.png)

Volume and artefact counts to get density over time.

``` r
kasv_tidy <- 
kasv %>% 
  t %>% 
  as_tibble() %>% 
  setNames(as.character(.[1,])) %>% 
  .[-1,] %>% 
  mutate_all(parse_number) %>% 
  mutate(artefact_density = total_artifacts / volume,
         sites = names(kasv)[-1])
```

    ## Warning: `as_tibble.matrix()` requires a matrix with column names or a `.name_repair` argument. Using compatibility `.name_repair`.
    ## This warning is displayed once per session.

``` r
ggplot(kasv_tidy,
       aes(date_age,
           artefact_density)) +
  geom_point(aes(size = total_artifacts)) +
  geom_text_repel(aes(label = sites),
                  nudge_x = 0.25, 
                  nudge_y = 0.25) +
  ylab("Arefact density") +
  xlab("Age of assemblage (ka)") +
  scale_size_continuous(name = "Total number\nof artefacts")  +
  theme_minimal(base_size = 16)
```

![](KAS_level_files/figure-markdown_github/unnamed-chunk-5-1.png)

Get the lithic data for some sites:

``` r
# we'll use googlesheets package to get the data more efficiently
library(googlesheets)

# here is the key to the google sheet
kas_sites <- gs_key("14G0Mp-YSsKBOBPF1Z1zPWQbZQ-sjJSWLetDIQSlbsjw")
```

    ## Worksheets feed constructed with public visibility

``` r
# get the tab names from this sheet
kas_sites_names <- gs_ws_ls(kas_sites)

# sheets we need here
sheets <- c( "general_info",    "assemblage_info" )

# get the data from the tabs in this sheet!
kas_sites_sheets <- 
  map(sheets, ~kas_sites %>% gs_read_listfeed(ws = .x))
```

    ## Accessing worksheet titled 'general_info'.

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_number(),
    ##   X1 = col_character()
    ## )

    ## See spec(...) for full column specifications.

    ## Accessing worksheet titled 'assemblage_info'.

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_double(),
    ##   X1 = col_character()
    ## )

    ## See spec(...) for full column specifications.

Compute artefact volumetric density by frequency of retouched piece (log scale), following Julien Riel-Salvatore and C. Michael Barton in <https://www.jstor.org/stable/4128419>

``` r
# put the site names on this list of data frames
site_names <-  kas_sites_sheets[[1]] %>%  
  select(-X1) %>% 
  names()

# density of arefacts at each site
artefact_density <- 
kas_sites_sheets[[1]] %>% 
  t %>% 
  as.data.frame() %>% 
 rownames_to_column() %>% 
  mutate_all(as.character) 

names(artefact_density) <- as.character(as.vector(artefact_density[1, ]))
artefact_density <- artefact_density[-1,]

artefact_density <- 
artefact_density %>% 
  mutate(density = as.numeric(total_artifacts) / as.numeric(volume)) %>% 
   mutate_at(2:ncol(.), as.numeric)  

# frequency of retouched artefacts
retouched_artefacts <- 
 kas_sites_sheets[[2]] %>% 
   filter(!X1 %in% c("debris", 
                     "unkown", 
                     "pebble",  
                     "metate", 
                     "flake",
                     "unfinished",
                     "ground",
                     "blank"
                     )) %>% 
    t %>% 
  as.data.frame() %>% 
 rownames_to_column() %>% 
  mutate_all(as.character) 

names(retouched_artefacts) <- as.character(as.vector(retouched_artefacts[1, ]))
retouched_artefacts <- retouched_artefacts[-1,]

retouched_artefacts <- 
retouched_artefacts %>% 
  mutate_at(2:ncol(.), as.numeric)  %>% 
  mutate(total_retouched = rowSums(.[,-1], na.rm = TRUE))


# join together the site age, artefact density df with the retouched artefacts df
kas_density_retouch_ages <- 
artefact_density %>% 
  left_join(retouched_artefacts) %>% 
  mutate(proportion_retouched = total_retouched / total_artifacts) %>% 
  mutate(sites = X1)
```

    ## Joining, by = "X1"

``` r
# compute correlation
kas_sites_retouch_density_corr <- 
cor.test(kas_density_retouch_ages$proportion_retouched,
         kas_density_retouch_ages$density)

r_value <- unname(kas_sites_retouch_density_corr$estimate)
```

``` r
size <-  8


ggplot(kas_density_retouch_ages,
       aes(density,
           proportion_retouched
           )) +
  geom_point(aes(colour = date_age / 1000,
                 size = total_artifacts,
                 shape = is.na(stemmed_point))) +
 # geom_text_repel(aes(label = sites),
 #                 nudge_x = 0.075, 
 #                 nudge_y = -0.075,
 #                 size = 5) +
  geom_smooth(alpha = 0.2) +
  scale_y_log10(limits = c(0.001, 1),
                labels = scales::comma_format(accuracy = 0.001)) +
  scale_x_log10(limits = c(0.001, 10),
                labels = scales::comma_format(accuracy = 0.001)) +
  theme_minimal(base_size = 12) +
  scale_color_viridis_c(name = "Age of\nassemblage (ka)") +
  xlab("Artefact volumetric density") +
  ylab("Proportion of retouched pieces") +
  annotate("text",
           x = 0.005,
           y = 0.7,
           label = "Curated",
           size = size -2,
           colour = "grey50") +
    annotate("text",
           x = 5,
           y = 0.01,
           label = "Expedient",
           size = size -2,
           colour = "grey50") +
    annotate("text",
           x = 0.01,
           y = 0.01,
           label = glue('r = {round(r_value, 3)}'),
           size = size - 2,
           colour = "grey50")
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

![](KAS_level_files/figure-markdown_github/unnamed-chunk-8-1.png)

Artefact volumetric density and frequency of retouched pieces can be proxies to identify the characteristics of the hunter-gatherers who carried that stone tools whether they are residentially mobile or logistically orgnized foragers based on the premise that the relationship between lithic assemblage composition and land-use strategy represent an expression of economizing behavior.

The artefact volumetric density is defined as the total number of artefacts per cubic meter of excavated sediment, and served as a proxy for accumulation ratio of artefacts. the relative frequency of retouched pieces is the count of retouched flake tools devided by the total number of artifacts.

The two ends of the curated-expedient show that residential to logistical mobility. Expedient assemblages can be depicted as "base camps" or "residences", while curated ones represent either residential mobility, where the central locus of group changes frequently, or certain task groups from a central residential site.

The plot show that younger sites are likely to be used as base camp than older site, which means that people tended to stay longer at one place after around 40-35ka.

(Riel-Salvatore and Barton 2004)
