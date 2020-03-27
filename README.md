This README.md lists project members, goals, responsibilities, and a summary of the files in the repository. 

### Project File Summary
* README.md - a summary of all contents in this repository. 
* /Data - dataset file including data cleaning
* /EDA - exploratory data analysis 
* /Modeling - Multivariete regression code 
* /Project_Prompt - the guidelines for this project. 

### Project Members
* Vivian Dang
* Rashid Baset

### Project Scenario
A real estate company, Remax, has hired us to help them better understand factors affecting King County’s house sale prices for additional insights in the market and home price guidance for their clients.  

### Project Goals
- We want to provide inference of home prices in King County to get an understanding of where prices fall in the market. 
- We want to provide inferential insight into the important features that positively affect home prices. 


### Methodology
- Identify business application. 
- Identify target and predictor variables to focus on. 
- Clean data
- Investigate the data by providing Exploratory Data Analysis (EDA).
- Determine criteria for classification.
- Generate multivariete regression model based on criteria to fit the training data. 
- Test the optimized traiing model on the test data and evaluate score. 
- Create a presentation to translate finidngs into actionable insights for the business application. 

### Exploratory Analysis
1.  Price distribution of homes 
2.  Affect of sqft living on price
3.  Affect of grade on price
4.  Grade mean and price

### Multivariable Regression 

Performed a multivariable linear regression model to predict home price (80 variables):

Create a dummy dataframe:

#Define variables for modeling
y = 'price'
x_cols = ['bedrooms', 'bathrooms', 'sqft_living','floors','condition','yr_built', 'zipcode','sqft_living15', 'sqft_lot15', 'sqft basement']

#Define categorical variables
categorical_variables = ['floors', 'condition', 'zipcode']

#create DataFrame
df_ohe = pd.get_dummies(kc_housing[x_cols], columns= categorical_variables, drop_first =True)

Used statsmodel to obtain a model summary with x = df_ohe.values and y = np.log(‘price’) which is log_{e}. From the model, we dropped variables that had a p-value >0.05 because this means that these variables are not statistically significant in our model. 

Used numpy to convert our dummies dataframe and np.log(‘price’) to a numpy array for training. 

Used train_test_split from sklearn to split our data into X_train, X_test, y_train, y_test.
X_train, X_test, y_train, y_test = train_test_split(x_array, y_array_log, test_size=0.2, random_state=50)

Used LinearRegression() to find the best fit line and create a list of 
lr = LinearRegression()
model = lr.fit(X_train, y_train)

# coefficients (m slope)
m = lr.coef_

# y-intercept b
b = lr.intercept_
Perform a cross validation
cv_5_results  = (cross_val_score(lr, X_train, y_train, cv=5,  scoring='r2'))
Create a residual map with yellowbrick
model = LinearRegression()
visualizer = ResidualsPlot(model)

visualizer.fit(X_train, y_train)  # Fit the training data to the visualizer
visualizer.score(X_test, y_test)  # Evaluate the model on the test data
visualizer.show()            

List of variables in our final model: 67 zip codes, sqft_living, sqft_living15, sqft_lot15, sqft basement, bedrooms, bathrooms, floors_1.5, floors_2, floor_3,  and condition 2-5.

Results from model:
The mean of our R Squared values for our cross validation was 0.84 which means that our model explains 84% of the variance 
Factors that cause an increase in house price:
As condition increases, price will also increase
An increase in bathrooms, sqft_living, sqft_living15, sqft_lot15
Having a 1.5 floor
Factors that cause a decrease in house price:
An increase in bedrooms or sqft_basement
Houses with 2 or 3 floors
Located in zip code 98002
Top 3 zip codes with a high price factor:
98039, 98004, 98109

Limitations of our model:
We did not use factors like having a waterfront, quality of view, and year renovated
The top 3 zipcodes are located in areas with a waterfront so waterfront and view rating may have an impact on determining the value of the house

Further Research:
provide additional home quality insights by including:
Waterfront (bin it to a yes or no column)
View (bin it to rating 0-4)
Year renovation (bin year by decade and associate it with a value score)
Attribute a affluency value score using zip codes by finding real estate tax rate 


----------------


King County Dataset: 21597 rows x 21 columns


Categorical variables:
Condition: index 0-5, rating of the house’s condition
Grade: index 1-13, rating of the house’s construction/design 

Continuous variables:
Sqft_livingsquare: footage of the home
sqft_lotsquare: footage of the lot
Sqft_above: square footage of house apart from basement
Sqft_living15:  the square footage of interior housing living space for the nearest 15 neighbors
Sqft_lot15: the square footage of the land lots of the nearest 15 neighbors

Discrete variables:
Bedrooms:  numbers of bedrooms in the house
Bathrooms: number of bathrooms
Floors: total floors (levels) in house

Variables dropped:
Id: unique identifier for a house
Not a feature of the house
Date: house was sold
Not a feature of the house
Lat:  latitude coordinate
Long: longitude coordinate
No meaningful info from one unit increase of lat or long to price
Waterfront: house which has a waterfront
17% nan values
View:  index 0-4, rating of the views from the house
We were not able to find the scale for view to draw meaningful insights
yr_renovated: year when house was renovated
There are 17k numbers of 0, we do not want to drop those rows
We cannot compare 0.0 (true vs false) to years (ex:1965-2015)
We could set yr-renovated to year built for houses that have not been renovated. However, we do not want to assume that a renovated house is the same as a newly built house



