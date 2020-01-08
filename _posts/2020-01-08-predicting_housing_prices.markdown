---
layout: post
title:      "Predicting Housing Prices"
date:       2020-01-08 17:28:21 +0000
permalink:  predicting_housing_prices
---


For my Module 1 Project, my business idea was to create a website that predicts prices of homes in King County, Washington.  Anyone interested in a price of a home can input information about that home into the website.  The website then outputs a predicted selling price of the home with the supplied specifications, such as number of floors or if it has a waterfront view.

Multiple linear regression was used in creating the predictive model from which the pricing predictions are made.

Starting from the given data set, my first step was to import relevant libraries, such as pandas for data manipulation and matplotlib for analysis and plotting.  From there, I followed a typical multiple linear regression workflow, beginning with data cleaning and imputing values for null entries.  I found that loc indexing proved very useful here.  The following code snippet is an example of such indexing.  Basically, some data set entries were missing a value for square footage of the basement.  Well, it makes sense to think that square footage of the basement (`sqft_basement`) is equal to square footage of the total living space (`sqft_living`) minus square footage of the living space above the basement (`sqft_above`).  So the following code - which makes use of loc indexing - was implemented in order to replace missing data entries with a data entry that is likely the true value of the missing entry:  

```
df.loc[df['sqft_basement'] == '?', 'sqft_basement'] = (df['sqft_living'] - df['sqft_above'])
```

After cleaning the data, the next step was identifying categorical predictors and making dummy variables for them.  Then, I removed variables exhibiting collinearity through, among other techniques, implementation of a correlation matrix.  The correlation matrix boiled the question of whether variables were collinear down to whether the absolute value of two variable's correlation coefficient was above a certain threshold.

From there, I began an iterative process of model creation, where the model was trained with a training portion of the data, and tested with a testing portion of the data.  A preliminary model was built, and then successive models were built after particular steps were taken, such as stepwise selection (to remove predictors with p-values greater than .05) and log-transformations.  Log-transformations, and particularly log-transformations of the dependent variable - `price` - proved useful in raising the adjusted R-squared value of models.  

Once the final model was built, analysis was done on the results of the model.  From the analysis, business recommendations were able to be made.

A key finding was that the residual between the atual price and the predicted price is smaller when the actual price is a smaller value.  This points to the idea of heteroskedasticity being present in the model.  With that said, the presence of some heteroskedasticity is possible when the variance of the dependent variable is so high - the lowest-priced house in the data set was $78,000, while the highest priced house was $7,700,000.

This key finding led to the idea that the price-predicting website should focus on predicting the sale price of lower-priced homes, where the model's residual is lower.  

In the end, the threshold of which houses to predict was put at houses expected to sell for under $320,000.  For these houses, the average error between the actual sale price of the house and the predicted model was about $41,900.  

Interestingly, if the threshold were $300,000, the average error of predictions would be about $41,350.  At the same time, about 20.5% of homes in the data set fit into the classification of selling for under $300,000, while almost 24.4% of homes fit into the classification of selling for under $320,000.  In other words, the potential number of users to use the website can grow dramatically with only a small tradeoff in error.  

King County is the 12th most populous county in the US according to the July 1, 2018 official estimate.  King County has a population of over 2.2 million people.  In the US, there are 45 counties with over 1 million people.  Taking these numbers into consideration, and the fact that our website could serve roughly 25% of people interested in the price of a home (this value estimation is based off of fact that 24.4% of homes in the given dataset fit into the model that the website would use), this website could potentially see scores of thousands of users if explanded o predict housing prices for other counties as well.

For future work, there are a few things I would like to do or look at that would likely help out the website:
1. Refine the final model to create better estimates (as the better the estimate, the more likely it is that the users of the website will like their experience).
2. Try out different modeling techniques - ones different from multiple linear regression.
3. Model housing prices of other zipcodes and counties. 
4. Inspect the trend in the residual to actual price ratio as prices go up.  This is important beause, although average error for the model tends to go up as price goes up, perhaps its the case that the ratio of average error vs. actual price goes down as actual price goes up.  If this is the case, then our model would actually be, arguably, better at modeling and predicting the price of more expensive homes than it is at modeling and predicting the price less expensive homes.  To expand on this even more, remember that, as stated earlier, about 24.4% of homes in the King County housing data set are under $320,000.  Well, 40.9% of homes are under $400,000, 58.1% of homes are under $500,000, and 70.5% of homes are under $600,000.  If we could add people interested in homes with prices that are higher than the previously-mentioned $320,000 threshold, we could potentially grow the number of website-users by 2 times the amount of users that the website is currently projected to get, 3 times, or even more. 

