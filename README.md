# irs-current-exempt-orgs-database

R scripts to document and build the IRS Current Exempt Organizations Database.


--------------------


# CURRENT EXEMPT ORGANIZATIONS LIST

From the IRS Website: https://apps.irs.gov/app/eos/forwardToPub78Download.do

Publication 78 Data Download

Using the link below, you may download as a zipped text file the most recent list of organizations eligible to receive tax-deductible charitable contributions (Pub. 78 data). The download file is a large compressed file, from which you may extract a very large delimited text file. This file is updated on a monthly basis. Opening the file using word processing software may prevent formatting/appearance issues that may be present if the file is viewed with a text editing program. Information is available describing the layout and contents of the downloadable file.

Format: Pipe-Delimited ASCII Text


## Data Dictionary

Variable                | Notes
---------               |------------------------------------------------------------- 
EIN         |  Unique Employee Identification Number for each nonprofit 
Legal Name  |  Name of nonprofit (optional) 
City        |  Location of nonprofit (optional) 
State       |  Location of nonprofit (optional) 
Country      |  If the country is null, it is assumed to be the United States 
Deductibility Status  | PC, PF, EO, ...  (optional) 

|    EIN|Legal Name                                        |City      |ST |Country       |DS |
|------:|:-------------------------------------------------|:---------|:--|:-------------|:--|
|   4101|South Lafourche Quarterback Club                  |Lockport  |LA |United States |PF |
| 587764|Iglesia Bethesda Inc.                             |Lowell    |MA |United States |PC |
| 635913|Ministerio Apostolico Jesucristo Es El Senor Inc. |Lawrence  |MA |United States |PC |
| 765634|Mercy Chapel International                        |Mattapan  |MA |United States |PC |
| 841363|Agape House of Prayer                             |Mattapan  |MA |United States |PC |
| 852649|Bethany Presbyterian Church                       |Brookline |MA |United States |PC |



*Note that this dataset is current, not cumulative, meaning it lists only entities with current 501c3 status*.



Data from May 2023: 1,045,476 organizations listed in the Pub78 download. 

|Deductibility Status |   Freq|
|:--------------------|------:|
|PC                   | 907371|
|PF                   |  98345|
|SOUNK                |  11592|
|EO                   |  11339|
|POF                  |   6979|
|SO                   |   4937|
|GROUP                |   1863|
|EO,LODGE             |   1417|
|FORGN                |    581|
|UNKWN                |    370|
|EO,GROUP,LODGE       |    268|
|SONFI                |    194|
|EO,GROUP             |     85|
|FORGN,PF             |     51|
|GROUP,SOUNK          |     44|
|FORGN,SOUNK          |     16|
|GROUP,SO             |      7|
|EO,FORGN             |      5|
|EO,PF                |      4|
|FORGN,POF            |      4|
|EO,FORGN,GROUP,LODGE |      1|
|EO,SO                |      1|
|FORGN,SO             |      1|
|GROUP,PF             |      1|

*The IRS provides no documentation on the definition of the different deductibility status codes.*

|Country                |    Freq|
|:----------------------|-------:|
|United States          | 1044817|
|CANADA                 |     310|
|AFGHANISTAN            |      51|
|BRITISH VIRGIN ISLANDS |      33|
|AUSTRALIA              |      27|
|UNITED KINGDOM         |      24|
|ISRAEL                 |      15|
|GEORGIA                |      12|
|JAPAN                  |      10|
|KENYA                  |       9|
|PUERTO RICO            |       9|
|FRANCE                 |       8|
|MAURITIUS              |       8|
|MEXICO                 |       7|
|UGANDA                 |       6|

*Truncated for preview purposes.*

## BUILD THE DATABASE


```R 


# create subdirectory for the data

getwd()

dir.create( "IRS Nonprofit Data" )

setwd( "./IRS Nonprofit Data" )



# download and unzip

file.url <- "https://apps.irs.gov/pub/epostcard/data-download-pub78.zip"

download.file( url=file.url, "exemptorgs.zip" )

unzip( "exemptorgs.zip" )

file.remove( "exemptorgs.zip" )

dat.exempt <- read.delim( file="data-download-pub78.txt", 
            header = FALSE, 
            sep = "|", 
            quote = "",
            dec = ".", 
            fill = TRUE,  
            colClasses="character"
          )



# add header information - variable names

var.names <- c("EIN", "Legal.Name",  "City", "State", 
               "Country", "Deductibility.Status")


names( dat.exempt ) <- var.names

rm( var.names )

```



## EXPORT THE DATASET


```R

# AS R DATA SET

saveRDS( dat.exempt, file="ExemptOrganizations.rds" )


# AS CSV

write.csv( dat.exempt, "ExemptOrganizations.csv", row.names=F )


# IN STATA

install.packages( "haven" )
library( haven )
write_dta( dat.exempt, "ExemptOrganizations.dta" )

# IN SPSS  - creates a text file and a script for reading it into SPSS

library( foreign )
write.foreign( df=dat.exempt, datafile="ExemptOrganizations.txt", codefile="CodeToLoadDataInSPSS.txt", package="SPSS" )

# if package 'foreign' is not installed first try:  install.packages("foreign")

```

