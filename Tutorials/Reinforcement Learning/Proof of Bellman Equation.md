# Variables and Functions

## Random Variables
- $S_t$: State at time $t$, any realization of $S_t$ is denoted by $s \in \mathcal{S}$, where $\mathcal{S}$ is the state space
- $A_t$: Action taken at time $t$, any realization of $A_t$ is denoted by $a \in \mathcal{A}$, where $\mathcal{A}$ is the action space
- $R_t$: Reward at time $t$ and $R_t \sim \mathcal{R}$, defined as $\mathcal{R} : \mathcal{S} \times \mathcal{A} \times \mathcal{S} \rightarrow \mathbb{R}$, this is written as $\mathcal{R}_a(s, s^{\prime})$

## Functions
- $\mathcal{T}$: Transition function, $\mathcal{R} : \mathcal{S} \times \mathcal{A} \times \mathcal{S} \rightarrow [0, 1]$, this is written as $\mathcal{T}_a(s, s^{\prime})$
- $\pi$: Policy, there are two types of policies, deterministic and probabilistic. Deterministic policy one to one mapping from state to action, Probabilistic Policy gives a distribution of different actions, i.e. $\pi(a|s)$


# Definition V-function & Q-function
$$
V^{\pi}(s) = \mathbb{E}_{\tau \sim \pi} \left[ \sum_{i = 0}^{\infty}  \gamma^{i} R_{t + i} \vert S_t = s \right]
$$

$$
Q^{\pi}(s, a) = \mathbb{E}_{\tau \sim \pi} \left[ \sum_{i = 0}^{\infty}  \gamma^{i} R_{t + i} \vert S_t = s, A_t = a \right]
$$
# Proof

$$
\begin{align}
	V^{\pi}(s) 
	&= \mathbb{E}_{\tau \sim \pi} \left[ \sum_{i = 0}^{\infty}  \gamma^{i} R_{t + i} \vert S_t = s \right] \\
	&= \mathbb{E}_{\tau \sim \pi} \left[ R_t + \gamma R_{t + 1} + \gamma^2 R_{t+2} + \cdots  \vert S_t = s \right] \\
	&= \mathbb{E}_{\tau \sim \pi} \left[ R_t + \gamma \sum_{i = 0} ^\infty \gamma^i R_{t+i+1} \vert S_t = s \right] \\
	&= \underbrace{\mathbb{E}_{\tau \sim \pi} \left[ R_t \vert S_t = s\right]}_\text{Part 1}  + \gamma \underbrace{\mathbb{E} \left[ \sum_{i = 0} ^\infty \gamma^i R_{t+i+1} \vert S_t = s \right]}_\text{Part 2} \\
\end{align}
$$
## Part 1
$$
\begin{align}
	\mathbb{E} \left[ R_t \vert S_t = s\right] &= \sum_{a \in \mathcal{A}} \sum_{s^{\prime} \in \mathcal{S}}\pi(a \vert s)\mathcal{T}_a(s, s^{\prime}) \mathcal{R}_a(s,s^{\prime})
\end{align}
$$
## Part 2
$$
\begin{align}
	& \mathbb{E} \left[ \sum_{i = 0} ^\infty \gamma^i R_{t+i+1} \vert S_t = s \right] \\ 
	
	&= \sum_{a \in \mathcal{A}} \sum_{s^{\prime} \in \mathcal{S}} \pi(a \vert s)\mathcal{T}_a(s, s^{\prime})  \mathbb{E} \left[ \sum_{i = 0} ^\infty \gamma^i R_{t+i+1} \vert S_t = s , A_t = a, S_{t+1} = s^{\prime} \right] \\
	
	&= \sum_{a \in \mathcal{A}} \sum_{s^{\prime} \in \mathcal{S}} \pi(a \vert s)\mathcal{T}_a(s, s^{\prime})  \mathbb{E} \left[ \sum_{i = 0} ^\infty \gamma^i R_{t+i+1} \vert S_{t+1} = s^{\prime} \right] \; \; \text{(Using Markov Property)} \\
	
	&= \sum_{a \in \mathcal{A}} \sum_{s^{\prime} \in \mathcal{S}} \pi(a \vert s)\mathcal{T}_a(s, s^{\prime}) V^{\pi}(s^{\prime})
\end{align}
$$

## Combining Part 1 &  Part 2
$$
\begin{align}
	V^{\pi}(s) &= \sum_{a \in \mathcal{A}} \sum_{s^{\prime} \in \mathcal{S}}\pi(a \vert s)\mathcal{T}_a(s, s^{\prime}) \mathcal{R}_a(s,s^{\prime}) + \gamma \sum_{a \in \mathcal{A}} \sum_{s^{\prime} \in \mathcal{S}} \pi(a \vert s)\mathcal{T}_a(s, s^{\prime}) V^{\pi}(s^{\prime}) \\
	
	&= \sum_{a \in \mathcal{A}} \sum_{s^{\prime} \in \mathcal{S}}\pi(a \vert s)\mathcal{T}_a(s, s^{\prime}) \left[ \mathcal{R}_a(s,s^{\prime}) + \gamma V^{\pi}(s^{\prime})  \right] \\
\end{align}
$$

## Writing the Equation in Expectation Form
$$
\begin{align}
	V^{\pi}(s) &= \mathbb{E}_{a \sim \pi(s)} \left[ Q^{\pi}(s, a) \right] \\
	
	Q^{\pi}(s, a) &= \mathbb{E}_{s^{\prime} \sim \mathcal{T}_a(s, s^{\prime}), \; R_t \sim \mathcal{R}_a(s, s^{\prime})} \left[ R_t + \gamma V^{\pi}(s^{\prime})\right] \\ 
	
	Q^{\pi}(s, a) &= \mathbb{E}_{s^{\prime} \sim \mathcal{T}_a(s, s^{\prime}), \; R_t \sim \mathcal{R}_a(s, s^{\prime})} \left[ R_t + \gamma \mathbb{E}_{a^{\prime} \sim \pi(s^{\prime})} \left[ Q^{\pi}(s^{\prime}, a^{\prime}) \right]\right]
\end{align}
$$

