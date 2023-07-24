## Variables

$$ X_{ijt} = Produce \ SKU \ i \ on \ line \ L5 \ or \  L6 \ at \ time \ t \ \in \ \ [T] $$
```python
X = model.addVars(SKU_LIST,LINE,TIME, vtype=GRB.BINARY, name="PRO_LINE")
```
$$ F_{vt} = Produce \ SKU_v \ on \ line \ 7 \ at \ time \ t \ \in \ \ [T]\\ $$
```python
V = model.addVars(SKU_VP_LIST,TIME, vtype=GRB.BINARY, name="PACK_LINE")
```
$$CON_{vi} = \text{WIP bottle conversion quantity per assembly }\\ $$

$$ J_i = production \ line\ J \\ $$

$$ Time \ Interval, \ t = {1,2, ..., T} \ \ \ \forall t \in [1,8640]\\ $$

$$ W_{ijt} = \text{SKU Change}\ \in \ \ [-1,0,1]\\ $$
```python
W_Pro = model.addVars(SKU_LIST,LINE,TIME, vtype=GRB.INTEGER, lb=-1, ub =1, name ="JOB_CHANGE_PRO")

W_Pro_2 = model.addVars(SKU_LIST,LINE,TIME, vtype=GRB.INTEGER, lb=-1, ub =1, name ="JOB_CHANGE_PRO_2")
```

Why are two variables used?

$$ B_{ijt} = \text{Job Start}\ \in \ \ [0,1]\\ $$

$$ E_{ijt} = \text{Job End}\ \in \ \ [0,1]\\ $$

$$ Demands_{it} \ = \ Demand \ for \ SKU \ i \ at \ time \ t \\ $$

$$ L = \{ 1,2 \}\ ,\ two \ stages \ of \ production \ or \ packing\\ $$

$$ Y_{it} = start \ of \ the \ storage \ of \ item \ i \ at \ time \ t $$

$$ R_{it} = end \ of \ the \ storage \ of \ item \ i\\ $$

$$ c_{i,j} = \ item \ i \ can \ be \ produced \ on \ line \ j\\ $$

$$ CIP_{ik} \ = \ CIP \ time \ from \ item \ i \ to \ k \\ $$

$$ CO_{ik} \ = \ Changeover \ time \ from \ item \ i \ to \ k \\ $$

$$ TC_{ik} \ = \ Total \ Changeover \ time \ from \ item \ i \ to \ k \\ $$

$$ I_{it} = Inventory \ of \ item \ i \ at \ the \ end \ of \ time \ t $$
```python
I = model.addVars(SKU_LIST,LINE,TIME, name ="Inv")
```

$$ MSA_{i}\ = \ Production \ rate \ per \ hour \ in \ btls \\ $$

$$ CIP_{total} = sum \ of \ CIP \ time\ $$

$$ Tray_{cap} = \text{WIP tray capacity in bottles}$$

$$ MIN_{RT} = \text{Minimum run time is 48 hrs}$$

$$ TH_f = \text{Threshold hours of 5 step CIP by flavor}$$

## Constraints

  


1. Production is greater than demand
$$ \sum_{i=1}^n (X_{ijt} ) \cdot MSA_{i} \geq Demands_{it} \text{; } \forall j \text{, } \forall t  $$

  
  
2. Item is produced on a line only if the line has the capability
$$ \ X_{ijt} \leq \ c_{ij} \; \forall \ i , j $$

  
3. At most, one item could be produced on a line
$$ \sum_{i=1}^n X_{ijt} \leq 1 \;  \forall \ j, t $$
4. SKU Change can be any of these values $[-1,0,1]$
$$ W_{ijt} \ = \ X_{i,j,t} \ - \ X_{i,j,t-1} $$

5. Job Start
$$ B_{i,j,t} \ = \ max (0, W_{ijt} ) $$
6. Job End

$$E_{i,j,t} \ = \ max (0, -W_{ijt} ) $$

7. Total End Time

$$TE \ = \ max (t * E_{ijt} ) $$

  

8. Inventory at end of $t+1$

$$I_{t+1} \ = \ I_t + \sum_{i=1}^n Y_{it} \cdot MSA_{i,t} - \sum_{i=1}^n R_{it} \cdot CON_{v,i}\ \ \ \forall t $$

  
9. Inventory greater than $0$

$$I_t \geq 0\ $$

10. If an item is being produced on L5 or L6, either inventory storage or packing starts at the same time

$$\sum_{j \in [1,2]} X_{ijt} \leq Y_{it} + R_{i,t} \text{, } \forall i \text{, } \forall t $$
11. Inventory storage and packing must not incur at the same time in parallel
$$Y_{it} + R_{i,t} \leq 1 \text{, } \forall i , t $$

  
12. CIP must elapse after production of an item on L5/L6 before producing next item
$$\sum_{t+1}^{t+CIP_{ik}} X_{kjt} \leq M \cdot (1-X{ijt}) \; \forall i, j, t, \ \& \ i\neq k $$

13. Minimum running time is 48 hours

$$\sum_{t+1}^{t+MIN_{RTi}} X_{ijt} \ = \ MIN_{RTi}$$
$$X_{ijt}\ =\ 1$$
$$X_{ij(t-1)}\ =\ 0$$
$$\forall i, \ j, \ t \ \& i\neq k $$
14. Force 5 step CIP every 96 hours of production run

$$\sum_{t+TH}^{t+TH+42} X_{kjt} \leq M \cdot (TH-\sum_{t}^{t+TH} X{ijt}) \ \ \ \forall i, \forall j, \forall t $$
