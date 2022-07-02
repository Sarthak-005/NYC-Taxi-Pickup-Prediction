![NYC_](https://user-images.githubusercontent.com/58303643/177010587-f349c09c-629f-41f6-a6d5-542cc2277adf.gif)
# NYC Taxi Pickup Prediction 
The task is to analyze and predict pickup density for taxis at a given time and region in New York, exracted features using time series and fourier transforms(simple moving average, weighted moving average, etc), applied regression models(Xgboost, Random Forest,Logistic Regression)
## Objectives:
Our end-user is an Taxi-Driver, who is probably going to use this on his smartphone.
So our primary Objective is to Predict the number of pickups as accurately as possible for aech region in next 10mins (in 10mins interval).
So according to the objective we need to divide the NYC into various regions.
For our model, at any time input will be :
1) Current location of the taxi, hence we will get the region (pre-defined by us) in which the TaxiDriver is present.
2) Current time. So for eg. say we get current time as 2:00 pm, then we need to do the estimation for next 10 min interval i.e 2:00 - 2:10 pm

So our output would be - 'output is specified as our primary objective'

## Data Information
Get the data from : http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml (2016 data) The data used in the attached datasets were collected and provided to the NYC Taxi and Limousine Commission (TLC)

### Yellow Taxi: Yellow Medallion Taxicabs
These are the famous NYC yellow taxis that provide transportation exclusively through street-hails. The number of taxicabs is limited by a finite number of medallions issued by the TLC. You access this mode of transportation by standing in the street and hailing an available taxi with your hand. The pickups are not pre-arranged.

### For Hire Vehicles (FHVs)
FHV transportation is accessed by a pre-arrangement with a dispatcher or limo company. These FHVs are not permitted to pick up passengers via street hails, as those rides are not considered pre-arranged.

### Green Taxi: Street Hail Livery (SHL)
The SHL program will allow livery vehicle owners to license and outfit their vehicles with green borough taxi branding, meters, credit card machines, and ultimately the right to accept street hails in addition to pre-arranged rides.

Credits: Quora

### Footnote:
In the given notebook we are considering only the yellow taxis for the time period between Jan - Mar 2015 & Jan - Mar 2016

## Data Cleaning:
Pickup/Drop-off Latitude and Longitude :
It is inferred from the source https://www.flickr.com/places/info/2459115 that New York is bounded by the location coordinates(lat,long) - (40.5774, -74.15) & (40.9176,-73.7004). So, any coordinates not within these coordinates are not considered by us as we are only concerned with pickups which originate within New York.

- Trip Durations:
According to NYC Taxi & Limousine Commision Regulations the maximum allowed trip duration in a 24 hour interval is 12 hours. So we remove the points where the duration is >12 hr

- Speed:
We found that the 99.9th percentile value of speed is 45.31 mph. So, we consider only the data points where the computed speed is <45.31mph. We also observed that the avg speed in New York is 12.45miles/hr, i.e. a cab driver can travel 2 miles per 10min on avg

- Trip Distance:
The 99.9th percentile value of the distance covered in a ride is ~23 miles. So we remove rows with large trip distances

- Fare:
From percentile and graphical analysis, we set the upper limit of total fare to be 1000$ and consider only the data points which satisfies the limit
## ML Problem Formulation
### Time-series forecasting and Regression
To find number of pickups, given location cordinates(latitude and longitude) and time, in the query reigion and surrounding regions.
To solve the above we would be using data collected in Jan - Mar 2015 to predict the pickups in Jan - Mar 2016.

## Data preparation:
- Clustering/Segmentaion:
Now we break whole of NYC into clusters/segments/regions. We choose the (lat,long) of pickup as features and we apply K-Means clustering algorithm(we find the right K using GridSearch). On choosing a cluster size of 40, we find an optimal min inter-cluster distance. Finally, we assign the cluster no. to each data point

- Time-binning:
We use the unix time-stamps to find the 10 min time-bins each data point belongs to(index of the 10min time interval to which that data point belongs)

- Smoothing time-series data:
In our time-series data plot, there will be some 10-min bins where there are no pickups. And its not useful to predict zero pickups for a data point and moreover these points will create a problem in Moving Averages(using ratios). Thus, we smooth our training data(2015)(as in smoothing we are looking at future values) and fill with zero our test data(2016)

- Fourier Transform:
From the Fourier transform plot of our time-series data we find the top amplitudes and corresponding frequencies

### Performance metrics
- Mean Absolute percentage error.
- Mean Squared error.

## Modeling : Baseline Models
I haveused the following baseline model by generating feature with ratio and previous value at a time (t-1) and will calculate Mean Absolute Percentage Error

- Moving Averages
- Weighted Moving Averages
- Exponential Moving Averages
Along with that, we will use below regression model by selecting best hyper-parameter with the help of different technique depending on hype parameter to predict the taxi demand.

- Linear Regression with GridSearch
- Random Forest Regressor with Random search
- XgBoost Regressor with Random search
![image](https://user-images.githubusercontent.com/58303643/177010545-3cddb80a-cca1-4444-8217-80b2546c5210.png)

