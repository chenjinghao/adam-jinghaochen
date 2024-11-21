# day 81_Multivariable_regression and Valuation Model

[Multivariable_Regression_and_Valuation_Model_(start).ipynb](Multivariable_Regression_and_Valuation_Model_(start).ipynb)

[Multivariable_Regression_and_Valuation_Model_(start).html](Multivariable_Regression_and_Valuation_Model_(start).html)

- Study Notes
    - load necessary packages
        1. pandas, 
        2. numpy, 
        3. seaborn, 
        4. plotly.express, 
        5. matplotlib.pyplot, 
        6. sklearn.linear_model - linearregression
        7. sklean.model_selection - train_test_split
    1. define display format
        1. pd.options.display.float_format
    2. load the data
        1. pd.read_cvs
    3. preview the data
        1. shape
        2. colums()
        3. count()
        4. info()
        5. isna().values.any(
        6. duplicated().values.any()
        7. describe()
    4. visualized the date
        1. bar chart to view the distribution of house price, distance to employment, number of room, access to highways…..
    5. understand the data
        1. correlations between different columns (e.g. room and house price) 
            1. sns.pairplot()
            2. sns.joinplot()
    6. split training & test dataset
        1. price as target
        2. the rest of column is feature
        
        ```python
        target = data['PRICE']
        features = data.drop('PRICE', axis=1)
        ```
        
        ```python
        
        X_train, X_test, y_train, y_test = train_test_split(features, 
                                                            target, 
                                                            test_size=0.2, 
                                                            random_state=10)
        ```
        
        1. X_train, X_test → split the rest of column
        2. y_train, y_test → split the price
        
        7. multivariable regression
        
        r-squared
        
        ```python
        regr = LinearRegression()
        #activate the function
        regr.fit(X_train, y_train)
        #fill the data
        rsquared = regr.score(X_train, y_train)
        #calculate the r-squared
        
        print(f'Training data r-squared: {rsquared:.2}')
        #Training data r-squared: 0.75
        ```
        
        coefficients of model
        
        ```python
        regr_coef = pd.DataFrame(data=regr.coef_, index=X_train.columns, columns=['Coefficient'])
        regr_coef
        ```
        
        1. analyse the estimated values & regression residuals
            1. 
            
            ```markdown
            How good our regression is depends not only on the r-squared. 
            It also depends on the **residuals** - 
            the difference between the model's predictions ($\hat y_i$) 
            and the true values ($y_i$) inside `y_train`.
            ```
            
            ```python
            predicted_vals = regr.predict(X_train)
            residuals = (y_train - predicted_vals)
            
            # Original Regression of Actual vs. Predicted Prices
            plt.figure(dpi=100)
            plt.scatter(x=y_train, y=predicted_vals, c='indigo', alpha=0.6)
            plt.plot(y_train, y_train, color='cyan')
            plt.title(f'Actual vs Predicted Prices: $y _i$ vs $\hat y_i$', fontsize=17)
            plt.xlabel('Actual prices 000s $y _i$', fontsize=14)
            plt.ylabel('Prediced prices 000s $\hat y _i$', fontsize=14)
            plt.show()
            
            # Residuals vs Predicted values
            plt.figure(dpi=100)
            plt.scatter(x=predicted_vals, y=residuals, c='indigo', alpha=0.6)
            plt.title('Residuals vs Predicted Values', fontsize=17)
            plt.xlabel('Predicted Prices $\hat y _i$', fontsize=14)
            plt.ylabel('Residuals', fontsize=14)
            plt.show()
            ```
            
            ```markdown
            Why do we want to look at the residuals? We want to check that they look random. Why? The residuals represent the errors of our model. If there's a pattern in our errors, then our model has a systematic bias.
            
            We can analyse the distribution of the residuals. In particular, we're interested in the **skew** and the **mean**.
            ```
            
            residuals’  skewness of 1.46 → room to improve
            
            solution: 
            
            log the price
            
            ```markdown
            y_log = np.log(data['PRICE'])
            sns.displot(y_log, kde=True)
            plt.title(f'Log Prices. Skew is {y_log.skew():.3}')
            plt.show()
            ```
            
            Then do it all over again!
            
            ```python
            new_target = np.log(data['PRICE']) # Use log prices
            features = data.drop('PRICE', axis=1)
            
            X_train, X_test, log_y_train, log_y_test = train_test_split(features, 
                                                                new_target, 
                                                                test_size=0.2, 
                                                                random_state=10)
            
            log_regr = LinearRegression()
            log_regr.fit(X_train, log_y_train)
            log_rsquared = log_regr.score(X_train, log_y_train)
            
            log_predictions = log_regr.predict(X_train)
            log_residuals = (log_y_train - log_predictions)
            
            print(f'Training data r-squared: {log_rsquared:.2}')
            ```
            
            and now the residual skewness is 0.09
            
    7. predict a property’s value using the regression coefficients
        1. 
        
        ```python
        # Starting Point: Average Values in the Dataset
        features = data.drop(['PRICE'], axis=1)
        average_vals = features.mean().values
        property_stats = pd.DataFrame(data=average_vals.reshape(1, len(features.columns)), 
                                      columns=features.columns)
        property_stats
        
        # Make prediction
        log_estimate = log_regr.predict(property_stats)[0]
        print(f'The log price estimate is ${log_estimate:.3}')
        
        # Convert Log Prices to Acutal Dollar Values
        dollar_est = np.e**log_estimate * 1000
        # or use
        dollar_est = np.exp(log_estimate) * 1000
        print(f'The property is estimated to be worth ${dollar_est:.6}')
        ```