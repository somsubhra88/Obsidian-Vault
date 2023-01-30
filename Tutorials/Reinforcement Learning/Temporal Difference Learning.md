Temporal Difference(TD) learning is used to learn $Q$-function. The main idea is to use a neural network which produces $Q$-value estimates given $(s, a)$ pair as inputs. This is known as _value network_.



$$
\begin{align}
	Q^{\pi}(s, a) &= \mathbb{E}_{s^{\prime} \sim \mathcal{T}_a(s, s^{\prime}), \; R_t \sim \mathcal{R}_a(s, s^{\prime})} \left[ R_t + \gamma \mathbb{E}_{a^{\prime} \sim \pi(s^{\prime})} \left[ Q^{\pi}(s^{\prime}, a^{\prime}) \right]\right]
\end{align}
$$
