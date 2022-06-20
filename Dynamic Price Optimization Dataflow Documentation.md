In this document, we'll try to explain the end-to-end process to get the price recommendations. Primarily it is segregated into two significant steps - 
# Data Processing and Validation
Input data is raw JSON data, one comprises Product details, and the other is sales-related. On each JSON file, we will run a validation check to ensure there isn't any discrepancy in the data and then transform and store it in a usable format(comma-separated file).

## Dynamic Pricing
The elasticity of a product is defined as $\gamma = \frac{\partial Q / Q}{\partial P / P}$, where $Q$ is the sales quantity and $P$ is the sales price of the product. To simplify the differential equation, we'll take the denominator in the other side and doing intergration and little bit of manupulation we can express this equation this way - $Q=CP^\gamma$.