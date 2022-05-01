# Rossmann stores sales prediction

![rossmann_image](https://miro.medium.com/max/1400/0*IpUXpwNleNMgpPFr.png)
Rossmann is a company that operates over 3000 drug stores in Europe. Rossmann store managers are tasked with predicting their sales up to 6 weeks forward.
In this project, I've collected a large dataset from kaggle that includes sales registers from Rossmann. The dataset is available at https://www.kaggle.com/competitions/rossmann-store-sales/data. I did a model that can predict the drug stores sales
up to 6 weeks. After that, I made a telegram bot that can be used to verify the predicted sales for the choosen store.

## Data Description
There are three files to be used: train.csv, text.csv and store.csv. The dataset has 15 fields, and to recover them all, it is needed to join table 
train.csv to table store.csv. The predictions are made using table test.csv. The fields are: 
- Date - day of sell.
- Day of Week - day of the week.
- Store - a unique Id for each store.
- Sales - the turnover for any given day (this is what you are predicting).
- Customers - the number of customers on a given day.
- Open - an indicator for whether the store was open: 0 = closed, 1 = open.
- StateHoliday - indicates a state holiday. Normally all stores, with few exceptions, are closed on state holidays. Note that all schools are closed on public holidays and weekends. a = public holiday, b = Easter holiday, c = Christmas, 0 = None.
- SchoolHoliday - indicates if the (Store, Date) was affected by the closure of public schools.
- StoreType - differentiates between 4 different store models: a, b, c, d.
- Assortment - describes an assortment level: a = basic, b = extra, c = extended.
- CompetitionDistance - distance in meters to the nearest competitor store.
- CompetitionOpenSince[Month/Year] - gives the approximate year and month of the time the nearest competitor was opened.
- Promo - indicates whether a store is running a promo on that day.
- Promo2 - Promo2 is a continuing and consecutive promotion for some stores: 0 = store is not participating, 1 = store is participating.
- Promo2Since[Year/Week] - describes the year and calendar week when the store started participating in Promo2.
- PromoInterval - describes the consecutive intervals Promo2 is started, naming the months the promotion is started anew. E.g. "Feb,May,Aug,Nov" means each round starts in February, May, August, November of any given year for that store.
## Feature Engineering
After understanding the dataset attributes, it is needed to do some feature engineering to create some features that will help to create the machine learning model.
The following variables were created from the original dataset:
- year, month, day, week_of_year, year_week from Date.
- competition_since from CompetitionOpenSince[Month/Year].
- promo_since from Promo2Since[Year/Week].
- promo_time_week from Date and promo_since.
## Exploratory Data Analysis
### Univariate Analysis
It's important to see how's the distribution of the variables:
#### Response Variable
![Response Variable](/images/variable_response.png)
#### Numerical Variables
![Numerical Variables](/images/numerical_variable.png)
#### Categorical Variables
![Categorical Variables](/images/categorical_varible.png)
### Bivariate Analysis
At this point, some hypothesis were raised and tested with a few variables and the response variable sales:
1. Stores with greater assortment should sell more:                **False** - They actually sells less.
2. Stores with more closer competitors should sell less:           **False** - They actually sells more.
3. Stores with competitors for more time should sell more:         **False** - They actually sells less.
4. Stores with promotions active for longer time should sell more: **False** - They actually sells less.
5. Stores with more consecutive promotions should sell more:       **False** - They actually sells less.
6. Stores open at christimas holiday should sell more:             **False** - They actually sells less compared to other holidays.
7. Stores should sell more over the years:                         **False** - They actually sell less.
8. Stores should sell more at the second semester of the year:     **False** - They actually sell more at the first semester of the year.
9. Stores should sell more after 10th day of each month:           **True**
10. Stores should sell less at weekend:                            **True**
11. Stores should sell less over the school holidays:              **True** - Except July and August.
### Multivariate Analysis
For the last, the correlations between numerical and categorical variables can be analysed.
#### Numerical Variables
![Numerical Variables](/images/numerical_correlation.png)
#### Categorical Correlations
![Categorical Variables](/images/categorical_correlation.png)
## Data Preparation
At this step, the data will be prepared to the model training.
### Rescalling
- Numerical variables competition_distance, competition_time_month, promo_time_week and year had their scale transformed, to assume a most similar behavior to Gaussian distribution.
- Variable response sales had a log transform to assume a better distribution, more centralized.
### Encoding
- Categorical variables as state_holiday, store_type and assortment were encoded to be able to train the model.
### Nature transformation
- Cyclical numerical variables as time measures were transformed to sine and cosine distributions.
## Feature Selection
It was used boruta as method and a RandomForestRegressor to determinate the best features to use to evaluate the machine learning model.
### Features Selected
- store, promo, store_type, assortment, competition_distance, competition_open_since_month, competition_open_since_year, promo2, promo2_since_week,
promo2_since_year, competition_time_month, promo_time_week, day_of_week_sin, day_of_week_cos, month_sin, month_cos, day_sin, day_cos, week_of_year_sin, week_of_year_cos
## Machine Learning Modeling
5 diffente models were trained, and their performance was evaluated by Mean Absolute Error, Mean Absolute Percentage Error and Root Mean Squared Error.

![Models Performance](/images/performance_models.png)

### Cross Validation
It's important to see how's the behavior of the models with different parts of the dataset. For that, it was
used a Cross Validation method to train and validate the models in diferente parts and give us an idea of the real performance of each one.

![Cross Validation](/images/models_performance_cross_validation.png)

### Hyperparameter Fine Tuning
The choosen model was XGBoost regressor. To find the best hyperparameters for the choosen model, it was used the Random Search Method. Some hyperparameters were declared with some values, and the algorithm randomly chooses one value of each to train the model.
#### Final model performance
![Final Model](/images/final_model.png)

## Telegram Bot
Finally, the model was deployd at heroku and it was created an API to connect the prediction model to a Telegram Bot. Giving an ID of a Drug Store to the bot will return the prediction in the next 6 weeks.
![Bot](/images/telegram-bot.png)
