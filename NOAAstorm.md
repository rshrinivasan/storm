Title
========================================================

# Synopsis
This is an R Markdown document. Markdown is a simple formatting syntax for authoring web pages (click the **Help** toolbar button for more details on using R Markdown).

# Data Processing
When you click the **Knit HTML** button a web page will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:


```r
# data file download and processing
# set download url
furl <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2"

# set filename
fname <- "NOAAstorm.csv.bz2"

# download file
download.file(furl, fname, method = "curl" )

# unzip file and load into data frame
stormdata <- read.csv(bzfile("NOAAstorm.csv.bz2"))
```


```r
str(stormdata)
```

```
## 'data.frame':	902297 obs. of  37 variables:
##  $ STATE__   : num  1 1 1 1 1 1 1 1 1 1 ...
##  $ BGN_DATE  : Factor w/ 16335 levels "1/1/1966 0:00:00",..: 6523 6523 4242 11116 2224 2224 2260 383 3980 3980 ...
##  $ BGN_TIME  : Factor w/ 3608 levels "00:00:00 AM",..: 272 287 2705 1683 2584 3186 242 1683 3186 3186 ...
##  $ TIME_ZONE : Factor w/ 22 levels "ADT","AKS","AST",..: 7 7 7 7 7 7 7 7 7 7 ...
##  $ COUNTY    : num  97 3 57 89 43 77 9 123 125 57 ...
##  $ COUNTYNAME: Factor w/ 29601 levels "","5NM E OF MACKINAC BRIDGE TO PRESQUE ISLE LT MI",..: 13513 1873 4598 10592 4372 10094 1973 23873 24418 4598 ...
##  $ STATE     : Factor w/ 72 levels "AK","AL","AM",..: 2 2 2 2 2 2 2 2 2 2 ...
##  $ EVTYPE    : Factor w/ 985 levels "   HIGH SURF ADVISORY",..: 834 834 834 834 834 834 834 834 834 834 ...
##  $ BGN_RANGE : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ BGN_AZI   : Factor w/ 35 levels "","  N"," NW",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ BGN_LOCATI: Factor w/ 54429 levels ""," Christiansburg",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ END_DATE  : Factor w/ 6663 levels "","1/1/1993 0:00:00",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ END_TIME  : Factor w/ 3647 levels ""," 0900CST",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ COUNTY_END: num  0 0 0 0 0 0 0 0 0 0 ...
##  $ COUNTYENDN: logi  NA NA NA NA NA NA ...
##  $ END_RANGE : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ END_AZI   : Factor w/ 24 levels "","E","ENE","ESE",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ END_LOCATI: Factor w/ 34506 levels ""," CANTON"," TULIA",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ LENGTH    : num  14 2 0.1 0 0 1.5 1.5 0 3.3 2.3 ...
##  $ WIDTH     : num  100 150 123 100 150 177 33 33 100 100 ...
##  $ F         : int  3 2 2 2 2 2 2 1 3 3 ...
##  $ MAG       : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ FATALITIES: num  0 0 0 0 0 0 0 0 1 0 ...
##  $ INJURIES  : num  15 0 2 2 2 6 1 0 14 0 ...
##  $ PROPDMG   : num  25 2.5 25 2.5 2.5 2.5 2.5 2.5 25 25 ...
##  $ PROPDMGEXP: Factor w/ 19 levels "","-","?","+",..: 17 17 17 17 17 17 17 17 17 17 ...
##  $ CROPDMG   : num  0 0 0 0 0 0 0 0 0 0 ...
##  $ CROPDMGEXP: Factor w/ 9 levels "","?","0","2",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ WFO       : Factor w/ 542 levels ""," CI","%SD",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ STATEOFFIC: Factor w/ 250 levels "","ALABAMA, Central",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ ZONENAMES : Factor w/ 25112 levels "","                                                                                                                               "| __truncated__,..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ LATITUDE  : num  3040 3042 3340 3458 3412 ...
##  $ LONGITUDE : num  8812 8755 8742 8626 8642 ...
##  $ LATITUDE_E: num  3051 0 0 0 0 ...
##  $ LONGITUDE_: num  8806 0 0 0 0 ...
##  $ REMARKS   : Factor w/ 436781 levels "","\t","\t\t",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ REFNUM    : num  1 2 3 4 5 6 7 8 9 10 ...
```

```r
library(sqldf)
```

```
## Loading required package: gsubfn
## Loading required package: proto
```

```
## Warning: unable to load shared object '/Library/Frameworks/R.framework/Resources/modules//R_X11.so':
##   dlopen(/Library/Frameworks/R.framework/Resources/modules//R_X11.so, 6): Library not loaded: /opt/X11/lib/libSM.6.dylib
##   Referenced from: /Library/Frameworks/R.framework/Resources/modules//R_X11.so
##   Reason: image not found
```

```
## Could not load tcltk.  Will use slower R code instead.
## Loading required package: RSQLite
## Loading required package: DBI
## Loading required package: RSQLite.extfuns
```

```r
# subset data to pick out variables of interest for analysis
# TRANSFORM  PROPDMGEXP & CROPDMG variables from Char to Numeric
stormstaging <- sqldf("select EVTYPE, FATALITIES, INJURIES, PROPDMG, 
                     CASE PROPDMGEXP WHEN 'H' THEN 100
                                     WHEN 'K' THEN 1000
                                     WHEN 'M' THEN 1000000
                                     WHEN 'B' THEN 1000000000
                                     ELSE 1
                                     END PROPDMGMULTIPLIER,
                     CROPDMG,
                     CASE CROPDMGEXP WHEN 'H' THEN 100
                                     WHEN 'K' THEN 1000
                                     WHEN 'M' THEN 1000000
                                     WHEN 'B' THEN 1000000000
                                     ELSE 1
                                     END CROPDMGMULTIPLIER from stormdata")
names(stormstaging)
```

```
## [1] "EVTYPE"            "FATALITIES"        "INJURIES"         
## [4] "PROPDMG"           "PROPDMGMULTIPLIER" "CROPDMG"          
## [7] "CROPDMGMULTIPLIER"
```

```r
# create transformed data frame
stormaggregate <- sqldf("select EVTYPE, FATALITIES, INJURIES,
                           FATALITIES + INJURIES HUMANDMG,
                           PROPDMG * PROPDMGMULTIPLIER TOTPROPDMG,
                           CROPDMG* CROPDMGMULTIPLIER TOTCROPDMG,
                           (PROPDMG * PROPDMGMULTIPLIER + 
                            CROPDMG* CROPDMGMULTIPLIER) ECODMG
                   from stormstaging")
names(stormaggregate)
```

```
## [1] "EVTYPE"     "FATALITIES" "INJURIES"   "HUMANDMG"   "TOTPROPDMG"
## [6] "TOTCROPDMG" "ECODMG"
```
# Results

```r
# Total Economic Damage grouped by event type
ecodmg <- sqldf("select EVTYPE, SUM(TOTPROPDMG) TOTECODMG
                 from stormaggregate
                 group by EVTYPE")
# Print Top 10 Events causing maximum economic damage
sqldf("select EVTYPE Event, TOTECODMG EconomicDamage$
       from ecodmg
       order by TOTECODMG desc limit 10")
```

```
##                Event EconomicDamage$
## 1              FLOOD       1.447e+11
## 2  HURRICANE/TYPHOON       6.931e+10
## 3            TORNADO       5.693e+10
## 4        STORM SURGE       4.332e+10
## 5        FLASH FLOOD       1.614e+10
## 6               HAIL       1.573e+10
## 7          HURRICANE       1.187e+10
## 8     TROPICAL STORM       7.704e+09
## 9       WINTER STORM       6.688e+09
## 10         HIGH WIND       5.270e+09
```

```r
# Total Human Damage grouped by event type
humandmg <- sqldf("select EVTYPE, SUM(HUMANDMG) TOTHUMDMG
                   from stormaggregate
                   group by EVTYPE")

# Top 10 Events causing maximum human damage
sqldf(" select EVTYPE Event, TOTHUMDMG Humans
        from humandmg
        order by TOTHUMDMG desc limit 10")
```

```
##                Event Humans
## 1            TORNADO  96979
## 2     EXCESSIVE HEAT   8428
## 3          TSTM WIND   7461
## 4              FLOOD   7259
## 5          LIGHTNING   6046
## 6               HEAT   3037
## 7        FLASH FLOOD   2755
## 8          ICE STORM   2064
## 9  THUNDERSTORM WIND   1621
## 10      WINTER STORM   1527
```


