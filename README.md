# Pune_BusRoutes
Visualize Pune city bus routes

# Introduction

## Background

Pune Mahanagar Parivahan Mahamandal Ltd (PMPML) is the public transport bus service provider in Pune and Pimpri Chinchwad Corporation limits. As per latest data PMPML has approx. 1500 buses maintained at several depots, out of which approx. 1300 buses are operational. This fleet serves around one million one hundred thousand passengers each day! The frequencies on individual routes vary greatly according to demand. Certain long-distance routes are serviced only a few times per day.

Now there are many private transport options like Ola, Uber available at fingertips. But if there is a better public transport network and frequency, then one may consider going by public transport as well.

## Problem

Here we will try to visualize the bus frequency of some of the busiest bus-stops in Pune city. We&#39;ve two main resources, first is map of bus routes and second is a timetable of buses on the route. Let&#39;s talk about the first one, map of bus routes. It gives us information about what route bus will follow in a geographic context, but it won&#39;t give any information about frequency of buses for a particular bus stop. Let&#39;s talk about the second one, bus timetable. It gives us information about bus frequency at a particular bus stop but lacking geographic context. How about we combine this two information and visualize them!

Apart from analyzing bus frequency we will also try to identify or cluster the bus stops according their locality. For example, we will try to find out whether the particular bus stop is located in the commercial / residential zone, developed / under-developed area etc. This will help us to analyze commute patterns and justify the number of bus stops for particular neighborhoods.

## Interest

Bus transit visualization will give the frequency of buses on particular routes for a particular time period. This can help us to analyse service efficacy, transit performance, traffic management, route spacing, geographical coverage / network of service, cost efficiency and service effectiveness. This will help city planners to propose the bus stops and route network. Visualization can also help planners to explore new modes of public transport such as metro, trains as a replacement to fulfil the demand of the growing population of the city.

Lack of foresight can waste lot of people&#39;s money ([news](https://timesofindia.indiatimes.com/city/pune/demolition-of-flyovers-to-be-completed-in-15-days/articleshow/76948780.cms)), city planning with the help of modern methods can prevent our economic waste !

# Data

## Data Sources

### The PMPML Data

_Bus Timetable and Bus Stop Co-ordinates_

Pune Municipal Corporation (PMC) is providing open data sets for citizens of Pune as part of Open Governance, Open Data initiative of Government of India. Anyone can access this non-sensitive data relating to the city by just simple email registration and help PMC to develop solutions to Pune&#39;s problems.

Below are the details of dataset we are going access:

URL : [PMC Open Data Store](http://opendata.punecorporation.org/Citizen/CitizenDatasets/Index)

Dataset title : PMPML Bus Routes - July 2019

Tag/ category : PMPML

File type : .zip

Size : 8.81 MB

One needs to access the above link using a personal account login and search the dataset with mentioned title and category. The dataset can be downloaded and unzipped to access text files. There are total 9 text files out of which we are going to use below 5 files for analysis.

File details:

1. stops.txt

Contains list of bus stops along with their location coordinates and stop ID.

1. shapes.txt

Contains list of unique route shapes in the form of coordinates in the sequential order.

1. calendar.txt

Contains trip service IDs that are executed for the period 2nd Aug 2019 to 31st Oct 2019.

1. trips.txt

Contains unique trip IDs and route shape ID, service ID required for trip execution.

1. stop\_times.txt

Contains trip ID wise departure and arrival time at each bus stop of the trip in the sequential manner.

### The Foursquare Data

_Bus Stop Surrounding Venues Information_

To categorize bus stops according to their type of neighbourhood we will need a database with venues data. We are going to use Foursquare Places API that will give us the latest information about our bus stop surroundings.

For Foursquare query we will need following information:

1. Bus stop location (can be taken from &#39;stop.txt&#39; mentioned above)
2. Search code of main venue categories to search for.

We can find search codes in below [Foursquare documentation](https://developer.foursquare.com/docs/build-with-foursquare/categories/).

We&#39;ll query 10 main categories (refer Table 1Table 1) with their search codes for every bus stop.

1. Search radius (we will search within 1km radius from bus stop location)

| Sr.No. | Venue Category | Examples |
| --- | --- | --- |
| 1 | Arts &amp; Entertainment | Movies theatres, museum, sports stadium, theme park, zoo, etc. |
| 2 | College &amp; University | University, college, school, grounds, etc. |
| 3 | Event | Street sale, festival place, main market, etc. |
| 4 | Food | Restaurant, bakery, coffee shop, cafe, etc. |
| 5 | Nightlife Spot | Bar, lounge, pub, etc. |
| 6 | Outdoors &amp; Recreation | Botanical garden, gym, pool, track, hill, farm, forest, lake, river, etc. |
| 7 | Professional &amp; Other Places | Business center, distribution center, factory, government building, hospitals, camps, etc. |
| 8 | Residence | Home, bungalow, apartments, residential building, etc. |
| 9 | Shop &amp; Service | ATM, bank, shops, petrol pumps, salons, etc. |
| 10 | Travel &amp; Transport | bus/railway/metro station, airport, hotels, tunnels, roads, etc. |

_Table 1 Foursquare venue categories_

Sample of Foursquare data for few bus-stops (refer Table 2):

| **Bus\_stop\_id** | 32769 | 32770 | 32771 | 32772 | 32773 |
| --- | --- | --- | --- | --- | --- |
| **Arts &amp; Entertainment** | 1 | 1 | 3 | 5 | 5 |
| **College &amp; University** | 1 | 5 | 1 | 14 | 14 |
| **Event** | 1 | 0 | 0 | 0 | 0 |
| **Food** | 26 | 8 | 5 | 46 | 45 |
| **Nightlife Spot** | 0 | 3 | 2 | 5 | 5 |
| **Outdoors &amp; Recreation** | 3 | 5 | 5 | 5 | 5 |
| **Professional &amp; Other Places** | 3 | 4 | 2 | 21 | 23 |
| **Residence** | 4 | 3 | 6 | 5 | 5 |
| **Shop &amp; Service** | 3 | 23 | 9 | 27 | 26 |
| **Travel &amp; Transport** | 3 | 2 | 3 | 22 | 22 |

_Table 2 Sample Foursquare data for some bus stop ids_

## Initial feature selection, Data cleaning and validation

### The PMPML Data

Before processing bus data downloaded from PMC website, we checked each file and data associated with each feature. Based on sufficient data availability and its use for our analysis, we decided to keep or discard the initial features. (Refer Table 3 for more details)

| Sr. No. | Resource File Name | Feature Name | Kept / Dropped | Reason |
| --- | --- | --- | --- | --- |
| 1 | stops.txt | stop\_id | Kept | Can be used to visualize bus stops on map |
| 2 | stops.txt | stop\_name | Kept | Can be used to visualize bus stops on map |
| 3 | stops.txt | stop\_lat | Kept | Can be used to visualize bus stops on map |
| 4 | stops.txt | stop\_lon | Kept | Can be used to visualize bus stops on map |
| 5 | stops.txt | zone\_id | Dropped | Insufficient data |
| 6 | stops.txt | stop\_url | Dropped | Insufficient data |
| 7 | stops.txt | location\_type | Dropped | Insufficient data |
| 8 | stops.txt | parent\_station | Dropped | Insufficient data |
| 9 | stops.txt | stop\_timezone | Dropped | Insufficient data |
| 10 | stops.txt | wheelchair\_boarding | Dropped | Insufficient data |
| 11 | stops.txt | stop\_desc | Dropped | Insufficient data |
| 12 | stops.txt | stop\_code | Dropped | Insufficient data |
| 13 | shapes.txt | shape\_id | Kept | Can be used to calculate trip/ route distance and can be dropped after that. |
| 14 | shapes.txt | shape\_pt\_lat | Kept | Can be used to calculate trip/ route distance and can be dropped after that. |
| 15 | shapes.txt | shape\_pt\_lon | Kept | Can be used to calculate trip/ route distance and can be dropped after that. |
| 16 | shapes.txt | shape\_pt\_sequence | Kept | Can be used to calculate trip/ route distance and can be dropped after that. |
| 17 | shapes.txt | shape\_dist\_traveled | Dropped | Insufficient data |
| 18 | trips.txt | route\_id | Kept | Can be used to identify trip service day and route to be used |
| 19 | trips.txt | service\_id | Kept | Can be used to identify trip service day and route to be used |
| 20 | trips.txt | trip\_id | Kept | Can be used to identify trip service day and route to be used |
| 21 | trips.txt | trip\_headsign | Kept | Can be used to identify trip service day and route to be used |
| 22 | trips.txt | direction\_id | Kept | Can be used to identify trip service day and route to be used |
| 23 | trips.txt | shape\_id | Kept | Can be used to identify trip service day and route to be used |
| 24 | trips.txt | duty | Dropped | This data is not required for analysis |
| 25 | trips.txt | duty\_sequence\_number | Dropped | This data is not required for analysis |
| 26 | trips.txt | run\_sequence\_number | Dropped | This data is not required for analysis |
| 27 | trips.txt | trip\_short\_name | Dropped | Insufficient data |
| 28 | trips.txt | block\_id | Dropped | Insufficient data |
| 29 | trips.txt | wheelchair\_accessible | Dropped | Insufficient data |
| 30 | trips.txt | bikes\_allowed | Dropped | Insufficient data |
| 31 | stop\_times.txt | trip\_id | Kept | Can be used to filter arriving bus/es at particular bus stop for particular time. |
| 32 | stop\_times.txt | arrival\_time | Kept | Can be used to filter arriving bus/es at particular bus stop for particular time. |
| 33 | stop\_times.txt | departure\_time | Kept | Can be used to filter arriving bus/es at particular bus stop for particular time. |
| 34 | stop\_times.txt | stop\_id | Kept | Can be used to filter arriving bus/es at particular bus stop for particular time. |
| 35 | stop\_times.txt | stop\_sequence | Kept | Can be used to filter arriving bus/es at particular bus stop for particular time. |
| 36 | stop\_times.txt | stop\_headsign | Dropped | Insufficient data |
| 37 | stop\_times.txt | pickup\_type | Dropped | Insufficient data |
| 38 | stop\_times.txt | drop\_off\_type | Dropped | Insufficient data |
| 39 | stop\_times.txt | shape\_dist\_traveled | Dropped | Insufficient data |
| 40 | stop\_times.txt | timepoint | Dropped | Insufficient data |

_Table 3 Initial feature selection of PMPML data_

I found a few problems with &quot;stop\_times.txt&quot; which contains trip ID wise departure and arrival time at each bus stop of the trip in the sequential manner.

First, arrival and departure times were not in correct 24hr format.

E.g. For arrival time &quot;23:59:40&quot;, departure time is &quot;24:01:21&quot;, such values are observed when the trip begins late night and ends early in the morning. Ideally here departure time should be &quot;+1 Day 00:01:21&quot;. However, we can use this problem itself as filter to correct the time information as per our requirement.

The second problem with dataset is the difference between arrival and departure time is not realistic. For some bus stops difference is greater than 5 hrs. To solve this issue, we can use 75% occurrence value to replace time differences greater than 3 mins.

The third problem is also similar to second where the difference between the arrival times of two consecutive bus stops is greater than 5 hrs., so similar solution can be applied to this issue as well. We can use 30 mins as lowest time difference for replacement considering there are some long-distance trips.

Apart from the above issues, there were a few entries that need individual attention to correct arrival and departure times.

Now we can derive some features from available features, refer Table 4:

| Sr. No. | Derived-Features | Derived from features | Method |
| --- | --- | --- | --- |
| 1 | Trip distance | Shape point latitude and longitude | Every trip or trip ID has associated route ID. Every route ID has associated shape ID. Shape ID has number of shape points. Shape point is nothing but location on map with latitude and longitude information. When we connect these shape points a route is formed on the map. Now, if we calculate distance between consecutive shape points and sum them up then we&#39;ll get distance between 1st and last shape point which is nothing but route distance or trip distance.We can now drop kept features from &#39;shapes.txt&#39;. |
| 2 | Trip begin and end time, Trip Duration | bus stop arrival and departure time, bus stop sequence number | For every trip bus follows a sequence of bus stops to reach destination. We can use arrival time at first bus stop as trip begin time and arrival time at last bus stop as trip end time. Trip duration is nothing but difference between trip end and begin time. |

_Table 4 Derived features for PMPML data_

After preparing and combining the data from all the text files, there were 21803 unique trips with 19 features in the data (refer Table 5).

| Sr.No. | Resource / File Name | Feature Name |
| --- | --- | --- |
| 1 | stops.txt | stop\_id |
| 2 | stops.txt | stop\_name |
| 3 | stops.txt | stop\_lat |
| 4 | stops.txt | stop\_lon |
| 5 | trips.txt | route\_id |
| 6 | trips.txt | service\_id |
| 7 | trips.txt | trip\_id |
| 8 | trips.txt | trip\_headsign |
| 9 | trips.txt | direction\_id |
| 10 | trips.txt | shape\_id |
| 11 | stop\_times.txt | trip\_id |
| 12 | stop\_times.txt | arrival\_time |
| 13 | stop\_times.txt | departure\_time |
| 14 | stop\_times.txt | stop\_id |
| 15 | stop\_times.txt | stop\_sequence |
| 16 | Derived features | trip\_distance |
| 17 | Derived features | trip\_duration |
| 18 | Derived features | trip\_bgn\_time |
| 19 | Derived features | trip\_end\_time |

_Table 5 Final features_

# Methodology

## For clustering the data on the basis of stop location/venue details

We&#39;ve PMPML&#39;s bus stop data and Foursquare venues data of each bus stop surroundings. As a first step we&#39;ll normalize the data set of Foursquare data and cluster them using the KNN algorithm. We&#39;ll find out the best suitable value of number of clusters using fixed random state to make randomness deterministic. Then we&#39;ll train the model with the best value of clusters and merge the generated cluster labels with our PMPML&#39;s bus-stop dataframe. For further analysis on how clusters are formed we can plot the 75% values of each venue category for each cluster.

## For Visualizing the bus frequency

There are 349 bus-stops in Pune city. We&#39;ve to limit the number of bus-stops for visualization because visualizing all of them at a time may not look useful. So, we&#39;ll find out busiest bus-stops on the basis of trip count. Here trip count is nothing but the summation of trips arriving and departing from bus stop. We&#39;ll normalize the trip count data for each bus stop and apply the KNN algorithm to get clusters. We can then check how clustering is performed using bar plot, where we can select top busy bus-stops. Once we limit the of bus-stops we&#39;ll filter our dataframe with these bus stops depending on trip begin stop id or trip end stop id.

Trips are executed depending on service ID. Service ID is selected depending on day of the week. So, for each day of the week we will have a list of service IDs to run. We&#39;ll select duration as Friday 02/08/2019 00:00:00 hrs to 03/08/2019 02:00:00 hrs for our analysis. In-short, we&#39;re going to visualize the bus-frequency on Friday.

Now we&#39;ve our dataframe filtered with busiest bus stops and service IDs. All we&#39;ve to do is repeatedly filter dataframe for every second for mentioned duration and map bus location for every trip at given time. Also, as we map the buses, we&#39;ve to save them into the image format. But that will create an image for every second and that won&#39;t be easy to handle. On an average for every trip, it takes approx. 2 mins to reach its next bus stop. So, we&#39;ll change our filter frequency from 1 Sec to 2mins, that will show considerable movement of buses on the map/image.

Once we&#39;ve all the map images, we can combine them to form GIF for visualization.

# Results &amp; Discussions

## Clustering the data on the basis of stop location/venue details

After clustering the bus stops based on Foursquare data, we can get the following map. (refer Figure 1)

![](RackMultipart20210415-4-k9icge_html_4d42fd7bccaa4470.jpg)

_Figure 1 Map: clustering bus stops on the basis of Foursquare venue details_

Let&#39;s try to understand clustering from the below line plot. (refer Figure 2)

![](RackMultipart20210415-4-k9icge_html_339f8841d8af577e.jpg)

_Figure 2 Line plot: Understanding the bus stop clusters_

What are cluster 1 &amp; 2?

There are two significant differences between these two clusters (Number of Events and travel/transport venues). Cluster 2 contains the highest amount of transport venues compared to any of the clusters. Cluster 1 is very similar to cluster 2 with the only significant difference in the number of Events venues.

What are cluster 0 &amp; 3?

Cluster 0 has a smaller number of venues compared to any of the other clusters.

Cluster 3 has more venues than cluster 0 but lesser than cluster 1 or 2.

So, from the above observation and plot, we can say that:

- Cluster 2 is a developed part of the city and contains bus stops that are close to transport venues such as railway / bus / metro stations or airports etc.
- Cluster 1 is also a developed part of the city with the highest amount of Event venues.
- Cluster 0 is remote / underdeveloped part of the city and
- Cluster 3 is a developing part of the city.
- From the map, we can say that, although clusters 1,2, and 3 have lesser bus stops, they are densely located. This suggests that bus-stops have very good connectivity in the central / old part of the city.

We also plotted the bar chart of the number of bus stops for each cluster. (refer Figure 3)

![](RackMultipart20210415-4-k9icge_html_aa4afb7642ca597f.jpg)

_Figure 3 Bar plot: Understanding the bus stop clusters_

From the bar chart, we can say that bus service has a great network in for remote parts of the city (cluster 0), then it also covers travel / transport venues with a comparatively (compared to cluster 1) higher number of bus stops (cluster 2).

## Visualizing the bus frequency

We can also plot bus frequency for different times of the day. (refer Figure 4)

![](RackMultipart20210415-4-k9icge_html_54d025a8e578fe20.jpg)

_Figure 4 Bus service frequency during whole day_

From the plot, we can say that bus frequency rapidly increases around 6 am and touches the morning peak of approx. 660 buses in motion at around 9:30 am. We can call it &#39;morning rush&#39;.

After the noon bus frequency again increases close to 700 buses in motion and remains there up till 05:00 pm.

Between 05:00 pm to 07:30 pm bus frequency is close to 750 buses in motion, we can call it as &#39;evening rush&#39;.

From 07:30 pm onwards frequency declines gradually till 10:00 pm, and after that, it takes a plunge.

Now, we&#39;ve images/frames that can be used to make GIF. (refer Figure 5)

There are approx. 819 images/frames (size: 339 MB), since combining these images to form a GIF will require a lot of memory, I&#39;ve used &#39;PhotoScape X&#39; software (for Windows 10) to make final [GIF](https://github.com/2020mt93213/Pune_BusRoutes/blob/main/00_Ntbk_Resources/01_DataAnalysis/02_Final_GIF/BusesInMotion_20190802.gif).

Software settings are:

- Frame rate: 20 frames/sec
- Frame size: 500 x 251 px
- Loop: Infinite

![](RackMultipart20210415-4-k9icge_html_b64016ae7a833f41.png)

_Figure 5 Screen grab of bus service frequency visualization for whole day_

From the GIF visualization we can conclude on the following points:

- There are approx. 7 roads that are used by bus trips to connect / pass through the central part of the city.
- Bus frequency is fairly similar on every bus stop on these 7 main roads of the city for any given period.
- We can observe comparatively high traffic in the central part of the city from 7:30 am to 10:30 pm

# Conclusion

Location data is continuously growing so we have to find new ways to visualize this information and it must be clearer and more intuitive. In this project, we tried this new approach to visualize the bus frequency in Pune city. We can study individual bus stops and their pattern for more details. However, this data will be more effective if we can analyse it with community mobility data. It can give more insights into how people move during a different time and in different parts of the city. We can have a comparison with current transit options and improve or if required implement new transit modes.

# Setting up environment and Installation
You can start installation of [Python](https://www.python.org/) and [Jupyter Notebook](https://jupyter.org/) accroding to you system specifications.

For a local installation, make sure you have
[pip installed](https://pip.readthedocs.io/en/stable/installing/) and run:

    $ pip install notebook

## Usage - Running Jupyter notebook

### Running in a local installation

Launch with:

    $ jupyter notebook

## Contributing

If you're reading this section, you're probably interested in contributing to this project. Welcome and thanks for your interest in contributing!

Please familiarize yourself with using the Jupyter Notebooks of this project, and introduce yourself on the mailing list and share what area of the project you are interested in working on.

# References

1. PMC Open Data Store:

[http://opendata.punecorporation.org/Citizen/CitizenDatasets/Index](http://opendata.punecorporation.org/Citizen/CitizenDatasets/Index)

1. Foursquare documentation:

[https://developer.foursquare.com/docs/build-with-foursquare/categories/](https://developer.foursquare.com/docs/build-with-foursquare/categories/)

1. GIF software, PhotoScape X

[http://x.photoscape.org/](http://x.photoscape.org/)

1. Final GIF of Bus service frequency in Pune city

https://github.com/2020mt93213/Pune\_BusRoutes/blob/main/00\_Ntbk\_Resources/01\_DataAnalysis/02\_Final\_GIF/BusesInMotion\_20190802.gif

1. Github repository

[https://github.com/2020mt93213/Pune\_BusRoutes](https://github.com/2020mt93213/Pune_BusRoutes)
