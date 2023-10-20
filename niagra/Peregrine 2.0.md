# Variables
- index = concatenation of order number, SKU and the date string; let's assume we have $n$ such combinations
- Plant/RORG = source from where demand will be satisfied; let's assume we have $m$ such combinations
- Original Allocation Matrix($U$) = A Matrix which indicates the original plant allocation, elements of $U$ is $U_{i,j} = 1 \text{ index } i \text{ is originally allocated to plant } j \text{ else } 0$, dimension of the matrix is 
- Cost Matrix($C$) = The cost associated with transporting the demand of $i^{th}$ index from $j^{th}$ plant
# Problem Formulation