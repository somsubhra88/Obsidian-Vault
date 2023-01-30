$$
\begin{align}
	V^{\pi}(s) 
	&= \mathbb{E} \left[ \sum_{i = 0}^{\infty}  \gamma^{i} r_{t + i} \vert S_t = s \right] \\
	&= \mathbb{E} \left[ r_t + \gamma r_{t + 1} + \gamma^2 r_{t+2} + \cdots  \vert S_t = s \right] \\
	&= \mathbb{E} \left[ r_t + \gamma \sum_{i = 0} ^\infty \gamma^i r_{t+i+1} \vert S_t = s \right] \\
	&= \underbrace{\mathbb{E} \left[ r_t \vert S_t = s\right]}_\text{Part 1}  + \gamma \underbrace{\mathbb{E} \left[ \sum_{i = 0} ^\infty \gamma^i r_{t+i+1} \vert S_t = s \right]}_\text{Part 2} \\
\end{align}
$$
## Part 1
$$
\begin{align}
	\mathbb{E} \left[ r_t \vert S_t = s\right] &= \sum_{a \in \mathbb{A}} \sum_{s^{\prime} \in \mathbb{S}}\pi(a \vert s) 
\end{align}
$$