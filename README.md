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

For each of the top zipcodes in each category, I looked at plots based on the five metrics above. Plots for ROI First Difference and ROI Second Difference showed a marked difference in patterns after 2010. After this date, the frequency of oscillations in first difference increased, as did the variance of second differences. Since dates before 2010 clearly exhibit patterns that are no longer present in the real estate market, I limited my training data to the post-2010 period.

### EDA Result 2: Near Uniformity of ACF and PCF results

ACF and PCF plots for the top zipcodes in each of the four category show a uniform pattern:
- Without differencing, there was a slow decline in autocorrelation through lag 4 or 5 and a diminishing oscillation in partial autocorrelation through lag 4.
- With First differencing, there was autocorrelation through lag 2 and a diminishing oscillation in partial autocorrelation through lag 3 or 4
- Second differencing was most inconsistent, though in almost all cases there was a positive autocorrelation at lag 1 followed by a negative autocorrelation at lag 2. 
- First and second differencing shows consistent negative correlation at lag 12.

These results indicate that to model these zipcodes at least 1 differencing is needed as well as either a second differencing or additional terms at lags 1 and 2, and a seasonal term at lag 12.


### Model Building

I decided that instead of building a different model for each zipcode, I would try to build a single model validated on all of them. The reason I decided to do this was because the ACFs and PCFs of these zipcodes seemed similar enough that a single set of features might be effective in modeling each of them. 

### Model Validation Process

My model validation process was as follows: For each zipcode, I trained the model on the period from April 2010 through April 2017 and recorded the mean standard error for the model's predictions on the period from May 2017-April 2018. In other words, model were specifically tested for their performance over a one year period. 

To evaluate a model's overall performance I took the average  standard error for all twenty zipcodes.

### Baseline Model

I first tested a baseline AR(1) Model. The model's average standard error was 0.14. Error was lowest for the high value zipcodes and highest for the zipcodes with the highest ROI First Difference. 

### Optimizing Features

I next ran through a grid of features to find the model that minimized average standard error for all twenty zipcodes.

I tested all permuations of values of p, d, and q from 0 through 2 and values of ps12 ds12 and qs12 of 0 and 1. This meant testing 216 total models on twenty different zipcodes. 

The best performing model had values (1, 1, 2)x(1, 0, 1, 12) and yielded an average Standard Error of 0.11. As with the baseline model, errors were lowest for the high value zipcodes and highest for the high First Difference zipcodes. However, error improved drastically for the high first difference zipcodes, while increasingly slightly for the high value zipcodes.

### A Universal Model?

The generally low level of error for this set of features raised the possibility that these features could be used to effectively model ROI for any zip code. If this is the case, then we could train this model on every zipcode in the country, use each trained model to make a one year-out prediction and determine which zipcodes have the best prediction. 

### Testing the universal model.

Since I wanted to select zipcodes that balanced high predictions with low risk, I tested the SARIMA model by outputting the lower boundary of the model's 95% confidence interval for the last predicted date for each zipcode. 

This process took several hours, but ultimately output a list of predicted means and lower confidence intervals, of which I selected the top 5 for further analysis. Overall, errors were not drastically different from the errors that we already got for other zipcodes, though they were higher than the errors found for highest value zipcodes. 

### Can the Model be Trusted?

Because these five zipcodes were picked out specifically for their high performance on the "universal model", we need to determine whether their high predicted ROI is the result of selection bias or whether these zipcodes would be predicted to have high ROIs by models with features customized specifically to them. 


