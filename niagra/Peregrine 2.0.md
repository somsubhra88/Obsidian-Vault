# Variables
- index = concatenation of order number, SKU and the date string; let's assume we have $n$ such combinations

- SKU Set($S$) = 

- Plant/RORG = Source from where demand will be satisfied; let's assume we have $m$ plants
  
- Capacity Matrix($D$) = Capacity Matrix to indicate the capacity of each plant for each SKU, $D_{s,j}$ is the capacity of $s^{th}$ SKU at $j^{th}$ plant, which can be calculated by summing up the CTP and ATP
  
- Original Allocation Matrix($U$) = A Matrix which indicates the initial plant allocation, elements of $U$ is $U_{i,j} = 1 \text{ index } i \text{ is originally allocated to plant } j \text{ else } 0$, the dimension of the matrix is $n \times m$
  
- Cost Matrix($C$) = The cost associated with transporting the demand of $i^{th}$ index from $j^{th}$ plant, the dimension of the matrix is $n \times m$
# Problem Formulation

## Decision Variables
$$X_{ij} = 1 \; if \; demand \; at \; i^{th} \; index \; will \; delivered \; from \; j^{th} \; plant \; else \; 0$$

## Objective Function
$$ minimize \; X \otimes C - U \otimes C$$
## Constraints
Fulfilment Constraint
$$ \sum_{j=1}^n X_{i, j} = 1 \; \forall \; i \in [1..n]$$
Capacity Constraints