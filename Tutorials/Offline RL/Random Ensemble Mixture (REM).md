# Ensemble-DQN
Ensemble DQN is a simple extension of DQN that approximates the $Q$-values via an ensemble of parameterized $Q$-functions. Each $Q$-value estimate, denoted $Q^k_\theta (s, a)$, is trained against its own target $Q^k_{θ^\prime} (s, a)$, similar to [[Bootstrapped DQN]] . The $Q$ functions are optimized using identical mini-batches in the same order, starting from different parameter initialization. The loss $\mathcal{L}(\theta)$ takes the form,
$$\mathcal{L}(\theta) = \frac{1}{K} \sum_{k=1}^K \mathbb{E}_{s, a, r, s^\prime \sim \mathcal{D}} \Bigg[\mathcal{l}_k \Big( Q_\theta^k(s, a) - r - \gamma \; max_{a^\prime}(s^\prime, a^\prime) \Big)
\Bigg] $$
where $\mathcal{l}_\lambda$ is the [[Huber loss]].

# Random Ensemble Mixture (REM)
- Increasing the number of models used for ensembling typically improves the performance of supervised learning models. 
- Random Ensemble Mixture (REM) uses multiple parameterized $Q$-functions to estimate the $Q$-values, similar to Ensemble-DQN. 
- The key insight behind REM is that one can think of a convex combination of multiple $Q$-value estimates as a $Q$-value estimate itself. 
- Using this insight, we train a family of $Q$-function approximators defined by mixing probabilities on a $(K − 1)$-simplex.

![[Screenshot 2022-05-19 at 12.18.11 AM.png]]

For each mini-batch, we randomly draw a categorical distribution $\alpha$, which defines a convex combination of the $K$ estimates to approximate the optimal Q-function. This approximator is trained against its corresponding target to minimize the TD error. The loss $\mathcal{L}(\theta)$ takes the form,
$$
\mathcal{L}(\theta) = \frac{1}{K} \sum_{k=1}^K \mathbb{E}_{s, a, r, s^\prime \sim \mathcal{D}} 
\Bigg[
\mathbb{E}_{α \sim \mathbb{P}_\Delta} 
	\Big[
		l_k \big(\sum_k \alpha_k Q_\theta^k(s,a) -r -\gamma \cdot max_{a_\prime} \sum_k \alpha_k Q_{\theta^\prime}^k (s^\prime, a^\prime) \big)
	\Big]
\Bigg]
$$

Where $\mathbb{P}_\Delta$ represents a probability distribution over the standard $(K − 1)$-simplex $\Delta ^{K−1}= \{\alpha \in \mathbb{R}^K : \alpha_1 + \alpha_2 + \cdots + \alpha_K = 1, \; \alpha_k ≥ 0,  \; k = 1, \cdots , K \}$.