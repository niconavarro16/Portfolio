# CYCLISTIC🚲
## What is Cyclistic?
A bike-share company that features more than 5,800 bicycles and 600 docking stations. 

Cyclistic sets itself apart by also offering reclining bikes, hand tricycles, and cargo bikes, making bike-share more inclusive to people with disabilities and riders who can’t use a standard two-wheeled bike. The majority of riders opt for traditional bikes; about 8% of riders use the assistive options. Cyclistic users are more likely to ride for leisure, but about 30% use the bikes to commute to work each day.

## 3 questions to solve
- How do annual members and casual riders use Cyclistic bikes differently? 
- Why would casual riders buy Cyclistic annual memberships? 
- How can Cyclistic use digital media to influence casual riders to become members?

## Process
* First, I downloaded the data from the last quarter (June, July, August, and September) in excel and proceeded to bind the four months together to be able to clean and analyze the data together.

  It is important to mention that I decided to work with R studio for completing this project.

```

##DOWNLOAD DATA
august2024_bike <- read.csv('202408-divvy-tripdata.csv')
july2024_bike <- read.csv('202407-divvy-tripdata.csv')
june2024_bike <- read.csv('202406-divvy-tripdata.csv')
may2024_bike <- read.csv('202405-divvy-tripdata.csv')

##DATA CLEANING
#I am binding the datasets because they have the same structure
four_months_data <- bind_rows(august2024_bike, july2024_bike, june2024_bike, may2024_bike)
```

* After this, I continued by cleaning🧹 the duplicates and changing some columns to have them more organized.
```
#removing duplicate rows
four_months_data <- unique(four_months_data)

#separate dates from time
  #Convert columns to POSIXct date-time format if they are not already
four_months_data$started_at <- as.POSIXct(four_months_data$started_at, format = "%Y-%m-%d %H:%M:%OS", tz = "UTC")
four_months_data$ended_at <- as.POSIXct(four_months_data$ended_at, format = "%Y-%m-%d %H:%M:%OS", tz = "UTC")
  #Extract date and time separately
four_months_data$started_date <- format(four_months_data$started_at, "%Y-%m-%d")
four_months_data$started_time <- format(four_months_data$started_at, "%H:%M:%S")
four_months_data$ended_date <- format(four_months_data$ended_at, "%Y-%m-%d")
four_months_data$ended_time <- format(four_months_data$ended_at, "%H:%M:%S")
```

* Calculating the ride lenghts in minutes, and adding the new column **ride_length**
```
  #calculate ride length in minutes
    #Apply difftime 
four_months_data$ride_length <- sapply(1:nrow(four_months_data), function(i) {
  tryCatch({
    round(difftime(four_months_data$ended_at[i], four_months_data$started_at[i], units = "mins"), 2)
  }, error = function(e) {
    NA  
  })
})


four_months_data$ride_length <- mapply(function(start, end) {
  tryCatch({
    round(difftime(end, start, units = "mins"), 2)
  }, error = function(e) {
    NA  # Return NA for problematic rows
  })
}, four_months_data$started_at, four_months_data$ended_at)
```
