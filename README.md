# Real Estate Investment - Finding Markets with High Short Term Growth.

In this project, I use SARIMA modeling to predict high growth zipcodes for the period from April 2018-April 2019. The metric I use to determine high growth is the predicted year over year return on investment for a zipcode at the end of that period.

## The Data

The dataset I used for this project included median home prices for all zipcodes in the US between 1996 and 2018 - 14,723 zipcodes. Because around a thousand of these zipcodes did not have information for this entire period, I eliminated them from the dataset.

### Feature Engineering

In order to model the data, I engineered three features.

- Year over Year Return on Investment (ROI): calculated as (Current Month Median Home Price - Median Home Price 1 year ago)/(Median Home Price 1 year ago)

- ROI First Difference: Current Month ROI - Last Month ROI

- ROI Second Difference: Current Month First Difference - Last Month First Difference

I then detrended each of these features by taking the value of each feature for each zip code and subtracting from that value the value for the US as a whole. 

### Selecting zipcodes for training

I selected 20 zipcodes for training:
1. Top 5 valued zipcodes by highest 12 month rolling average
2. Top 5 ROIs by highest 12 month rolling average
3. Top 5 ROI First Difference by highest 12 month rolling average
4. Top 5 ROI Second Difference by highest 12 month rolling average

### EDA Result 1: Limit Training Data to post 2010

For each of the top zipcodes in each category, I looked at charts based on the five metrics above. Charts for ROI First Difference and ROI Second Difference showed a marked difference in patterns after 2010. After this date, the frequency of oscillations in first difference increased, as did the variance of second differences. Since dates before 2010 clearly exhibit patterns that are no longer present in the real estate market, I limited my training data to the post-2010 period.
