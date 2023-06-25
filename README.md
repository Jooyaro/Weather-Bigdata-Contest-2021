# Weather Bigdata Contest 2021

### A contest description
The Weather Big Data Contest is an annual competition organized by the South Korea Meteorological Administration. It presents objectives related to weather data and challenges participants to solve problems in both the public and private sectors. Participants can choose either sector to participate in. In 2021, the contest required participants to predict landslide occurrences within 24 and 48 hours based on both provided and external data regarding the Gyeongsang area in South Korea. Using data from 2011-2019, participants were expected to predict landslide occurrences in 2020. Our team won 2nd place and a special award in the Public Cooperation sector of the contest. The repository contains the related codes and results.

### Objective: Prediction of landslide occurrences within 24 and 48 hours regarding the Gyeongsang area in South Korea.

### Introduction
Due to recent global warming, the frequency of typhoons and heavy localized rainfall has increased in South Korea. The intensity of localized heavy rainfall has also grown, resulting in significant increases in hourly, daily, and consecutive rainfall amounts. Taking the year 2020 as an example, the monsoon season in the central region lasted for 54 days, making it the year with the highest recorded rainfall in the past decade. The increase in rainfall is expected to have a considerable impact on South Korea, where approximately 65% of the land area consists of mountainous terrain with significant elevation changes, leading to floods and landslides.

Landslide prediction information is categorized into forecasts and warnings based on the analysis of past landslide occurrences in each region. The amount of water in the soil is determined by the soil moisture index, which is calculated using historical data. Based on this index, landslide alerts and warnings are issued. A landslide alert is issued when the regional soil moisture index reaches 80%, and a landslide warning is issued when it reaches 100%. The Gyeongsang Province, where landslides frequently occur, is divided into three regions.

Currently, the landslide prediction information provided by the Korea Forest Service categorizes the entire country into 11 regions, which has limitations in predicting localized landslides. Therefore, there is a need to implement a landslide prediction information system specific to the Gyeongsang Province, incorporating regional rainfall and soil characteristics using statistical and machine learning models.


### Data (Total: 2,326,961 x 201)
- Row
  - 3,653 days(2011-2020) * 637 umd-s
- Column
  - Eight weather-related variables(four variables regarding short-term real-time observations, four variables regarding short-term forecat)
  - 193 geographic-related variables(97 variables related to the soil map, 90 variables related to the forestry map, six variables related to the soil drain grade)
- Type
  - Landslide records: # of occurrence, the magnitude of landslides for towns in Gyeongsang provinces
  - Rainfall(External cause): short-term real-time observations(precipitation) - daily precipitation, max precipitation intensity per hour, precipitation amount from the day before, precipitation amount from two days before, short-term forecast from 2011 to 2020(Precipitation probability, six-hour precipitation) of Gyeongsang provinces -> Generated variables: precipitation probability in 24hours, precipitation probability in 48hours, precipitation amount in 24hours, precipitation amount in 48hours
  - Geographic and geological data(Internal cause) - Soil map, Forestry map, Soil drain grade


### EDA for rainfall

<p align="center">
  <img width="400" height="300" alt="Screenshot 2023-06-25 at 11 05 53 AM" src="https://github.com/Jooyaro/Weather-Bigdata-Contest-2021/assets/35860986/5bd243ef-96b6-426c-9f46-4c7b6c76ab76">
</p>

Landslides primarily occur during periods of heavy rainfall, resulting in a higher concentration of occurrences during the summer. Over a span of nine years, there were a total of 359 occurrences, indicating a relatively low occurrence rate. Over a period of nine years, landslides occurred on 11 unique dates out of a total of 3,287 days. The landslides occurred in 243 out of 637 unique regions, indicating a concentration in a limited number of dates and a narrow range of areas.

![Screenshot 2023-06-24 at 10 04 40 PM](https://github.com/Jooyaro/Weather-Bigdata-Contest-2021/assets/35860986/0625b954-30ec-489f-8405-11d185b4fec9)

When comparing the variables related to max precipitation intensity per hour(daymax_rain), daily precipitation(day_rain), precipitation amount from the day before(onedayago_rain), and precipitation amount from two days before(twodaysago_rain) based on the occurrence of landslides, it can be observed that the values of these variables are higher when landslides occur compared to when they do not. Therefore, it can be anticipated that the 6-hour precipitation data from short-term forecasts will be crucial.

### EDA for geographic data
Please refer to the corresponding file in the repository.


### Modeling
- Handling missing values

<img width="408" alt="Screenshot 2023-06-24 at 10 55 30 PM" src="https://github.com/Jooyaro/Weather-Bigdata-Contest-2021/assets/35860986/9a9381a9-22d9-4366-be53-cbe82b4ff16f">

In the final dataset, missing values occurred in a total of five variables. Assuming the missing data pattern follows the MAR(Missing At Random) assumption, where the missing pattern is not dependent on the missing values themselves, the missing values were imputed using the MICE package in R.

- Undersampling

In the final dataset our team built, out of a total of 2,093,819 rows, there are 359 occurrences where landslides actually occurred on specific dates and towns. The data is highly imbalanced with a ratio of 5,832:1 between the occurrences of 0(no occurrence of landslide) and 1(yes occurrence of landslide). If all predictions are made as 0 in order to fit the data to the model, the accuracy would be close to 1, but the recall would be close to 0. To prevent this, two evaluation metrics were chosen for the model:
  - Accuracy: It compares the number of correct predictions (both occurrence and non-occurrence) to the total number of occurrences, representing the proportion of correct predictions out of the total.
  - Critical Success Index(CSI): It compares the number of correct predictions related to occurrences with the total sum of occurrences in both the predicted and actual occurrences.

Regarding the data used for the model, as the number of observations in the majority class(0) is 2,093,460, which is too large to oversample, we decided to use all 359 rows of data from the majority class(1) and perform undersampling on a portion of the data from the majority class(0). Undersampling can be done using the RandomUnderSampler function from the imbleran package in Python, but in this model, we used stratified sampling based on the date (month) as the criterion.

- Modeling
  
We compared ML/DL/statistical models for classification. During this process, we performed undersampling by using 1% of the observations from the majority class(0). The undersampled dataset consisted of 21,294 observations, including 20,935 observations from class 0 and 359 observations from class 1. We divided the data into training and test sets in a 7:3 ratio to model the prediction of 'occurrence within 24-hour' and 'occurrence within 48-hour', separately. We compared six models based on accuracy and critical success index (CSI).

<img width="950" alt="Screenshot 2023-06-24 at 11 17 43 PM" src="https://github.com/Jooyaro/Weather-Bigdata-Contest-2021/assets/35860986/0e577e11-f6d6-4d76-bfae-66fbbe66f480">

Both the models for predicting the occurrence within 1 day and 2 days showed higher accuracy and CSI when using the LightGBM model. Therefore, we used this model for predicting landslide occurrences in 2020. However, the accuracy calculation showed a very low value of 90.38%, and the CSI was 14.33%. This is mainly due to the high number of landslide occurrences in 2020. When examining the number of landslide forecasts issued by the Korea Forest Service's Landslide Information System, it was found that the number of forecasts was below 100 until 2019, but it significantly increased to 154 in 2020. Therefore, we applied a statistically robust logistic regression model.

Out of the 201 collected variables, we performed LASSO logistic regression analysis with an L1 penalty to estimate non-significant variables as 0 or coefficients close to 0. This allowed us to perform the analysis with only a subset of variables. To confirm significant variables through coefficient comparison, we performed min-max normalization for each variable and then conducted the modeling. We trained the LASSO logistic regression model using data from 2011-2019, resulting in 105,032*201 and 42,229*201 observations. Since logistic regression returns probability values, we set the cut-off threshold at 0.5 to distinguish between 0 and 1.

### Result
<img width="834" alt="Screenshot 2023-06-24 at 11 23 30 PM" src="https://github.com/Jooyaro/Weather-Bigdata-Contest-2021/assets/35860986/5e63d0b7-2444-45da-9ddc-4655ade3ffa0">

As a result, out of the initial 201 variables, 8 variables were selected for each model to predict landslide occurrences within 1 day and 2 days, respectively:
  - 1-day model: max_rain, day_rain, prob24, prob48, LOCTN_ALTT_max, CLZN_CD3, TPGRP_TPCD12, SCSTX_CD3
  - 2-day model: sixhour48, prob24, prob48, LOCTN_ALTT_max, CLZN_CD2, CLZN_CD3, TPGRP_TPCD12, KORTR_GROU39

<img width="761" alt="Screenshot 2023-06-24 at 11 34 39 PM" src="https://github.com/Jooyaro/Weather-Bigdata-Contest-2021/assets/35860986/b8b8e682-2e3a-42c5-96ec-10894d5069c5">

As seen in the preceding table, both models showed that the most significant variables are related to weather. Regarding terrain variables, higher elevation was associated with a higher probability of landslide occurrences, and in the case of the southern region, including Gyeongsang area, the probability of landslide occurrences was found to be higher.

In the model predicting landslide occurrences 1 day ahead, the coefficient of the variable "day_rain," representing precipitation from the previous day, had the highest magnitude (18.5278). The variable "prob24," indicating the probability of rainfall in the next 24 hours, showed a positive correlation, while the variable "prob48," representing the probability of rainfall in the next 48 hours, showed a negative correlation. This implies that a higher probability of rainfall 48 hours ahead reduces the likelihood of landslide occurrences 1 day later. Additionally, when the soil texture corresponds to silty loam(SCSTX_CD3), indicating low permeability, the possibility of landslide occurrences is somewhat reduced.

In the model predicting landslide occurrences 2 days ahead, the variable "sixhour48," representing accumulated rainfall in the previous 48 hours, showed a positive correlation. Areas with planted fruit trees (KOFTR_GROU39), which are susceptible to soil erosion, had a higher probability of landslide occurrences.


### Discussion
The current landslide prediction system provided by the Korea Forest Service divides the country into 11 regions(3 regions in the case of the Gyeongsang area) uniformly, which limits its ability to predict landslides occurring at a local level. In response to this limitation, our team proposes a Landslide Prediction Information System for the Gyeongsang region, incorporating regional rainfall and soil characteristics, which offers the advantage of predicting landslide occurrences at the town or district level.

This system takes into account the specific rainfall and soil characteristics of each area in the Gyeongsang region, allowing for more localized and accurate landslide predictions.






