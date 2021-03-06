Data Collection & Cleaning
--------------------------

    # ...   -=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-
    # ...   gdp data in & clean
    # ...   -=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-

    # ...   pull in World Bank GDP data set
    url <- "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FGDP.csv"
    download.file(url, destfile = "./GDP.csv")
    gdp <- read.csv("GDP.csv", sep = ",",
                    header = FALSE,
                    skip = 5,
                    stringsAsFactors = FALSE)

    # ...   assign names to data columns
    gdp_names <- c("country_code", "rank", "aaa", "country_name", "economy_dollars")
    names(gdp) <- gdp_names

    # ...   assign rank as integers, eliminate data rows with invalid rank values
    gdp$rank <- as.integer(gdp$rank)

    ## Warning: NAs introduits lors de la conversion automatique

    gdp <- gdp[!is.na(gdp$rank),]

    # ...   remove , from date income data field and convert to numeric
    gdp$economy_dollars <- gsub(",", "", gdp$economy_dollars)
    gdp$economy_dollars <- as.numeric(gdp$economy_dollars)

    # ...   eliminate 3rd column and columns 6 --> width, as they are not needed
    gdp[3] <- NULL
    width <- dim(gdp)[2]
    gdp[5:width] <- NULL

    # ...   -=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-
    # ...   educational data in & clean
    # ...   -=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-


    # ...   pull in World Bank Educational data set
    url <- "https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FEDSTATS_Country.csv"
    download.file(url, destfile = "./EDSTATS_Country.csv")
    edu <- read.csv("EDSTATS_Country.csv", sep = ",",
                    header = TRUE,
                    skip = 0,
                    stringsAsFactors = FALSE)

    # ...   eliminate columns 5 --> width, as they are not needed
    width <- dim(edu)[2]
    edu[5:width] <- NULL

    # ...    merge GDP & Educational data into 1 data frame, matched by country code
    gdp_edu <- merge(gdp, edu, by.x = "country_code", by.y = "CountryCode")

    # ...   rename income group variaables, so that they display in
    # ...   alphabetic order related to income levels

    gdp_edu[gdp_edu$Income.Group == "High income: OECD",   ]$Income.Group <- "1_High_OECD" 
    gdp_edu[gdp_edu$Income.Group == "High income: nonOECD",]$Income.Group <- "2_High_nonOECD" 
    gdp_edu[gdp_edu$Income.Group == "Upper middle income", ]$Income.Group <- "3_Upper_Middle" 
    gdp_edu[gdp_edu$Income.Group == "Lower middle income", ]$Income.Group <- "4_Lower_Middle" 
    gdp_edu[gdp_edu$Income.Group == "Low income",          ]$Income.Group <- "5_Low_Income" 

### Data is now gathered, cleaned, merged into one data frame

    # ...   save merged data frame into .Rda file for future ease of use

    save(gdp_edu, file = "../gdp_edu.Rda")

#### Data set is now saved in file : ./data/gdp\_edu.Rda

#### Summary statistics of saved data set :

    str(gdp_edu)

    ## 'data.frame':    189 obs. of  7 variables:
    ##  $ country_code   : chr  "ABW" "AFG" "AGO" "ALB" ...
    ##  $ rank           : int  161 105 60 125 32 26 133 172 12 27 ...
    ##  $ country_name   : chr  "Aruba" "Afghanistan" "Angola" "Albania" ...
    ##  $ economy_dollars: num  2584 20497 114147 12648 348595 ...
    ##  $ Long.Name      : chr  "Aruba" "Islamic State of Afghanistan" "People's Republic of Angola" "Republic of Albania" ...
    ##  $ Income.Group   : chr  "2_High_nonOECD" "5_Low_Income" "4_Lower_Middle" "3_Upper_Middle" ...
    ##  $ Region         : chr  "Latin America & Caribbean" "South Asia" "Sub-Saharan Africa" "Europe & Central Asia" ...

    summary(gdp_edu)

    ##  country_code            rank        country_name       economy_dollars   
    ##  Length:189         Min.   :  1.00   Length:189         Min.   :      40  
    ##  Class :character   1st Qu.: 48.00   Class :character   1st Qu.:    6972  
    ##  Mode  :character   Median : 95.00   Mode  :character   Median :   28242  
    ##                     Mean   : 95.31                      Mean   :  379597  
    ##                     3rd Qu.:143.00                      3rd Qu.:  205789  
    ##                     Max.   :190.00                      Max.   :16244600  
    ##   Long.Name         Income.Group          Region         
    ##  Length:189         Length:189         Length:189        
    ##  Class :character   Class :character   Class :character  
    ##  Mode  :character   Mode  :character   Mode  :character  
    ##                                                          
    ##                                                          
    ## 

#### These are the data names in the data set :

    names(gdp_edu)

    ## [1] "country_code"    "rank"            "country_name"    "economy_dollars"
    ## [5] "Long.Name"       "Income.Group"    "Region"

#### Data set size : 189, 7

#### Data sets displayed in boxplot \[log(income)\] for the population :

    boxplot(log10(gdp_edu$economy_dollars) ~ gdp_edu$Region)

![](read_clean_data_files/figure-markdown_strict/unnamed-chunk-4-1.png)

    # ...   -=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-
    # ...   this completes the data gather & clean operations
    # ...   -=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-
