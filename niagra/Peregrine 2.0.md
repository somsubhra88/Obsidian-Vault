The objective is to determine the most cost-effective allocation of resources to fulfil orders while adhering to specific constraints, such as resource availability, demand requirements, and operational rules. It uses mathematical modelling to minimize the total costs associated with resource assignments across different orders, ensuring that each order is met with the optimal combination of resources and options. This optimisation process aims to balance meeting demands precisely and efficiently using resources and minimising the overall operational costs while respecting logistical limitations and business rules.

# Decision Variables


1. **X - Resource Selection Variables**
    
    - **Type:** Binary
    - **Description:** `X` variables are binary, which means each resource allocation decision for the combinations specified by `unique_identifiers` in the `base_data` is a yes (1) or no (0) choice. This alteration simplifies the model by making resource selection a matter of inclusion or exclusion rather than quantity, which could be relevant if the resources are indivisible or if the situation calls for a simple presence/absence decision.
2. **Y - Option Selection Variables**
    
    - **Type:** Binary
    - **Description:** These binary variables indicate whether a particular option is selected for an order. Options could be related to different ways of fulfilling the order, such as using alternate SKUs or sourcing strategies. A binary type ensures an option is either fully selected (1) or not selected (0).
3. **S - Alternate RORG Variables**
    
    - **Type:** Binary
    - **Description:** These variables determine whether a specific RORG (a term that might represent a regional organisational unit or resource group) is chosen for each order. As binary variables, they indicate a yes (1) or no (0) selection, ensuring that each order is assigned to one RORG or none.
4. **D - Alternate Date Variables**
    
    - **Type:** Binary
    - **Description:** Similar to the `S` variables, these binary variables are used to select a specific alternate date for fulfilling each order. This setup allows the model to optimise the scheduling aspect of resource allocation by selecting or not selecting particular dates.

```Python
unique_identifiers = [  
    "ORDERNUM",  
    "ORIGINAL_SKU",  
    "ALTERNATE_SKU",  
    "ALTERNATE_RORG",  
    "ALTERNATE_DATE",  
    "ALTERNATE_OPTION",  
]
```
### Summary

Each set of these variables plays a critical role in the optimisation model by defining the decision space over which the model seeks to minimise cost. `X` variables adjust the allocation of resources, `Y` variables toggle the selection of specific order options, and both `S` and `D` variables manage the assignment of orders to different organisational units and dates, respectively.

# Parameters
1. **A - Required Cases**
    
    - **Fields Involved:** Based on `unique_identifiers` which include identifiers like order number, SKU, etc.
    - **Description:** This parameter represents the number of cases required for each unique combination of identifiers in the dataset.
2. **C - Available Resources**
    
    - **Fields Involved:** ALTERNATE_SKU, ALTERNATE_DATE, ALTERNATE_OPTION, RESOURCE
    - **Description:** This parameter indicates the quantity of resources available for each combination of the specified fields. It accounts for the total resources that can be allocated to meet the demands specified by the `A` parameter, considering different SKUs, options, and dates.
3. **T - Total Cases Per Order**
    
    - **Fields Involved:** ORDERNUM
    - **Description:** This parameter aggregates the total number of cases required per order. It is a broader measure that sums up the demands across multiple SKUs.
4. **L - Resource Quantity Aggregated Over All Unique Combinations**
    
    - **Fields Involved:** Based on `unique_identifiers`
    - **Description:** Similar to `A`, but specifically modifies the modified case counts.
5. **COST - Total Cost**
    
    - **Fields Involved:** Based on `unique_identifiers`
    - **Description:** This parameter calculates the total cost of each unique identifier combination. 

# Constraints
## Demand Constraints
- This demand constraint ensures that the total amount of resources allocated across various configurations for each order meets or exceeds the required cases. It guarantees that the resource allocation satisfies the demand for each order.

$$\sum_{\text{idx} \in I(\text{ordernum})} X_{\text{idx}} \cdot A_{\text{idx}} \geq T_{\text{ordernum}}$$
$$
\begin{align*}
I(\text{ordernum}) &: \text{The set of indices for variable combinations related to the specific order number.} \\
X_{\text{idx}} &: \text{The decision variable representing the quantity of resources allocated at index } \text{idx}. \\
A_{\text{idx}} &: \text{The parameter indicating the required cases for the configuration at index } \text{idx}. \\
T_{\text{ordernum}} &: \text{The total required cases for the order number } \text{ordernum}.
\end{align*}
$$
- This demand constraint ensures that the total allocation of resources across different configurations does not exceed one for each unique combination of order number and SKU within the dataset. Essentially, this constraint prevents over-allocation, ensuring that each specific SKU within an order is allocated at most once, thus avoiding any duplication in resource assignment.
$$\sum_{\text{idx} \in J(\text{ordernum}, \text{sku})} X_{\text{idx}} \leq 1$$
$$
\begin{align*}
J(\text{ordernum}, \text{sku}) &: \text{The set of indices for variable combinations related to the specific order number and SKU.} \\
X_{\text{idx}} &: \text{The decision variable representing the allocation of resources at index } \text{idx}.
\end{align*}
$$
## Resource Constraints
This resource constraint ensures that the total allocation of resources for each resource type does not exceed the available cumulative quantity up to a given date. It limits the cumulative demand satisfied by a specific resource to its available quantity by the specified date, thus preventing the over-allocation of resources and ensuring resource availability is not exceeded during the planning period.
$$\sum_{\text{idx} \in K(\text{resource}, \text{dt})} X_{\text{idx}} \cdot A_{\text{idx}} \leq Q_{\text{resource}, \text{dt}}$$
$$
\begin{align*}
K(\text{resource}, \text{dt}) &: \text{The set of indices for variable combinations where the resource is } \text{resource} \text{ and the date is } \leq \text{dt}. \\
X_{\text{idx}} &: \text{The decision variable representing the allocation of resources at index } \text{idx}. \\
A_{\text{idx}} &: \text{The required quantity of resource for the configuration at index } \text{idx}. \\
Q_{\text{resource}, \text{dt}} &: \text{The available quantity of the resource at date, quantity is already calculated cummulatively} \text{dt}.
\end{align*}

$$