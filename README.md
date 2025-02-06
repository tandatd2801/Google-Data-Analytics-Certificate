# Google-Data-Analytics-Certificate

# Introduction

Cyclistic is a bike-share program with more than 5,800 bicycles and 600 docking stations. The marketing director believes the company’s future success depends on maximizing the number of annual memberships. As a junior data analyst, I want to understand how casual riders and annual members use Cyclistic bikes differently. From these insights, I will design a new marketing strategy to convert casual riders into annual members. I will be using the 6 phases of the data analysis process (Ask, Prepare, Process, Analyze, Share, and Act) to help guide my marketing strategy.

# Phase 1: Ask

Identify the business task: Maximizing the number of annual memberships by converting casual riders into annual members. Three questions:
1. How do annual members and casual riders use Cyclistic bikes differently?
2. Why would casual riders buy Cyclistic annual memberships?
3. How can Cyclistic use digital media to influence casual riders to become members?

# Phase 2: Prepare

Identify the data source: [link](https://divvy-tripdata.s3.amazonaws.com/index.html), I downloaded the data from January 2024 to December 2024.
Determine the credibility of the data: ROCCC system.
- Reliability: This data is reliable. Information is available about the data source.
- Originality: This is an original dataset as it was collected by the first party, Cyclistic bike-share.
- Comprehensiveness: This data is comprehensive. There is information about the riders, such as the duration of the ride, the day and month of the ride, the start and end station of their rides, rider id, etc.
- Current: This data is current. It has been collected in the past 12 months.
- Cited: The data has been made available by Motivate International Inc. under this license.

# Phase 3: Process

Loading the required packages.
```
library("tidyverse")
library("lubridate")
library("dplyr")
library("hms")
library("tidyr")
library("scales")
options(dplyr.width = Inf)
```
Importing the past 12 months’ Cyclistic data.
```
df_1 <- read_csv("202401-divvy-tripdata.csv")
df_2 <- read_csv("202402-divvy-tripdata.csv")
df_3 <- read_csv("202403-divvy-tripdata.csv")
df_4 <- read_csv("202404-divvy-tripdata.csv")
df_5 <- read_csv("202405-divvy-tripdata.csv")
df_6 <- read_csv("202406-divvy-tripdata.csv")
df_7 <- read_csv("202407-divvy-tripdata.csv")
df_8 <- read_csv("202408-divvy-tripdata.csv")
df_9 <- read_csv("202409-divvy-tripdata.csv")
df_10 <- read_csv("202410-divvy-tripdata.csv")
df_11 <- read_csv("202411-divvy-tripdata.csv")
df_12 <- read_csv("202412-divvy-tripdata.csv")
df_1['month'] <- 'Jan'
df_2['month'] <- 'Feb'
df_3['month'] <- 'Mar'
df_4['month'] <- 'Apr'
df_5['month'] <- 'May'
df_6['month'] <- 'Jun'
df_7['month'] <- 'Jul'
df_8['month'] <- 'Aug'
df_9['month'] <- 'Sep'
df_10['month'] <- 'Oct'
df_11['month'] <- 'Nov'
df_12['month'] <- 'Dec'
```

Binding all the past 12 months data into a single data frame to provide a full-year view.
```
trip_data <- rbind(df_1,df_2,df_3,df_4,df_5,df_6,df_7,df_8,df_9,df_10,df_11,df_12) 
nrow(trip_data)
```
The total # of cyclist entries in the data pre-cleaning phase is 5860568.
```
glimpse(trip_data)
```
```
Rows: 5,860,568
Columns: 14
$ ride_id            <chr> "C1D650626C8C899A", "EECD38BDB25BFCB0", "F4A9CE78…
$ rideable_type      <chr> "electric_bike", "electric_bike", "electric_bike"…
$ started_at         <dttm> 2024-01-12 15:30:27, 2024-01-08 15:45:46, 2024-0…
$ ended_at           <dttm> 2024-01-12 15:37:59, 2024-01-08 15:52:59, 2024-0…
$ start_station_name <chr> "Wells St & Elm St", "Wells St & Elm St", "Wells …
$ start_station_id   <chr> "KA1504000135", "KA1504000135", "KA1504000135", "…
$ end_station_name   <chr> "Kingsbury St & Kinzie St", "Kingsbury St & Kinzi…
$ end_station_id     <chr> "KA1503000043", "KA1503000043", "KA1503000043", "…
$ start_lat          <dbl> 41.90327, 41.90294, 41.90295, 41.88430, 41.94880,…
$ start_lng          <dbl> -87.63474, -87.63444, -87.63447, -87.63396, -87.6…
$ end_lat            <dbl> 41.88918, 41.88918, 41.88918, 41.92182, 41.88918,…
$ end_lng            <dbl> -87.63851, -87.63851, -87.63851, -87.64414, -87.6…
$ member_casual      <chr> "member", "member", "member", "member", "member",…
$ month              <chr> "Jan", "Jan", "Jan", "Jan", "Jan", "Jan", "Jan", …
```

Cleaning and transforming the data to prepare for analysis.
Check if any entry exists where the column “start time” of a cyclist entry is greater than or equal to their “end time” column.
```
trip_data %>% filter(started_at >= ended_at)
```
```
A tibble: 723 × 14
   ride_id          rideable_type started_at          ended_at           
   <chr>            <chr>         <dttm>              <dttm>             
 1 D20C5C3F081852A9 electric_bike 2024-01-10 17:34:38 2024-01-10 17:34:38
 2 2EC792ACA079CEAE electric_bike 2024-01-26 11:56:58 2024-01-26 11:56:58
 3 F55B9A59BA0D16D4 classic_bike  2024-01-18 07:39:00 2024-01-18 07:39:00
 4 D83548C7F25E4A96 electric_bike 2024-01-31 16:15:23 2024-01-31 16:15:23
 5 D0AD175B04D31071 classic_bike  2024-01-03 21:29:47 2024-01-03 21:29:47
 6 3178F8568B02A678 classic_bike  2024-01-10 07:26:59 2024-01-10 07:26:58
 7 A25DDEEC43678E83 classic_bike  2024-01-31 05:16:21 2024-01-31 05:16:21
 8 F3CDEBF8F2FC637C classic_bike  2024-01-31 12:07:51 2024-01-31 12:07:51
 9 A0E7422DC369EAD9 electric_bike 2024-01-06 17:41:35 2024-01-06 17:41:35
10 A68286501329EECA classic_bike  2024-01-11 10:02:00 2024-01-11 10:02:00
   start_station_name            start_station_id
   <chr>                         <chr>           
 1 Sheridan Rd & Irving Park Rd  13063           
 2 Kildare Ave & 26th St         365.0           
 3 Burling St & Diversey Pkwy    TA1309000036    
 4 Sedgwick St & Huron St        TA1307000062    
 5 Ashland Ave & Wrightwood Ave  13296           
 6 Clark St & Leland Ave         TA1309000014    
 7 Clarendon Ave & Junior Ter    13389           
 8 Wells St & Elm St             KA1504000135    
 9 Milwaukee Ave & Fullerton Ave 428             
10 State St & 35th St            TA1307000129    
   end_station_name              end_station_id start_lat start_lng end_lat
   <chr>                         <chr>              <dbl>     <dbl>   <dbl>
 1 Sheridan Rd & Irving Park Rd  13063               42.0     -87.7    42.0
 2 Kildare Ave & 26th St         365.0               41.8     -87.7    41.8
 3 Burling St & Diversey Pkwy    TA1309000036        41.9     -87.6    41.9
 4 Sedgwick St & Huron St        TA1307000062        41.9     -87.6    41.9
 5 Ashland Ave & Wrightwood Ave  13296               41.9     -87.7    41.9
 6 Clark St & Leland Ave         TA1309000014        42.0     -87.7    42.0
 7 Clarendon Ave & Junior Ter    13389               42.0     -87.6    42.0
 8 Wells St & Elm St             KA1504000135        41.9     -87.6    41.9
 9 Milwaukee Ave & Fullerton Ave 428                 41.9     -87.7    41.9
10 State St & 35th St            TA1307000129        41.8     -87.6    41.8
   end_lng member_casual month
     <dbl> <chr>         <chr>
 1   -87.7 member        Jan  
 2   -87.7 casual        Jan  
 3   -87.6 member        Jan  
 4   -87.6 member        Jan  
 5   -87.7 member        Jan  
 6   -87.7 member        Jan  
 7   -87.6 member        Jan  
 8   -87.6 member        Jan  
 9   -87.7 member        Jan  
10   -87.6 member        Jan  
ℹ 713 more rows
```

For entries where the column “start time” of a cyclist is higher than the “end time” column, the start time and end time fields are swapped.
```
trip_data[trip_data['started_at'] > trip_data['ended_at'], c('started_at', 'ended_at')] <- trip_data[trip_data['started_at'] > trip_data['ended_at'], c("ended_at", "started_at")]
```

Dropping rows where the column “start time” of a cyclist entry equals to their “end time” column as such erroneous entries may skew the results.
```
trip_data <- trip_data[!(trip_data['started_at'] == trip_data['ended_at']),]
```

Create a column called “ride_length”, by calculating the length of each ride by subtracting the column “started_at” from the column “ended_at.”
```
start <- as.POSIXct(unlist(trip_data['started_at']), format = "%m/%d/%Y %H:%M:%S", tz = "UTC")
end <- as.POSIXct(unlist(trip_data['ended_at']), format = "%m/%d/%Y %H:%M:%S", tz = "UTC")
trip_data['ride_length'] <- as_hms(difftime(end, start))
```

Create a column called “day_of_week,” by calculating the day of the week that each ride started.
```
trip_data['day_of_week'] <- weekdays(start)
```

Find out how many distinct users my dataset has.
```
nrow(trip_data[duplicated(trip_data),])
nrow(trip_data)
n_distinct(trip_data['ride_id'])
```
There are 0 duplicates in the data. The total # of unique cyclist entries in the data post-cleaning is 5859861.

# Phase 4: Analyze

Conducting descriptive analysis of the variables in my data frame.
```
summary(trip_data)
```
```
ride_id          rideable_type        started_at                    
 Length:5860072     Length:5860072     Min.   :2024-01-01 00:00:39.00  
 Class :character   Class :character   1st Qu.:2024-05-20 20:03:56.25  
 Mode  :character   Mode  :character   Median :2024-07-22 20:49:13.35  
                                       Mean   :2024-07-17 08:08:15.75  
                                       3rd Qu.:2024-09-17 20:20:07.67  
                                       Max.   :2024-12-31 23:56:49.84  
                                                                       
    ended_at                      start_station_name start_station_id  
 Min.   :2024-01-01 00:04:20.00   Length:5860072     Length:5860072    
 1st Qu.:2024-05-20 20:22:57.75   Class :character   Class :character  
 Median :2024-07-22 21:09:50.05   Mode  :character   Mode  :character  
 Mean   :2024-07-17 08:25:34.95                                        
 3rd Qu.:2024-09-17 20:33:32.96                                        
 Max.   :2024-12-31 23:59:55.70                                        
                                                                       
 end_station_name   end_station_id       start_lat       start_lng     
 Length:5860072     Length:5860072     Min.   :41.64   Min.   :-87.91  
 Class :character   Class :character   1st Qu.:41.88   1st Qu.:-87.66  
 Mode  :character   Mode  :character   Median :41.90   Median :-87.64  
                                       Mean   :41.90   Mean   :-87.65  
                                       3rd Qu.:41.93   3rd Qu.:-87.63  
                                       Max.   :42.07   Max.   :-87.52  
                                                                       
    end_lat         end_lng        member_casual         month          
 Min.   :16.06   Min.   :-144.05   Length:5860072     Length:5860072    
 1st Qu.:41.88   1st Qu.: -87.66   Class :character   Class :character  
 Median :41.90   Median : -87.64   Mode  :character   Mode  :character  
 Mean   :41.90   Mean   : -87.65                                        
 3rd Qu.:41.93   3rd Qu.: -87.63                                        
 Max.   :87.96   Max.   : 152.53                                        
 NA's   :7232    NA's   :7232                                           
 ride_length       day_of_week       
 Length:5860072    Length:5860072    
 Class1:hms        Class :character  
 Class2:difftime   Mode  :character  
 Mode  :numeric
```                      

Calculating mean and maximum values of ride_length for each cyclistic user
```
trip_data %>% group_by(member_casual) %>% summarize(mean_duration = hms(seconds_to_period(mean(ride_length))), max_duration = hms(seconds_to_period(max(ride_length))))
```
```
 A tibble: 2 × 3
  member_casual mean_duration max_duration
  <chr>         <time>        <time>      
1 casual        25'09.247142" 25:59:56    
2 member        12'46.490959" 45:48:19   
```

Mean duration for casual riders is higher compared to annual members.
Maximum ride duration for casual riders is lower compared to annual members.

Calculating mean and maximum values of ride_length for each cyclistic user on each day of the week.
```
print(trip_data %>% group_by(day_of_week = factor(day_of_week, levels = unique(day_of_week)), member_casual) %>% summarize(mean_duration = hms(seconds_to_period(mean(ride_length))), max_duration = hms(seconds_to_period(max(ride_length)))), n=14)
```
```
A tibble: 14 × 4
Groups:   day_of_week [7]
   day_of_week member_casual mean_duration max_duration
   <fct>       <chr>         <time>        <time>      
 1 Friday      casual        24'31.766179" 25:00:29.000
 2 Friday      member        12'25.642018" 24:59:56.000
 3 Monday      casual        24'08.444195" 24:59:57.666
 4 Monday      member        12'12.706666" 24:59:57.000
 5 Saturday    casual        28'11.711588" 25:59:56.000
 6 Saturday    member        14'04.668757" 25:59:48.000
 7 Wednesday   casual        22'16.730810" 25:00:26.665
 8 Wednesday   member        12'27.462150" 45:48:19.000
 9 Sunday      casual        29'25.466606" 25:00:31.000
10 Sunday      member        14'16.587231" 25:00:29.000
11 Thursday    casual        21'54.606633" 24:59:57.877
12 Thursday    member        12'15.527232" 24:59:58.097
13 Tuesday     casual        21'30.943550" 25:00:51.765
14 Tuesday     member        12'17.070635" 24:59:57.212
```

Mean duration for casual riders is higher compared to annual members in each day of the week.
Maximum ride duration for casual riders is higher compared to annual members in each day of the week.

Calculating mean and maximum values of ride_length for each cyclistic user in the past 12 months.
```
print(trip_data %>% group_by(month = factor(month, levels = unique(month)), member_casual) %>% summarize(mean_duration = hms(seconds_to_period(mean(ride_length))), max_duration = hms(seconds_to_period(max(ride_length)))), n=24)
```
```
A tibble: 24 × 4
Groups:   month [12]
   month member_casual mean_duration max_duration
   <fct> <chr>         <time>        <time>      
 1 Jan   casual        21'19.023887" 24:59:57.000
 2 Jan   member        13'48.196090" 24:59:57.000
 3 Feb   casual        25'11.520707" 25:00:29.000
 4 Feb   member        12'54.912451" 25:00:29.000
 5 Mar   casual        24'57.901451" 25:59:56.000
 6 Mar   member        11'58.312321" 25:59:48.000
 7 Apr   casual        26'01.217469" 25:00:31.000
 8 Apr   member        12'22.443321" 24:59:56.000
 9 May   casual        27'44.725749" 24:59:57.000
10 May   member        13'30.022946" 45:48:19.000
11 Jun   casual        27'36.643018" 25:00:26.665
12 Jun   member        13'52.537878" 24:59:56.298
13 Jul   casual        27'41.435849" 25:00:13.912
14 Jul   member        13'42.611779" 24:59:57.212
15 Aug   casual        26'03.902408" 24:59:57.932
16 Aug   member        13'25.176402" 24:59:56.892
17 Sep   casual        21'37.909993" 25:00:29.147
18 Sep   member        12'12.745242" 24:59:58.097
19 Oct   casual        23'13.759232" 24:59:57.666
20 Oct   member        11'58.741938" 24:59:56.775
21 Nov   casual        19'30.425834" 25:00:51.765
22 Nov   member        11'07.173898" 24:59:56.042
23 Dec   casual        17'45.344677" 24:59:57.464
24 Dec   member        10'45.827754" 24:59:55.872
```

Mean duration for casual riders is higher compared to annual members in the past 12 months.
Maximum ride duration for casual riders is higher compared to annual members in the past 12 months.

# Phase 5: Visualizations

Helper function: Detecting and Removing outliers for visualization
```
detect_outlier <- function(x) {
    Quantile1 <- quantile(x, probs=.25)
    Quantile3 <- quantile(x, probs=.75)
    IQR = Quantile3-Quantile1
    x > Quantile3 + (IQR*1.5) | x < Quantile1 - (IQR*1.5)
}
remove_outlier <- function(dataframe) {
      dataframe <- dataframe[!detect_outlier(dataframe[['ride_length']]),]
}
trip_data_sampled <- remove_outlier(trip_data)
```

I have removed outliers from the data to make the plots smooth and easy to visualize.

### Fig 1: Bar plots of the number of rides for each cyclistic user in the past 12 months.

```
ggplot(data=trip_data)+
  geom_bar(mapping=aes(x=factor(month, levels = unique(month)),fill=member_casual))+
  scale_fill_manual(values=c("#999999", "#E69F00", "#56B4E9"))+
  scale_y_continuous(labels= comma)+ 
  theme(axis.text.x = element_text(angle = 90, hjust=1, vjust=.5))+
  labs(title="Cyclistic Trip Data: Bar plots of the number of cyclistic rides on each members", subtitle="Sample of two types of users: casual riders vs annual riders",caption="Data collected by Motivate International Inc.", x="Day of the week", y = "Number of rides")+
  facet_wrap(~member_casual)
```
<img width="431" alt="Rplot1" src="https://github.com/user-attachments/assets/c930597d-ae8c-4602-9688-42b067838aad" />

The bar plots highlight that the number of trips for casual riders and annual members are highest in September.

### Fig 2: Bar plots of the number of rides for each cyclistic user during weekdays.

```
ggplot(data=trip_data)+
  geom_bar(mapping=aes(x=factor(day_of_week, levels = unique(day_of_week)),fill=member_casual))+
  scale_fill_manual(values=c("#999999", "#E69F00", "#56B4E9"))+
  scale_y_continuous(labels= comma)+ 
  theme(axis.text.x = element_text(angle = 90, hjust=1, vjust=.5))+
  labs(title="Cyclistic Trip Data: Bar plots of the number of cyclistic rides on each day of the week", subtitle="Sample of two types of users: casual riders vs annual members",caption="Data collected by Motivate International Inc.", x="Day of the week", y = "Number of rides")+
  facet_wrap(~member_casual)
```

<img width="431" alt="Rplot2" src="https://github.com/user-attachments/assets/7d50188a-9e30-4afd-8389-34c465129106" />

The bar plots show that the number of trips for casual riders is highest on Saturday compared to Wednesday for annual members.

# Phase 6: Act

Recommendations for Cyclistic Marketing Strategy: Cyclistic could advertize on the financial benefits of being an annual member. Being an annual member allows you to take more trips in shorter durations. Showcasing how annual membership allows casual riders to save money on individual rides and enjoy a seamless experience with easy access to bikes throughout the year could be fruitful in converting casual riders to annual members. The data clearly shows the seasonality of the service usage among casual riders, it will make sense to run promo campaigns during warmer months when the usage peaks. This way it is possible to cover more potential customers to convert to buy the membership.
