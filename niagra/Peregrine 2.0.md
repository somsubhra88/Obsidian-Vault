# Variables
- index = concatenation of order number, SKU and the date string; let's assume we have $n$ such combinations
- Plant/RORG = source from where demand will be satisfied; let's assume we have $m$ plants
- Original Allocation Matrix($U$) = A Matrix which indicates the initial plant allocation, elements of $U$ is $U_{i,j} = 1 \text{ index } i \text{ is originally allocated to plant } j \text{ else } 0$, the dimension of the matrix is $n \times m$
- Cost Matrix($C$) = The cost associated with transporting the demand of $i^{th}$ index from $j^{th}$ plant, the dimension of the matrix is $n \times m$
# Problem Formulation

## Decision Variables
$$X_ij = 1 if \; i^{th} \; index \; will \; delivered \; from j^{th} \; plant \; else \; 0$$