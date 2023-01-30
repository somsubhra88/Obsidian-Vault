$$
\begin{align}
	V^{\pi}(s) 
	&= \mathbb{E} \left[ \sum_{i = 0}^{\infty}  \gamma^{i} R_{t + i} \vert S_t = s \right] \\
	&= \mathbb{E} \left[ R_t + \gamma R_{t + 1} + \gamma^2 R_{t+2} + \cdots  \vert S_t = s \right] \\
	&= \mathbb{E} \left[ R_t + \gamma \sum_{i = 0} ^\infty \gamma^i R_{t+i+1} \vert S_t = s \right] \\
	&= \underbrace{\mathbb{E} \left[ R_t \vert S_t = s\right]}_\text{Part 1}  + \gamma \underbrace{\mathbb{E} \left[ \sum_{i = 0} ^\infty \gamma^i R_{t+i+1} \vert S_t = s \right]}_\text{Part 2} \\
\end{align}
$$
## Part 1
$$
\begin{align}
	\mathbb{E} \left[ R_t \vert S_t = s\right] &= \sum_{a \in \mathbb{A}} \sum_{s^{\prime} \in \mathbb{S}}\pi(a \vert s)T_a(s, s^{\prime}) r(s,a,s^{\prime})
\end{align}
$$
## Part 2
$$
\begin{align}
	& \mathbb{E} \left[ \sum_{i = 0} ^\infty \gamma^i R_{t+i+1} \vert S_t = s \right] \\ 
	&= \sum_{a \in \mathbb{A}} \sum_{s^{\prime} \in \mathbb{S}} \pi(a \vert s)T_a(s, s^{\prime})  \mathbb{E} \left[ \sum_{i = 0} ^\infty \gamma^i R_{t+i+1} \vert S_t = s , A_t = a, S_{t+1} = s^{\prime} \right] \\
	&= \sum_{a \in \mathbb{A}} \sum_{s^{\prime} \in \mathbb{S}} \pi(a \vert s)T_a(s, s^{\prime})  \mathbb{E} \left[ \sum_{i = 0} ^\infty \gamma^i R_{t+i+1} \vert S_{t+1} = s^{\prime} \right] \; \; \text{(Using Markov Property)}
\end{align}
$$