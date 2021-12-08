

<h2> Data Analyst Task |  Jessica Fenker </h2>

<a href="https://jessicafenker.com/">jessicafenker.com</a> | <a href="https://scholar.google.com/citations?user=x3R-PWkAAAAJ&hl=en&oi=ao">google scholar profile</a>

This is an source document requested by the Atlas of Living Australia (ALA) as a step prior to the interview for the role of Data Analyst. A simplied R script can be found (here).

------------------------------------------------------------------------

## **General Rules**

Download a dataset following a set of requirements, and produce a visualisation based on a subset of the data that you find informative. You should also provide code showing the steps taken to download, explore and visualise the data.  

Using a method of your choice, download all observations of reptiles (class "Reptilia") recorded in the ACT. Your download should include, but not be limited to, the following fields:

- decimalLatitude  
- decimalLongitude  
- scientificName  
- dataResourceName  
- basisOfRecord  
- State or Territory (field id `cl22`)  
- Forests of Australia 2013 (field id `cl10902`)  



## **Methodology to obtain the dataset**

I decided to use the `galah` R package to download the dataset directly to my computer. `galah` is an R interface to the data hosted by the Atlas of Living Australia (ALA), that allows you to effectively investigate the available dataset, apply filters and quality profiles and download the data, including species records and media data.

For that, I first installed and loaded the package (from CRAN). I then asked for help, using the help? funciton to read more about the package. I also accessed the [galah vignette](https://atlasoflivingaustralia.github.io/galah/articles/galah.html) for extra information and guideline.

```r
install.packages("galah")
library(galah)
?galah
```


Following the guidelines, I specified that I wanted data from Australia, and included my e-mail (already registered at ALA) to be able to download the data.

```r
galah_config(atlas = "Australia",
             email = "jehfenker@gmail.com")
```




## **Understanding the data structure**

Prior to obtaining the necessary dataset, I investigated some of the 'galah' functions.  

First, I searched for the species/group of interest and the count of number of registers:

```r
reptiles <- select_taxa("Reptilia")
reptiles
ala_counts(taxa = reptiles)  # counts the number of registers: 1312041
```

Then, I explored the categories within the ALA fields of interest:

```r
find_field_values("basisOfRecord")
find_field_values("scientificName")
find_field_values("stateProvince")
find_field_values("cl22")
find_field_values("cl10902")
```

For curiosity, I also obtained a list of reptile species in the ACT:

```{r galah}
ACT_reptile_sp <- ala_species(taxa = reptiles,
                       filters = select_filters(stateProvince = "Australian Capital Territory"))
ACT_reptile_sp  # 72 species
ala_counts(taxa = reptiles,
           filters = select_filters
           (stateProvince = "Australian Capital Territory"))  # 10349 registers
```


Then I explored the search_fields function and tested the filtering selection:

```r
search_fields("species")

# Testing the select_filters functions
# Choosing preserved specimens for the ACT from 2010 to today
museum_sp_ACT <- select_filters(
  stateProvince = "Australian Capital Territory",
  basisOfRecord = "PRESERVED_SPECIMEN",
  year >= 2010)
```


Then, I requested occurences of reptiles, applied previous filter options and explored the select_columns() function:

```r
reptiles_museum_ACT <- ala_occurrences(taxa    = reptiles,
                                       filters = museum_sp_ACT )

# Exploring the select columns function
ALA_basic_columns <- select_columns( group = "basic")

# Including extra columns to the basic
task_columns <- select_columns( c("basisOfRecord","cl22","cl10902"), 
                                group = "basic")
```

Finally, I explored the data quality profiles available in the package:

```r
profiles <- find_profiles()
profiles
```




## **Obtaining the dataset**


I searched for "Reptilia", which includes all reptile species available at ALA. As requested in the task, I filetered the dataset for records only in the ACT and specifying the set of columns that I was interested. I asked to include a Digital Object Identifier (DOI) number, that I could refer if the data was going to be published.

Note that ACT includes data from Canberra and surrounding regions and Jervis Bay:

```r
reptiles_ACT <- ala_occurrences(taxa    = reptiles,
                                filters = select_filters(
                                  stateProvince = "Australian Capital Territory",
                                  profile       = "ALA"),
                                columns   = task_columns,
                                mint_doi  = TRUE)
head(reptiles_ACT)

# Plot the coordinates for a fast visualization of your point data
plot(reptiles_ACT$decimalLongitude,reptiles_ACT$decimalLatitude)

# Generate a DOI
doi <- attr(reptiles_ACT, "doi")
citation <- ala_citation(reptiles_ACT)
doi
citation
```


For curiosity, I also explored the reptile images available at ALA from 2021:

```r
# Use the occurrences previously downloaded
media_data <- ala_media(
  taxa         = reptiles,
  filters      = select_filters(stateProvince = "Australian Capital Territory",
                                year = 2021),
  download_dir = getwd())  # 279 images!
```



## **Plotting the results**

I decided to focus on the... 
