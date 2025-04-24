# Delhivery-Feature-Engineering
Time series feature engineering project on Delhivery's e-commerce logistics data. Focused on transforming raw delivery pipeline data into meaningful features to support forecasting models. Includes trip segmentation, distances, cutoffs, OSRM-based estimates, and treatment of outliers, missing values, and categorical fields.
<img src="https://i.imgur.com/Iq9D3CN.png" alt="delhivery_img" width="720"/>

## Business Problem Statement:
The company wants to understand and process the data coming out of data engineering pipelines:

- Clean, sanitize and manipulate data to get useful features out of raw fields.
- Make sense out of the raw data and help the data science team to build forecasting models on it.

**Concepts Used:**

- Feature Creation
- Relationship between Features
- Column Normalization /Column Standardization
- Handling categorical values
- Missing values - Outlier treatment / Types of outliers

### Dataset:</br>
Dataset Link: [Delhivery_data.csv](https://d2beiqkhq929f0.cloudfront.net/public_assets/assets/000/001/551/original/delhivery_data.csv)

- There are a total of **25 variables**:
  - 8 text-based (categorical)
  - 12 numerical data columns
- The dataset has **14.7k observations** with **no missing or duplicate values**.

### Key Insights
**Keys**
- `trip_uuid`: All Ids values are unique, as we used the group by function on it.
- `trip_creation_time`: The available data is between **12th September 2018** to **3rd October 2018** — about 1 month of data.
- `route_schedule_uuid`: There are **1,497 unique operating routes**.
- `route_type`: There are two main route types:
  - **Carting**: 8,906 trips
  - **FTL**: 5,881 trips

**Time Variables**
- `start_scan_to_end_scan` (hours): Mean = 961.26, Median = 449.00, Std = 1037.01, Min = 20.00, Max = 7898.00
- `actual_time` (hours): Mean = 416.93, Median = 132.00, Std = 598.10, Min = 9.00, Max = 4532.00
- `osrm_time` (hours): Mean = 213.87, Median = 64.00, Std = 308.01, Min = 6.00, Max = 1686.00
- `segment_actual_time` (hours): Mean = 36.20, Median = 29.00, Std = 53.57, Min = -244.00, Max = 3051.00
- `segment_osrm_time` hours: Mean = 18.51, Median = 17.00, Std = 14.78, Min = 0.00, Max = 1611.00

**Distance Variables**
- `actual_distance_to_destination` (km): Mean = 234.07, Median = 66.13, Std = 344.99, Min = 9.00, Max = 1927.45
- `osrm_distance` (km): Mean = 284.77, Median = 78.53, Std = 421.12, Min = 9.01, Max = 2326.20
- `segment_osrm_distance` (km): Mean = 22.83, Median = 23.51, Std = 17.86, Min = 0.00, Max = 2191.40

As can be seen in the histogram plot and descriptive stats, except the segment data, all are highly right-skewed.

**Source and Destination variables**
- `dest_state` is a categorical variable, with the top 5 states being **Maharashtra, Karnataka, Haryana, Tamil Nadu, and Uttar Pradesh**.
- Similarly, `source_state` has **Delhi** in the top 5, excluding Uttar Pradesh.
- It generated a word cloud for `dest_city`, where **Bengaluru, Mumbai, Gurgaon, Delhi,** and **Hyderabad** are prominent.
- For `source_city`, the top 5 are **Bengaluru, Mumbai, Gurgaon, Bhiwandi,** and **Delhi**.
- In the word cloud for `source places`, **Central, Bilaspur** and **Mankoli** are highlighted. ("Central" represents multiple locations across cities and states, so we can exclude it from the top places). The word cloud also includes **Nelamangala, Bommasandra**, and **Kempegowda International Airport**, which had typos in the dataset.
-For `dest_places`, **Bilaspur, Central, Mankoli, Nelamangala**, and **Bommasandra** are highlighted.
We don't have much information about the **source and destination codes**, so we will leave them as they are.

**Trip Creation Time Variables**
- `trip_month`: shows count of months for the unique trips created. September has **13K** trips and october month has **1.7K** trips, here the octomber month count is for first 3 days of month only.
- `trip_hour`: The trip creation time spans throughout the day. However, the number of trips increases during non-working hours and is lower during normal office working hours, which is the reverse of what we would typically expect.
- `trip_day`: The data shows the count of unique trips created each day, but we only have data between **13th September** and **3rd October**.
- `day_of the week`: The highest number of trips was created on **Day 2** (Wednesday), and the lowest number of trips was on **Day 6** (Sunday).

#### Hypothesis Testing:

**Failing to Reject the Null Hypothesis:** The p-value is above the significance level of 0.05, so we fail to reject the null hypothesis in these cases

The Order and Scan total times don't have significant difference in their mean.

This suggests that the **mean** of total time from **Orders** and **Scan** - start to end time is **statistically similar**. This implies that the both time measured are consistent with each other.

**Reject the Null Hypothesis:** The p-value is below the significance level of 0.05, so we reject the null hypothesis in these cases.
There is **significant difference** in **mean** of **Actual and OSRM time**,
also between **Segment's actual and OSRM time**.

**Failing to Reject the Null Hypothesis:** The p-value is above the significance level of 0.05, so we fail to reject the null hypothesis in these cases.
There is **no** significant difference between **Actual and Segment's Actual time**, also between **OSRM and Segment's OSRM** recorded time mean.

**Reject the Null Hypothesis:** The p-value is below the significance level of 0.05, so we reject the null hypothesis in these cases.
There is **significant difference** in **mean** of **Actual and OSRM Distance**,
also between **Segment's actual and OSRM distance**.

**Failing to Reject the Null Hypothesis:** The p-value is above the significance level of 0.05, so we fail to reject the null hypothesis in these cases.
There is **no** significant difference between **OSRM and Segment's OSRM** recorded distance means.

**Insights**
1. System recorded time i.e. order start to end time and delivery recorded time start scan to end scan time are similar.

2. Total or in segments, Actual time taken for delivery is greater than OSRM calculated/predicted time.

3. Actual total and segment time are equal when aggregated. same goes for OSRM. This means the there is no delay in segments for actual and osrm times. And in addition we can say that OSRM predicted times are better at dividing time for each segment.

4. Actual distance covered in a trip is less than OSRM and Segment OSRM distance. This means there are drivers are taking a shorter routes than predicted, either the system is predicting low because of the Optimal routes in maps are different than actual. Or the drives are taking short cuts in between to cut distance

5. osrm and Segment osrm dist are nearly same, this show the predicted value for the whole trip is equal to the sum of predict value of
each segment

6. This means the system is good at dividing the distance for each segment.But this still varies with actual time, so it can pe trained more to be more precise.

7. Earlier we saw that the time taken to reach distance is greater than the predicted. and now we found that actual distance covered is shorter than the predicted.
Either the system is predicting wrong time or the driver is stalling more time.

**Recommendation:**

1. There is need for improvement in open-source routing engine computation.

2. Shortcuts can be harsh on products, as the routes are not well developed and may damage the goods in transport. either make drivers follow the prescribed routes or check system to there are actually shorter routes to destination  

3. The difference is coming between the actual and osrm distance, the system need to have better gps providers for better distance calculation

4. Make system predictions better to calculate time or
improve routes maps and make driver follow them for secured delivery

### Corridors
#### Busiest States
**Insights:**
1. The busiest states in **delivery route type carting** are **Maharashtra** and **Karnataka**, with more than **2000 trip creations** recorded.
  - The **average distance** for trips with **Maharashtra** as the source and destination is nearly **70 kilometers**, and the **time taken** is nearly **180 hours**.
  - For **Karnataka**, the average distance is nearly **60 kilometers**, and the time taken is nearly **150 hours**.

2. The busiest states in **delivery route type FTL** are **Telangana, Delhi,** and **Haryana**, with nearly **500 trips** created.
  - The **average distance** for trips between **Telangana** as the source and destination is nearly **80 kilometers**, and the time taken is nearly **190 hours**.
  - The **average distance** from **Delhi to Haryana** is **180 kilometers**, and the time taken is nearly **360 hours**.

3. When considering **interstate trips**, whether in **FTL** or **carting**, **Delhi and Haryana** are the top states, followed by **Uttar Pradesh**.

**Recommendation:**
1. Focus on improving delivery efficiency by **optimizing routes** in Maharashtra and Karnataka, as these states see the **highest** number of **trips** for the carting route type.
2. For FTL route types, prioritize **better planning and coordination** for Delhi-Haryana and Telangana trips, as they have the **longest average distances and times**.

#### Busiest Cities
**Insights:** (Similar analysis as done above for state)

1. The busiest **city-to-city corridors** for **delivery route type carting** are **Bengaluru**, **Bhiwandi to Mumbai**, and **Hyderabad**.
  - **Intercity Bengaluru** trips are the highest, with more than **1.3k trips**.
  - Next is **Bhiwandi to Mumbai** with **400 trips**, followed by **intercity Hyderabad** with **300 trips**.
  - The **average distance** for Bengaluru trips is **40 km**, for Mumbai it is **30 km**, and for Hyderabad, it is **25 km**. All three cities have the same **average time of 100 hours**.

2. Clearly, **Bengaluru** is contributing the most to Delhivery’s operations.

3. The busiest **city-to-city corridor** for **delivery route type FTL** is from **Delhi to Gurgaon** and **Pune to Bhiwandi**.
  - For **Delhi to Gurgaon**, there are nearly **250 trips** created.
    - The **average distance** between **Delhi and Gurgaon** is **300 km**, with a time of nearly **580 hours**.
  - The **average distance** between **Pune and Bhiwandi** is **150 km**, and the time taken is **300 hours**.

 **Recommendation:**
 1. Scaling up delivery resources such as **fleet size** and **logistics infrastructure** in **Bengaluru** to meet increasing demand and supply needs in this busiest city.
 2. Introducing **better route planning** for **Delhi to Gurgaon** and **Pune to Bhiwandi** routes, as these corridors have significant distances and long delivery times (580 hours and 300 hours, respectively).

#### Top Start to Destination Delhivery 
**Insights:** (Similar analysis as done above for state and city)

1. The busiest **place-to-place delivery** for **route type carting** is between **Nelamangala**, **Kempegowda International Airport**, and **Bommasandra**.
  - Ignoring "Central" as it is a place mentioned for many cities and states.
  - The **average distance** between **Nelamangala** and **Kempegowda International Airport** is **30 km**, with a time of nearly **90 hours**.
  - The **average distance** between **Nelamangala** and **Bommasandra** is **100 km**, taking nearly **210 hours**.
  - The **average distance** between **Bommasandra** and **Kempegowda** is **40 km**, with a time of **110 hours**.

2. The busiest **place-to-place delivery** for **route type FTL** is from **Tathawade to Mankoli**, the return route from **Mankoli to Tathawade**, and **Mehmadpur to Bilaspur**.
  - The **average distance** between **Tathawade and Mankoli** is **150 km**, with a time of **300 hours**.
  - The **average distance** for the **return journey** is **200 km**, with a time of **400 hours**.
  - There seems to be an error in routing between **Tathawade and Mankoli**, as the average distances for the onward and return trips are different.
  - The **average distance** between **Mehmadpur and Bilaspur** is **950 km**, taking **1600 hours**.

**Recommendation:**
1. Given the high demand in places like **Nelamangala, Kempegowda International Airport, and Bommasandra**, consider **expanding warehouse capacity** in these regions by building larger or additional warehouses to better manage the high volume of deliveries.

#### OSRM Compute Difference
**Insights on effectiveness of OSRM Predictions**

1. For **Guwahati to Dankuni**, the system has predicted the distance incorrectly by about **650 km**.
2. For **Bilaspur to Poonamalle**, the difference is about **470 km**, and for **Nelamangala to Transport**, it is **450 km**.

3. The highest time difference between **OSRM predicted** and **actual time** is **3000 hours** for **Guwahati to Dankuni**. The next highest is for **Kotwali to Central of Tulsipur**, with a difference of about **2500 hours**.

4. These higher differences are due to **less data** on those trips

**Recommendations:**
1. The above differences in distance and time suggest that much **improvement is needed** for these **locations**, as the system is unable to predict correctly and may not save time or resources effectively.

2. Gather **more data** on **less frequent routes** and optimize the OSRM calculations for **better prediction**.

## Future work
1. Build forecasting models for trip creation based on dependent variables like state, city or time.
