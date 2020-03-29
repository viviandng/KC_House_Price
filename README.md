# King County House Sale Prices 

Contributors: Vivian Dang & Rashid Baset
    
![house.png](https://github.com/viviandng/flatiron-project-2/blob/master/images/house.png)

### Items in Repository:
- kc_house_data_csv: King County house dataset 
- Kc_housing Notebook.ipynb: code for data processing and visualization
- Kc_House.pdf: presentation slides
- images: folder for visualizations


### Prompt:
A real estate company, Remax, has hired our team to help them better understand factors affecting King County’s house sale prices for additional insights in the market and home price guidance for their clients.

### Key Questions:

1.	How much are houses selling for in King County?
2.	How do features of a house affect its price?
3.	How does the location of a house affect its price?

### Data Collection

King County House Price Dataset: https://github.com/viviandng/flatiron-project-2/blob/master/kc_house_data.csv


#### Data description:
King County Dataset: 21597 rows x 21 columns
- Categorical variables:
    - Condition: index 0-5, rating of the house’s condition
    - Grade: index 1-13, rating of the house’s construction/design 

- Continuous variables:
    - Sqft_livingsquare: square footage of house's living space
    - sqft_lotsquare: square footage of lot
    - Sqft_above: square footage of house excluding basement
    - Sqft_living15: square footage of living space for the nearest 15 neighbors
    - Sqft_lot15: square footage of lot for the nearest 15 neighbors

- Discrete variables:
    - Bedrooms:  numbers of bedrooms
    - Bathrooms: number of bathrooms
    - Floors: number of floors (levels) in house

- Variables dropped & reasonings:
    - Id: unique identifier for a house
        - Not a feature of the house
    - Date: house was sold
        - Not a feature of the house
    - Lat: latitude coordinate
    - Long: longitude coordinate
        - No meaningful information from one unit increase of lat or long to price
    - Waterfront: house which has a waterfront
        - 17% non numerical values
    - View:  index 0-4, rating of the views from the house
        - We were not able to find the grading scale for view to analyze it
    - yr_renovated: year when house was renovated
        - 17,000 rows with 0.0 as values, we do not want to drop those rows
        - We cannot compare 0.0 (true vs false) to years (ex:1965-2015)
        - We could set yr_renovated to year built for houses that have not been renovated. However, we do not want to assume that a renovated house is the same as a newly built house

### Exploratory Data Analysis: 

**Figure 1. Price distribution of houses**

- Prices are skewed to the right, meaning that there are outliers in the higher price range. These outliers range from \$2-8M, causing our mean (\$540k) to be \$100,000 more than our median (\$450k). 50% of house sale prices are between \$322K and \$645K. <br/>
![house_price_dist.png](https://github.com/viviandng/flatiron-project-2/blob/master/images/Fig1:%20house_price_dist.png) 
<br/>

**Figure 2: Square footage vs. Price of Houses**
- Square footage (sqft) shows a positive correlation with price; for each unit increase sqft, there is also an increase in the value of the house. Majority of the houses are under 4,000sqft and under \$2M <br/>
![sqft_price.png](https://github.com/viviandng/flatiron-project-2/blob/master/images/Fig2:%20sqft_price.png)
<br/>

**Figure 3: Grade vs. Price of House** 
- Grade shows a positive correlation with price; houses with a higher grade tends to cost more. Houses with grade equal to or less than 6 do not have many outliers in the higher price range because 6 is the lowest grade that meets building code. These houses have simple construction and design so it is difficult for them to be priced higher.<br/>
![Grade_price.png](https://github.com/viviandng/flatiron-project-2/blob/master/images/Fig3:%20grade_price.png)
<br/>

**Figure 4: Grade vs. Average Price of House**
- On average, houses under grade 10 will be valued at less than \$1M. Whereas, houses with grade 13 are significantly higher, with an average value over \$3.5M.<br/>
![Grade_avgPrice.png](https://github.com/viviandng/flatiron-project-2/blob/master/images/Fig4:%20grade_averageprice.png)



### Multivariate Linear Regression Model: 
A multivariate linear regression model was perfromed to predict house price with 80 variables:


1. Create a dummy dataframe:
        
        Set variables for modeling 
        y = 'price' 
        x_cols = ['bedrooms', 'bathrooms', 'sqft_living','floors','condition','yr_built', 'zipcode','sqft_living15', 'sqft_lot15', 'sqft basement']
        categorical_variables = ['floors', 'condition', 'zipcode']
    
        Create a DataFrame 
        df_ohe = pd.get_dummies(kc_housing[x_cols], columns= categorical_variables, drop_first =True)

2. Use statsmodel to obtain a model summary with x = df_ohe.values and y = np.log(‘price’) which is log_{e}. From the model, drop variables with a p-value >0.05 because these variables are not statistically significant in our model. 

3. Use numpy to convert our dummies dataframe (df_ohe) to a numpy array for training

4. Use train_test_split from sklearn to split our data into X_train, X_test, y_train, y_test. <br/>
        
    
    X_train, X_test, y_train, y_test = train_test_split(x_array, y_array_log, test_size=0.2, random_state=50)
   
5. Use LinearRegression() to find the best fit line and create a list of coefficients <br/>
        
        lr = LinearRegression() 
        model = lr.fit(X_train, y_train) 
    
        coefficients (m slope)
        m = lr.coef_ 
    
        y-intercept 
        b = lr.intercept_ 
    
6. Perform a cross validation <br/>
        
    
        cv_5_results  = (cross_val_score(lr, X_train, y_train, cv=5,  scoring='r2'))
    
7. Create a residual map with yellowbrick <br/>
        
        model = LinearRegression() 
        
        visualizer = ResidualsPlot(model)
        visualizer.fit(X_train, y_train)  
        
        visualizer.score(X_test, y_test)  
        visualizer.show() 
    

Variables in our final model: 67 zip codes, sqft_living, sqft_living15, sqft_lot15, sqft basement, bedrooms, bathrooms, floors_1.5, floors_2, floor_3,  and condition 2-5. <br/>

**Results from model:** 

- The mean of our R-squared values for our cross validation was 0.84; our model explains 84\% of the variance. 
- Factors that caused an increase in house price:
  - An increase in number of bathrooms, sqft_living, sqft_living15, sqft_lot15, conditions (2-5)
  - Having a 1.5 floor 
   - Factors that caused a decrease in house price:
        - An increase in bedrooms or sqft_basement
        - Houses with 2 or 3 floors
- Location has a high impact on house price:
  - Top 3 zip codes with a high price factor: 98039, 98004, 98109 <br/>
     ![zipcodepricepredictor.png](https://github.com/viviandng/flatiron-project-2/blob/master/images/zipcodepricepredictor.png) 
     <br/><br/><br/>

**Figure 5: Price factors in descending order for each variable in the model.** 

- Variables with a price factor less than 1 means that the variable decreases the price of a house when in consideration with all variables <br/> 
  ![variables_pricefactor.png](https://github.com/viviandng/flatiron-project-2/blob/master/images/Fig5:%20variable_pricefactor.png) 


### Limitations & Further Research
We did not use factors like having a waterfront, quality of view, and year renovated. The top 3 zipcodes are located in areas with a waterfront so waterfront and view rating may have an impact on determining the value of the house <br/>

**Further Research:** 
- provide additional home quality insights by including:
    - Waterfront (bin it to a yes or no column)
    - View (bin it to rating 0-4)
    - Year renovation (bin year by decade and associate it with a value score)
    - Attribute a affluency value (tax rate) to zip codes 

