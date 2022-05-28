The key insight in TD Learning is that $Q$-values for the current time step can be defined in terms of $Q$-values of the next time step. That is, $Q^{\pi}(s, a)$ is defined recursively, as below
$$
Q^\pi (s, a) = \mathbb{E}_{s^\prime \sim p(s^\prime \vert s, a), r \sim \mathcal{R}(s, a, s^\prime)} \Big[ r + \gamma \mathbb{E}_{a^\prime \sim \pi(s^\prime)} \big[Q^\pi (s^\prime , a^\prime)  \big] \Big]
$$
Assume we have a neural network to represent the $Q$-function, $Q_\theta$. In TD learning, $Q^\pi_{target}(s_t, a_t)$ is derived by estimating the right-hand side of the above equation using $Q_\theta$. At each training iteration, $