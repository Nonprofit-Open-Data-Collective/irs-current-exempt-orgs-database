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


*Note that this dataset is current, not cumulative, meaning it lists only entities with current 501c3 status*.



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

