In this document, we'll try to explain the end-to-end process to get the price recommendations. Primarily it is segregated into two significant steps - 
# Data Processing and Validation
Input data is raw JSON data, one comprises Product details, and the other is sales-related. On each JSON file, we will run a validation check to ensure there isn't any discrepancy in the data and then transform and store it in a usable format(comma-separated file).

## Dynamic Pricing
The elasticity of a product is defined as $\gamma = \frac{\partial Q / Q}{\partial P / P}$, where $Q$ is the sales quantity and $P$ is the sales price of the product. To simplify the differential equation, we'll take the denominator in the other side and doing integration and little bit of manipulation we can express this equation this way - $Q=CP^\gamma$, where $C$ is the integration constant. Now, let's assume that at $t$-th time point/day sales quantity is $Q_t$ and average selling price is $P_t$. So, $Q_t =  P_t^\gamma$ and $Q_{t-1} =  P_{t-1}^\gamma$, by taking the ration of these two, we can express $Q_t$ as a function of $P_t$, i.e., $Q_t = Q_{t-1} \big( \frac{P_t}{P_{t-1}}\big)^\gamma$. This equation can be used for elasticity estimation.

### Elasticity Estimation
We have historical data to estimate the elasticity of the SKU/product
$$
Q_t(P_t) = (1-\gamma) Q_{t-1} + \gamma Q_{t-1}\frac{P_t}{P_{t-1}}
$$


$$
log\frac{Q_t}{Q_{t-1}} = \gamma log \frac{P_t}{P_{t-1}}
$$
