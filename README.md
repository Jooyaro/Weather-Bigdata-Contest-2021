# Weather-Bigdata-Contest-2021

## Topic: Prediction of landslide occurrences 24 hours/48 hours from the given date in the respective region of Gyeongsang area in South Korea.

(The Weather Big Data Contest is an annual big data contest organized by the South Korea Meteorological Administration. Our team won 2nd place and a special award in the Public Cooperation category of the contest. The related codes and results are presented in this repository.)


### Introduction
Due to recent global warming, the frequency of typhoons and heavy localized rainfall has increased in South Korea. The intensity of localized heavy rainfall has also grown, resulting in significant increases in hourly, daily, and consecutive rainfall amounts. Taking the year 2020 as an example, the monsoon season in the central region lasted for 54 days, making it the year with the highest recorded rainfall in the past decade. The increase in rainfall is expected to have a considerable impact on South Korea, where approximately 65% of the land area consists of mountainous terrain with significant elevation changes, leading to floods and landslides.

Landslide prediction information is categorized into forecasts and warnings based on the analysis of past landslide occurrences in each region. The amount of water in the soil is determined by the soil moisture index, which is calculated using historical data. Based on this index, landslide alerts and warnings are issued. A landslide alert is issued when the regional soil moisture index reaches 80%, and a landslide warning is issued when it reaches 100%. The Gyeongsang Province, where landslides frequently occur, is divided into three regions.

Currently, the landslide prediction information provided by the Korea Forest Service categorizes the entire country into 11 regions, which has limitations in predicting localized landslides. Therefore, there is a need to implement a landslide prediction information system specific to the Gyeongsang Province, incorporating regional rainfall and soil characteristics using statistical and machine learning models.


### Data (Total: 2,326,961 x 201)
- Row: 3,653 days(2011-2020) * 637 umd-s
- Column: 8 weather variables(초단기실황 4개, 단기예보 4개) + 193 geographic variables(97 from soil map, 90 from forestry map, 6 from soil drain grade)
- landslide records: 초단기실황 (강수) - 일강수량, 일일시간당최대강수량, 1일전 일강수량, 2일전 일강수량, 단기예보 from 2011-2020 (강수확률, 6시간 강수량) of Gyeongsang provinces --> Generate variables: 24시간뒤강수확률, 48시간뒤강수확률, 24시간뒤강수량, 48시간뒤강수량
- rainfall(External cause)
- geographic and geological data(Internal cause) - 토양도, 임상도, 배수등급


### EDA

![image](https://github.com/Jooyaro/Weather-Bigdata-Contest-2021/assets/35860986/cc64078e-677d-4332-867c-289ee936ef7a)
산사태는 주로 비가 많이 오는 시기에 집중하여 발생하기 때문에 발생건수가 여름에 집중되어 있으며, 9년간 총 359건으로 발생건수가 많지 않다.

9년 간 산사태가 발생한 고유 날짜는 3,287일 중 11일, 산사태가 발생한 고유 지역은 637개 지역 중 243개 지역으로 적은 날짜에 좁은 지역으로 편중되어 있다.




### Modeling
- Handling missing data
- Undersampling
- Model comparison

### Conclusion
