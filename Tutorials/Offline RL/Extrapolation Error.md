The key insight in TD Learning is that $Q$-values for the current time step can be defined in terms of $Q$-values of the next time step. That is, $Q^{\pi}(s, a)$ is defined recursively, as below
$$
Q^\pi (s, a) = \mathbb{E}_{s^\prime \sim p(s^\prime \vert s, a), r } \Big[  \Big]
$$