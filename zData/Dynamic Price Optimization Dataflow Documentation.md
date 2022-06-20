In this document, we'll try to explain the end-to-end process to get the price recommendations. Primarily it is segregated into two significant steps - 
# Data Processing and Validation
Input data is raw JSON data, one comprises Product details, and the other is sales-related. On each JSON file, we will run a validation check to ensure there isn't any discrepancy in the data and then transform and store it in a usable format(comma-separated file).

## Product Validator Service
The input data is a list of JSON, and each JSON contains four primary keys - ASIN, ProductTitle, ReleaseDate, and ProductGroup. ASIN is a mandatory field, and the rest of the areas are strings. We're following the schema to validate the incoming JSON. We don't require any transformation on this data, we convert the data into a pandas data frame, and from there, we store it as a comma-separated file.
```JSON
schema = {  
    'ASIN': {  
        'type': 'string',  
        'required': True,  
        'maxlength': 10  
    },  
    "ProductTitle": {  
        'type': 'string',  
        'maxlength': 1000  
    },  
    "ReleaseDate": {  
        'type': 'string',  
        'maxlength': 100  
    },  
    "ProductGroup": {  
        'type': 'string',  
        'maxlength': 100  
    }  
}
```

## Order Validator Service
The sales data comes in JSON format, is passed through a validator, and then transformed. The schema of the incoming JSON is as follows - 
```JSON
schema = {  
    "ReportDate": {  
        'type': 'integer',  
        'min': 1577817000000,  
        'max': 1655843622000  
    },  
    "ASIN": {  
        'type': 'string',  
        'required': True,  
        'maxlength': 10  
    },  
    "OrderedRevenue": {  
        'type': 'float',  
        'min': 0  
    },  
    "OrderedUnits": {  
        'type': 'integer',  
        'min': 0  
    },  
    "AvgSalesPrice": {  
        'type': 'float',  
        'min': 1  
    }  
}
```

Order Validation and transformation process is a little complex, so with the help of a tree diagram, we'll try to explain the process - 
![[Blank diagram.jpeg]]


If any lines of the input JSON fail to meet the criterion mentioned in the schema then it will be considered failed data. The rest will be consider for transformation, first we will discard all the rows where we're observing average selling price and sales is less than equal to zero(though this scenario will not arise as we already took out all such cases in the validation part). Then we'll do case by case checks - 
- If there is absolutely no variation in the price for a particular SKU, the we'll discard the entire data for that particular SKU and append it to the failed data.
- If we observe that the number of distinct prices for a particular SKU is less than some threshold(currently 5), in such cases it will dificult to estimate the elasticty of the product, hence we append this data to the failed data.
- If total number of data points is less than some pre-defined threshold, then also we discard this data and append it to the failed data.
- After doin all the above checks, whatever remains is good data and worthy of 

## Dynamic Pricing
The elasticity of a product is defined as $\gamma = \frac{\partial Q / Q}{\partial P / P}$, where $Q$ is the sales quantity and $P$ is the sales price of the product. To simplify the differential equation, we'll take the denominator in the other side and doing integration and little bit of manipulation we can express this equation this way - $Q=CP^\gamma$, where $C$ is the integration constant. Now, let's assume that at $t$-th time point/day sales quantity is $Q_t$ and average selling price is $P_t$. So, $Q_t =  P_t^\gamma$ and $Q_{t-1} =  P_{t-1}^\gamma$, by taking the ration of these two, we can express $Q_t$ as a function of $P_t$, i.e., $Q_t = Q_{t-1} \big( \frac{P_t}{P_{t-1}}\big)^\gamma$. This equation can be used for elasticity estimation.

### Elasticity Estimation
We have historical data to estimate the elasticity of the SKU/product. For ease of the estimation, we'll take log both side of this equation $Q_t = Q_{t-1} \big( \frac{P_t}{P_{t-1}}\big)^\gamma$ and the equation will look like this $log(Q_t/Q_{t-1}) = \gamma \cdot (P_t/P_{t-1})$, this equation is analogous to the linear equation $Y=α X$, which can easily be calculated from the data - 
$$
\begin{aligned}
X &= \{P_i/P_{i-1}\}_{i = 2}^{t-1}\\
Y &= \{Q_i/Q_{i-1}\}_{i = 2}^{t-1}\\
\gamma &= (X^T X)^{-1}X^TY
\end{aligned}
$$
### Demand Forecasting
For demand forecasting we were looking for something in linear form as this will be used to calculate the revenue and eventually it'll be used in Optimisation. We took help of Taylor Series Expansion to linearize the Demand Forecasting Equation, steps are mentioned below - 

$$
\begin{aligned}
Q_t &= Q_{t-1} \big( \frac{P_t}{P_{t-1}}\big)^\gamma \\
&=Q_{t-1} \big( 1 + \frac{P_t - P_{t-1}}{P_{t-1}}\big) ^ \gamma \\
&=Q_{t-1}\big(1 + \gamma \frac{P_t - P_{t-1}}{P_{t-1}} \big) \\
&= (1-\gamma) Q_{t-1} + \gamma\frac{Q_{t-1}}{P_{t-1}} P_t
\end{aligned}
$$

