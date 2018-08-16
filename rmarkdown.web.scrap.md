FishBase Web Scraping with rvest
================
Meg Oshima, Grant Adams, Dr. Robert Leaf
July 21, 2016

Many websites have databases that are not available in an easily accessible form (e.g. .txt, .csv etc.). In our work we were interested in obtaining data that was nested within several layers of web pages and required multiple steps of web scraping.

[FishBase](http://www.fishbase.org/search.php) is a global online database of species data for finfish. From FishBase we collected growth data (*L-infinity*, *k*, and *t*<sub>0</sub>) for hundreds of species. On FishBase, after selecting a species, the first page provides a table of values from every study available.

![](rmarkdown.web.scrap_files/figures-markdown_github/Growth%20tables.PNG)

From there, clicking on the parameter value *k* leads to a page of more detail for that particular study and provides the data reference ID number that accesses the page with the actual citation for the data.

![](rmarkdown.web.scrap_files/figures-markdown_github/detail%20table.PNG)

![](rmarkdown.web.scrap_files/figures-markdown_github/citation%20table.PNG)

The full citation was "scraped"" from fishbase.org using the 'rvest' R package. We started with a .csv file containing a vector of the URL addresses (I have attached an example spreadsheet).

``` r
library(rvest)
library(dplyr)
library(scales)
library(stringr)
fish.df <- read.csv(file = "r.bloggers.fb.example.csv", header = T, sep = ",")
head(fish.df)
```

    ##                    CommonName      Family     Genus   Species Linf..cm.
    ## 1 Emperor, Pacific yellowtail Lethrinidae Lethrinus atkinsoni      41.3
    ## 2 Emperor, Pacific yellowtail Lethrinidae Lethrinus atkinsoni      41.4
    ## 3 Emperor, Pacific yellowtail Lethrinidae Lethrinus atkinsoni      42.8
    ## 4           Emperor, spangled Lethrinidae Lethrinus nebulosus      50.2
    ## 5           Emperor, spangled Lethrinidae Lethrinus nebulosus      61.4
    ##   Length.Type    K t0..years.
    ## 1          SL 0.31         NA
    ## 2          SL 0.31         NA
    ## 3          SL 0.29         NA
    ## 4          SL 0.21         NA
    ## 5          FL 0.34         NA
    ##                                                                                                                                                                                                                                                         URL
    ## 1 http://www.fishbase.us/PopDyn/FishPopGrowthSummary.php?ID=2050&pref=2291&sex=unsexed&loo=41.30000&k=0.31000&id2=1854&genusname=Lethrinus&speciesname=atkinsoni&fc=328&gm_loo=39.331044377911&gm_lm=1&gm_m=1&gm_k=0.2998332870113&vautoctr=3307&gm_lm_rl=1
    ## 2 http://www.fishbase.us/PopDyn/FishPopGrowthSummary.php?ID=2050&pref=2291&sex=unsexed&loo=41.40000&k=0.31000&id2=1854&genusname=Lethrinus&speciesname=atkinsoni&fc=328&gm_loo=39.331044377911&gm_lm=1&gm_m=1&gm_k=0.2998332870113&vautoctr=3308&gm_lm_rl=1
    ## 3 http://www.fishbase.us/PopDyn/FishPopGrowthSummary.php?ID=2050&pref=2291&sex=unsexed&loo=42.80000&k=0.29000&id2=1854&genusname=Lethrinus&speciesname=atkinsoni&fc=328&gm_loo=39.331044377911&gm_lm=1&gm_m=1&gm_k=0.2998332870113&vautoctr=3309&gm_lm_rl=1
    ## 4 http://www.fishbase.us/PopDyn/FishPopGrowthSummary.php?ID=2042&pref=2291&sex=unsexed&loo=50.20000&k=0.21000&id2=1854&genusname=Lethrinus&speciesname=nebulosus&fc=328&gm_loo=39.331044377911&gm_lm=1&gm_m=1&gm_k=0.2998332870113&vautoctr=3278&gm_lm_rl=1
    ## 5 http://www.fishbase.us/PopDyn/FishPopGrowthSummary.php?ID=2042&pref=3679&sex=unsexed&loo=61.40000&k=0.34000&id2=1854&genusname=Lethrinus&speciesname=nebulosus&fc=328&gm_loo=39.331044377911&gm_lm=1&gm_m=1&gm_k=0.2998332870113&vautoctr=3289&gm_lm_rl=1

Then, an object called "web.id" for each entry was created that opened the html code. The reference ID was extracted and put into a new column in our data frame.

``` r
fish.df$Data_ref <- NA
for (i in 1:nrow(fish.df)){
  #created a web ID with the URL from fish.df$URL
  web.id <- as.character(fish.df[i,"URL"]) 
  #opened the html code for the webpage 
  web.html <- read_html(web.id) 
  #pulled information from the table on the webpage
  species.id <- as.character(html_attr(html_nodes(web.html, "a"), "href"))
  #pulled out line with data reference number
  species.id <- species.id[grep("References", species.id)[1]] 
  #split after ID= and called the number (2nd object)
  species.id <- strsplit(species.id, "ID=")[[1]][2]
  #inserted data reference number into the new column in fish.df
  fish.df$Data_ref[i] <- species.id 
  print(i)
}
```

Next, a *for()* loop was used to create a unique web address for each entry based on the data reference number. The information stored in the table was retrieved and only the citation was isolated and added into a new column of the data frame. Some of the web pages had a ".org" address and the others had a ".com" address, therefore, two loops were created, if the webpage wasn't found with a ".com" address, it was redone with a ".org" address.

``` r
fish.df$Citation<-NA

for (i in 1:nrow(fish.df)){
  # http://www.fishbase.org/References/FBRefSummary.php?ID=105366 
  #created the URL that leads to the page with the citation 
  #(general address + data_ref number)
  citation.web.id <- paste("http://www.fishbase.us/references/FBRefSummary.php?ID=",fish.df$Data_ref[i], sep ="") 
  #read information from the table on the webpage
  web.html <- read_html(citation.web.id) 
  # lets you skip errors #get text from the table, including the full citation
  citation.table <- tryCatch(web.html %>% html_nodes("table") %>% .[[1]] %>% html_table(fill=T), error = function(e)"Redo_with_.org_fishbase_site") 
  #isolates the citation
  citation <- tryCatch(citation.table[1,2], error = function(e)"Redo_with_.org_fishbase_site")
  #adds into the new column in fish.df
  fish.df$Citation[i]<-citation 
  print(i)
}
```

``` r
for (i in which( fish.df$Citation %in% "Redo_with_.org_fishbase_site")){
  citation.web.id <- paste("http://www.fishbase.org/references/FBRefSummary.php?ID=",fish.df$Data_ref[i], sep ="")
  web.html <- read_html(citation.web.id)
  citation.table <- web.html %>% html_nodes("table") %>% .[[1]] %>% html_table(fill=T)
  citation <- citation.table[1,2]
  fish.df$Citation[i]<-citation
}
```

Lastly, to isolate the year that the study was published, all non-number characters were removed and a strsplit isolated the second number (the year). That value was added to a new column in the data frame.

``` r
fish.df$Year<-NA

for (i in 1:nrow(fish.df)){
  
fish.df$Year[i] <- strsplit(unlist(fish.df$Citation[i]), "[^0-9]+")[[1]][2]
#removed all non-number characters and split the numbers, 
#then called the 2nd number in the list which was the year 
  
 }
```

With the rvest package and this code, we quickly retrieved text that normally takes several steps to access and added it into our dataframe. Author contact information: <megumi.oshima@usm.edu>, <robert.leaf@usm.edu>, <grant.adams@eagles.usm.edu>
