# Wildfire and Air Quality Analysis: Redding

Welcome to the repository that explores the intricate relationship between wildfires and air quality, specifically in the vicinity of Redding, California. The analysis is structured into three sections, each shedding light on different aspects of this complex interplay.

## Data Sources
Two primary data sources drive this project:

### Wildfire Data:

[USGS Wildland Fire Dataset](https://www.sciencebase.gov/catalog/item/61aa537dd34eb622f699df81)
Comprehensive information on fires recorded up to 2020, providing details such as geographic coordinates, area burned, and more.

### Air Quality Index (AQI) Data:
[AQI Dataset](https://aqs.epa.gov/aqsweb/documents/data_api.html)
Ambient air sample data collected nationwide, including meteorological information and AQI values for various pollutants from multiple monitoring stations.

## Section 1: [Wildfire Data Extraction](./Wildfire_Data_Extraction.ipynb)
### Overview
This notebook extracts the wildfire data extracted from the [USGS Wildland Fire Dataset](https://www.sciencebase.gov/catalog/item/61aa537dd34eb622f699df81). The data includes information about all the fires across the United States. Each fire is called a feature and will have geometric ring data indicating the latitude and longitude of the place under fire. If there are multiple geometric rings the largest ring will be the first one in the array.

### Steps
From all the records we got from the USGS Wildland Fire Dataset, we are processing and filtering out using the below criteria and considerations.

1. For each fire we are calculating its distance from Redding. In case of multiple rings available, we are only calculating for the largest ring. While considering the distance we are using the shortest point to determine the distance i.e. the point closest to Redding will be used to calculate the distance. We are only keeping the feature if the distance is less than 1250 miles.
2. If the year is older than 1963 we are not taking those data points
3. We are removing the geometric ring data and storing three new features in `attributes` i.e. `fire_lat`, `fire_lon` of the fire, and `distance_from_redding`.

### Reference
Some of the code has been taken from David McDonald's work on extracting and geojson calculation.

### Finding
This notebook helps in generating a processed subset of the data which we can work on for our visualization.

## Section 2: [Air Quality Index Data Extraction](./AQI_Analysis.ipynb)
### Overview
This notebook also involves steps to extract and process data. This notebook focuses on gathering Air Quality Index data from the [AQS EPA website](https://aqs.epa.gov/aqsweb/documents/data_api.html).

### Steps
We get daily data from AQS EPA API which we need from 1963 to 2020, we will still fetch till the current year that is 2023 just to see where the trend is going. We are converting the daily data to yearly AQI using the below steps:

1. For each day we have:
    - Data from multiple sensors/stations
    - We get data for up to 8 pollutants that have an AQI individually

2. For each day we group by pollutants and take the maximum value of all the values
3. Then for each year we take the worst 5 days and take an average of those 5.

The intuition behind this is that usually for any day we take the maximum of all the pollutants according to the standard. We are then taking the worst 10 so that we can have a better estimation concerning our smoke impact since the smoke impact will just have the fire and distance parameters and make the estimate extreme. This might not match up if we just look at the rest of the year (when AQI is less). Thus to get an accurate estimation of the fire smoke estimation's effect on air quality we are taking the worst 10 days.

### Reference
Some of the code has been taken from David McDonald's work on extracting and geojson calculation.
Some of the logic to process the daily pollutant data and get a yearly AQI estimate was calculated in collaboration with Aaditya and in discussion with Professor David McDonald.

### Findings
The notebook helps in generating a processed AQI dataset using all the pollutants and sensors that recorded it near Redding, California.


## Section 3: [Estimating Air Quality and Time Series Analysis](./Wildfire_Data_Analysis.ipynb)

### Overview
Building on the data from the above notebooks, this notebook focuses on estimating the fire smoke estimates of wildfires. It includes the analysis of fire smoke estimate data and the application of time series forecasting to predict future fire smoke. We also compare our estimate with the standard AQI which is calculated in the second notebook.

### Steps

1. First we load both the JSON data that we generated from the above sections
2. From each fire feature we estimate the smoke using the below logic 
    We are taking the max distance i.e. 1250 miles for each fire to convert into a range of 0-1, where o being the farthest from Redding and 1 being the closest. We are using this distance to penalize the overall score. Another factor that might affect the fire is the Hectares burnt. For hectares burnt, we can see from the descriptive statistics that there is a huge difference between the mean and 50% of the distribution. To accommodate for these extreme ended values, we are using the value at 75% distribution which is 204.
3. We plot the below three graphs:
    - Wildfires by Distance
    - Area Burned Over Time
    - Standard AQI vs Fire Smoke Estimate Over Time


### Findings
The analysis provides insights into the estimated AQI trends and predicts future air quality based on historical data. The comparison of actual data with predictions helps evaluate the model's performance.

## Conclusion

These notebooks collectively offer a comprehensive analysis of the relationship between wildfires and air quality near Redding, California from 1963 to 2020. The findings and visualizations contribute to a better understanding of the patterns, trends, and potential impact of wildfires on air quality in the region.

## License

The dataset is made available under the [Creative Commons License](https://creativecommons.org/licenses/by/4.0/).