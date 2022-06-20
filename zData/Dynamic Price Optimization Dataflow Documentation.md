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

