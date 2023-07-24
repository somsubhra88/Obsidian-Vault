## Variables

$$ X_{ijt} = Produce \ SKU \ i \ on \ line \ L5 \ or \  L6 \ at \ time \ t \ \in \ \ [T] $$

$$ F_{vt} = Produce \ SKU_v \ on \ line \ 7 \ at \ time \ t \ \in \ \ [T]\\ $$

$$CON_{vi} = \text{WIP bottle conversion quantity per assembly }\\ $$

$$ J_i = production \ line\ J \\ $$

$$ Time \ Interval, \ t = {1,2, ..., T} \ \ \ \forall t \in [1,8640]\\ $$

$$ W_{ijt} = \text{SKU Change}\ \in \ \ [-1,0,1]\\ $$

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

$$ MSA_{i}\ = \ Production \ rate \ per \ hour \ in \ btls \\ $$

$$ CIP_{total} = sum \ of \ CIP \ time\ $$

$$ Tray_{cap} = \text{WIP tray capacity in bottles}$$

$$ MIN_{RT} = \text{Minimum run time is 48 hrs}$$

$$ TH_f = \text{Threshold hours of 5 step CIP by flavor}$$

## Constraints

  


1. Production is greater than demand
$$ \sum_{i=1}^n (X_{ijt} ) \cdot MSA_{i} \geq Demands_{it} \text{; } \forall j \text{, } \forall t  $$

  
  
2. Item is produced on a line only if the line has the capability
$$

\displaystyle \ X_{ijt} \leq \ c_{ij} \; \forall \ i , j $$

  
3. At most one item could be produced on a line
$$

\sum_{i=1}^n X_{ijt} \leq 1 \;  \forall \ j, t $$

  

$$ \text{Job Start}\\

\displaystyle \ B_{i,j,t} \ = \ max (0, W_{ijt} ) $$

  

$$ \text{SKU Change [-1,0,1]}\\

\displaystyle \ W_{ijt} \ = \ X_{i,j,t} \ - \ X_{i,j,t-1} $$

  

$$ \text{Job End}\\

\displaystyle \ E_{i,j,t} \ = \ max (0, -W_{ijt} ) $$

  

$$ \text{Total End Time}\\

\displaystyle \ TE \ = \ max (t * E_{ijt} ) $$

  

$$ \text{Inventory at end of t+1}\\

\displaystyle \ I_{t+1} \ = \ I_t + \sum_{i=1}^n Y_{it} \cdot MSA_{i,t} - \sum_{i=1}^n R_{it} \cdot CON_{v,i}\ \ \ \forall t $$

  

$$ \text{Inventory greater than 0}\\

\displaystyle \ I_t \geq 0\ $$

  

$$\text{If an item is being produced on L5/6, either inventory storage or packing starts at the same time}\\

\displaystyle \ \sum_{j \in [1,2]} X_{ijt} \leq Y_{it} + R_{i,t} \text{, } \forall i \text{, } \forall t $$

  

$$\text{Inventory storage and packing must not incur at the same time in parallel}\\

\displaystyle \ Y_{it} + R_{i,t} \leq 1 \text{, } \forall i \text{, } \forall t $$

  

$$ \text{CIP must elapse after production of an item on L5/L6 before producing next item}\\

\displaystyle \ \sum_{t+1}^{t+CIP_{ik}} X_{kjt} \leq M \cdot (1-X{ijt}) \ \ \ \forall i, \forall j, \forall t, i\neq k $$

  

$$ \text{Minimum running time is 48 hours}\\

\displaystyle \ \sum_{t+1}^{t+MIN_{RTi}} X_{ijt} \ = \ MIN_{RTi} \ \ \ \ X_{ijt}\ =\ 1,X_{ijt-1}\ =\ 0\ \text{, } \forall i, \forall j, \forall t, i\neq k $$

  

$$ \text{Force 5 step CIP every 96 hours of production run}\\

\displaystyle \ \sum_{t+TH}^{t+TH+42} X_{kjt} \leq M \cdot (TH-\sum_{t}^{t+TH} X{ijt}) \ \ \ \forall i, \forall j, \forall t $$