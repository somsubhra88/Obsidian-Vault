# Variables and Functions
## Random Variables
- $S_t$: State at time $t$, any realization of $S_t$ is denoted by $s \in \mathbb{S}$
- $A_t$: Action taken at time $t$, an
- $R_t$
- 


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
	\mathbb{E} \left[ R_t \vert S_t = s\right] &= \sum_{a \in \mathbb{A}} \sum_{s^{\prime} \in \mathbb{S}}\pi(a \vert s)T_a(s, s^{\prime}) \mathcal{R}_a(s,s^{\prime})
\end{align}
$$
## Part 2
$$
\begin{align}
	& \mathbb{E} \left[ \sum_{i = 0} ^\infty \gamma^i R_{t+i+1} \vert S_t = s \right] \\ 
	&= \sum_{a \in \mathbb{A}} \sum_{s^{\prime} \in \mathbb{S}} \pi(a \vert s)T_a(s, s^{\prime})  \mathbb{E} \left[ \sum_{i = 0} ^\infty \gamma^i R_{t+i+1} \vert S_t = s , A_t = a, S_{t+1} = s^{\prime} \right] \\
	&= \sum_{a \in \mathbb{A}} \sum_{s^{\prime} \in \mathbb{S}} \pi(a \vert s)T_a(s, s^{\prime})  \mathbb{E} \left[ \sum_{i = 0} ^\infty \gamma^i R_{t+i+1} \vert S_{t+1} = s^{\prime} \right] \; \; \text{(Using Markov Property)} \\
	&= \sum_{a \in \mathbb{A}} \sum_{s^{\prime} \in \mathbb{S}} \pi(a \vert s)T_a(s, s^{\prime}) V^{\pi}(s^{\prime})
\end{align}
$$

## Combining Part 1 &  Part 2
$$
\begin{align}
	V^{\pi}(s) &= \sum_{a \in \mathbb{A}} \sum_{s^{\prime} \in \mathbb{S}}\pi(a \vert s)T_a(s, s^{\prime}) \mathcal{R}_a(s,s^{\prime}) + \gamma \sum_{a \in \mathbb{A}} \sum_{s^{\prime} \in \mathbb{S}} \pi(a \vert s)T_a(s, s^{\prime}) V^{\pi}(s^{\prime}) \\
	&= \sum_{a \in \mathbb{A}} \sum_{s^{\prime} \in \mathbb{S}}\pi(a \vert s)T_a(s, s^{\prime}) \left[ \mathcal{R}_a(s,s^{\prime}) + \gamma V^{\pi}(s^{\prime})  \right]
\end{align}
$$

## Writing the Equation in Expectation Form
$$
\begin{align}
	V^{\pi}(s) &= \mathbb{E}_{a \sim \pi(s)} \left[ Q^{\pi}(s, a) \right] \\
	Q^{\pi}(s, a) &= \mathbb{E}_{s^{\prime} \sim T_a(s, s^{\prime})} \left[ R_t + \gamma V^{\pi}(s^{\prime})\right] = \mathbb{E}_{s^{\prime} \sim T_a(s, s^{\prime})} \left[ R_t + \gamma \mathbb{E}_{a \sim \pi(s)} \left[ Q^{\pi}(s, a) \right]\right]
\end{align}
$$

