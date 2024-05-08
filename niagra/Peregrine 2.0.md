The objective is to determine the most cost-effective allocation of resources to fulfil orders while adhering to specific constraints, such as resource availability, demand requirements, and operational rules. It uses mathematical modelling to minimize the total costs associated with resource assignments across different orders, ensuring that each order is met with the optimal combination of resources and options. This optimization process aims to find a balance between meeting demands precisely and efficiently using resources and minimizing the overall operational costs while respecting logistical limitations and business rules.

# Decision Variables
1. **X - Resource Selection Variables**
    
    - **Type:** Continuous
    - **Description:** These variables represent the quantity of resources allocated to each possible combination of order characteristics defined in the dataset. The characteristics could include order number, SKU, and other relevant attributes. The continuous nature of these variables allows for partial allocations of resources.
2. **Y - Option Selection Variables**
    
    - **Type:** Binary
    - **Description:** These binary variables indicate whether a particular option is selected for an order. Options could be related to different ways of fulfilling the order, such as using alternate SKUs or sourcing strategies. A binary type ensures that an option is either fully selected (1) or not selected (0).
3. **S - Alternate RORG Variables**
    
    - **Type:** Binary
    - **Description:** These variables determine whether a specific RORG (a term that might represent a regional organizational unit or resource group) is chosen for each order. As binary variables, they indicate a yes (1) or no (0) selection, ensuring that each order is assigned to one RORG or none.
4. **D - Alternate Date Variables**
    
    - **Type:** Binary
    - **Description:** Similar to the `S` variables, these binary variables are used to select a specific alternate date for fulfilling each order. This setup allows the model to optimize the scheduling aspect of resource allocation by selecting or not selecting particular dates.