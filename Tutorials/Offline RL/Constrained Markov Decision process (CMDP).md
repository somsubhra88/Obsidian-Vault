# Constrained Markov Decision Process
To understand the constrained MDP, we must revisit MDP first and from there we all incorporate additional conditions to convert an MDP problem to a Constrained MDP.

## Markov Decision Process

A discrete time Markov Decision Process is defined by a 4-Tuple $(S, A, P_a, R_a)$, where:
- $S$ is the state space that contains a finite number of states
- $A$ is the action space is a finite set of actions
- $P_a$ is the transition probabilities, i.e., $P_a(s, s^\prime) = Pr[s_{t+1} = s^\prime | s_t=s, a_t = a]$
- $R_a(s, s^\prime)$ is the immediate reward received after transitioning from state $s$ to state $s^\prime$, due to action $a$


## Constrained Markov Process
A constrained MDP(CMDP) $(S, A, P_a, R_a, d, d_0)$ is an MDP with two additional parameters 
- $d:S \times A \rightarrow [0, D_{max}]$ is the **Cost Function**
- $d_0 \in \mathbb{R}^{\ge 0}$ is the _Maximum Allowed Cumulative Cost_


### Set of Feasible Policies
Policies for those expected cumulative cost is less than $d_0$
$$ \Pi := \Bigg\{ \pi : s \rightarrow Pr(A) \Bigg\vert \mathbb{E} \left[\sum_{t=0}^T \gamma^t d(s_t, a_t)\Big\vert s_0, \pi \right] \le d_0 \Bigg\}$$

### Objective
According to CMDP the problem can be formulated as
$$ J(\pi) = arg max_{\pi \in \Pi} V^\pi(s_0) $$
subject to
$$ C(\pi) = \mathbb{E}\left[\sum_{t=0}^T \gamma^t d(s_t, a_t) \Big\vert s_0, \pi \right] \le d_0 $$
### The Lagrangian Dual Problem
To solve the CMDP problem, first, the CMDP problem is converted into its Lagrangian problem as follows:
$$ max_{\pi \in \Pi} L(\pi, \lambda) = J(\pi) - \lambda (C(\pi) - d_0) $$
If $\Pi$ is not a null set then there exists a policy $\pi$ that satisfies the constraint $C(\pi) < d_0$
