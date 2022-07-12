In this document, we'll try to explain the end-to-end process to get the price recommendations. Primarily it is segregated into two significant steps - 
# Data Processing and Validation
Input data is raw JSON data, one comprises Product details, and the other is sales-related. On each JSON file, we will run a validation check to ensure there isn't any discrepancy in the data and then transform and store it in a usable format(comma-separated file).

## Product Validator Service
The input data is a list of JSON, and each JSON contains four primary keys - ASIN, ProductTitle, ReleaseDate, and ProductGroup. ASIN is a mandatory field, and the rest of the areas are strings. We're following the schema to validate the incoming JSON. We don't require any transformation on this data, we convert the data into a pandas data frame, and from there, we store it as a comma-separated file.
```JSON
schema = {  
    "ASIN": {  
        "type": "string",
        "required": true,  
        "maxlength": 10  
    },  
    "ProductTitle": {  
        "type": "string",
        "maxlength": 1000  
    },  
    "ReleaseDate": {  
        "type": "string",  
        "maxlength": 100  
    },  
    "ProductGroup": {  
        "type": "string",  
        "maxlength": 100  
    }  
}
```

For the time being, we're not doing any transformation. Still, in the future, we can use the title of the products to cluster similar products, which will eventually help us recommend prices.

## Order Validator Service
The sales data comes in JSON format, is passed through a validator, and then transformed. The schema of the incoming JSON is as follows - 
```JSON
schema = {  
    "ReportDate": {  
        "type": "integer",  
        "min": 1577817000000,  
        "max": 1655843622000  
    },  
    "ASIN": {  
        "type": "string",  
        "required": true,  
        "maxlength": 10  
    },  
    "OrderedRevenue": {  
        "type": "float",  
        "min": 0  
    },  
    "OrderedUnits": {  
        "type": "integer",  
        "min": 0  
    },  
    "AvgSalesPrice": {  
        "type": "float",  
        "min": 1  
    }  
}
```

Order Validation and transformation process is a little complex, so with the help of a tree diagram, we'll try to explain the process - 
![[Blank diagram.jpeg]]


If any lines of the input JSON fail to meet the criterion mentioned in the schema, then it will be considered failed data. The rest will be regarded as transformations. First, we will discard all the rows where we're observing average selling price and sales are less than equal to zero(though this scenario will not arise as we already took out all such cases in the validation part). Then we'll do matter-by-case checks - 
- If there is no variation in the price for a particular SKU, we'll discard the entire data for that specific SKU and append it to the failed data.
- Suppose we observe that the number of different prices for a particular SKU is less than some threshold(currently 5) in such cases. In that case, estimating the product's elasticity won't be easy. Hence we append this data to the failed data.
- If the total number of data points is less than some pre-defined threshold, then also we discard this data and append it to the failed data.
- After all the above checks, whatever remains is good data worth estimating the elasticity.

# Pricing
Pricing of the products is done two ways - for good data, we'll do dynamic pricing, and for bad data, we'll do ruled-based pricing.

## Dynamic Pricing
The elasticity of a product is defined as $\gamma = \frac{\partial Q / Q}{\partial P / P}$, where $Q$ is the sales quantity and $P$ is the sales price of the product. To simplify the differential equation, we'll take the denominator in the other side and doing integration and little bit of manipulation we can express this equation this way - $Q=CP^\gamma$, where $C$ is the integration constant. Now, let's assume that at $t$-th time point/day sales quantity is $Q_t$ and average selling price is $P_t$. So, $Q_t =  P_t^\gamma$ and $Q_{t-1} =  P_{t-1}^\gamma$, by taking the ration of these two, we can express $Q_t$ as a function of $P_t$, i.e., $Q_t = Q_{t-1} \big( \frac{P_t}{P_{t-1}}\big)^\gamma$. This equation can be used for elasticity estimation.

### Elasticity Estimation
We have historical data to estimate the elasticity of the SKU/product. For ease of the estimation, we'll take log both side of this equation $Q_t = Q_{t-1} \big( \frac{P_t}{P_{t-1}}\big)^\gamma$ and the equation will look like this $log(Q_t/Q_{t-1}) = \gamma \cdot log(P_t/P_{t-1})$, this equation is analogous to the linear equation $Y=α X$, which can easily be calculated from the data - 
$$
\begin{aligned}
X &= \{log(P_i/P_{i-1})\}_{i = 2}^{t-1}\\
Y &= \{log(Q_i/Q_{i-1})\}_{i = 2}^{t-1}\\
\gamma &= (X^T X)^{-1}X^TY
\end{aligned}
$$
### Demand Forecasting
For demand forecasting we were looking for something in linear form as this will be used to calculate the revenue and eventually it'll be used in Optimisation. We took help of Taylor Series Expansion to linearise the Demand Forecasting Equation; steps are mentioned below - 

$$
\begin{aligned}
Q_t &= Q_{t-1} \big( \frac{P_t}{P_{t-1}}\big)^\gamma \\
&=Q_{t-1} \big( 1 + \frac{P_t - P_{t-1}}{P_{t-1}}\big) ^ \gamma \\
&=Q_{t-1}\big(1 + \gamma \frac{P_t - P_{t-1}}{P_{t-1}} \big) \\
&= (1-\gamma) Q_{t-1} + \gamma\frac{Q_{t-1}}{P_{t-1}} P_t
\end{aligned}
$$

### Optimization
Price recommendations are generated by solving an Optimisation problem, the objective of the optimization is to maximize the Revenue($\mathcal{R}$)
$$
max_{\mathcal{P}_t} \; \mathcal{R} = \sum_{i \in \Omega} Q_{i,t} \cdot P_{i,t} = \sum_{i \in \Omega} \Big[\big( \gamma\frac{Q_{i,t-1}}{P_{i,t-1}} \big) P_{i,t}^2 + \big( (1-\gamma) Q_{i,t-1} \big) P_{i,t} \Big]
$$
Where $\mathcal{P}_t = \{P_{1,t}, P_{2,t}, \cdots P_{n,t} \}$ is the price of the of the products at time point $t$ and $\Omega$ is the product set. The Constraints are - 
- Price of each product should not go beyond 110% of the base price and below 95%, i.e. $P_{i,t} \le 1.10 \cdot B_i \; \; \& \; \; P_{i,t} \ge 0.95 \cdot B_i \; \; \forall i \in \Omega$, where $B_i$ is the base price of the product which can be calculated as the average price of the product till date.
- Total Profit should be ranging from 95% of total cost to 1.10% of total cost, where Profit is differences between the revenue and total cost(i.e. revenue - total cost). As Revenue is a quadratic function of $P_{i,t}$ and we can't use non-linear function in the constraint, so we had to linearise it using first order Taylor Series Expansion - $\mathcal{R}_{approx} = \mathcal{R}(\hat{\mathcal{P}}_t) + \Big[ \Delta\mathcal{R} \Big\vert_{\mathcal{P}_t = \hat{\mathcal{P}}_t} \Big]^T \Big(\mathcal{P}_t - \hat{\mathcal{P}}_t \Big)$, where $\mathcal{R}_{approx}$ is the approximation at point $\hat{\mathcal{P}}_t$, $\hat{\mathcal{P}}_t = \big[ \hat{\mathcal{P}}_{1,t}, \hat{\mathcal{P}}_{2,t}, \cdots \hat{\mathcal{P}}_{n,t} \big]$, and gradient of $\mathcal{R}$ at point $\hat{\mathcal{P}}_t$ is defined as $\Big[ \Delta\mathcal{R} \Big\vert_{\mathcal{P}_t} \Big] = \big[ \frac{\partial \mathcal{R}}{\partial P_{1,t}} \vert_{\hat{P}_{1,t}}, \frac{\partial \mathcal{R}}{\partial P_{2,t}} \vert_{\hat{P}_{2,t}}, \cdots \frac{\partial \mathcal{R}}{\partial P_{n,t}} \vert_{\hat{P}_{n,t}} \big]^T$. Using this approximation function of revenue we can construct the constraint for multiple points - $\mathcal{R}_{approx} \ge 1.00 \cdot \sum_{i \in \Omega} Q_{i,t} \cdot C_{i,t} \; \;  \& \; \; \mathcal{R}_{approx} \le 1.04 \cdot\sum_{i \in \Omega} Q_{i,t} \cdot C_{i,t}$, where $C_{i,t}$ is the cost of good sold(COGS), if cost is not available as a proxy we can use the base price.


## Rule Based Pricing
We have following rules to work on the rest of SKUs, i.e. the bad data
- If the price is zero all the time, then the new price is 0 as well. 
- If the price is having a constant price each time, then we set the price +/- 1% variation from its constant price. 
- If the price is varying less than 5 price points historically, then select the price of the most generated average revenue by far. 
- If the price point are roughly 10 then predict the price based on a time series ARIMA model. Similarly, for negative revenue/ volume data. 

# Performance Testing
Performance testing a much necessary component of a dynamic pricing engine, any dynamic pricing engine primarily comprises of two things - Elasticity Estimation and Price Optimization. Both of these components undergo several estimation process, which might raise concern regarding bias towards some high revenue products, and so on. There three major reason we want to track historical performance testing - 
- Lack of Transparency - though elasticity estimation is very straight forward, still optimization part may seem like a blackbox.
- Robustness - how the entire engine is performing over the days without any human interaction, that needs to be validated.
- Success - the biggest question is that whether we're at all benefited from the Dynamic Pricing Engine.

Because of the above mentioned reasons we wanted to track 