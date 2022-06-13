### Bernoulli Bandit
- Suppose there are $K$ actions(banners), and when played, any action yields either a success or a failure. 
- Action $k \in \{1, ..., K\}$ produces a success with probability $θ_k \in [0, 1]$. The success probabilities $(θ_1, ..., θ_K)$ are unknown to the agent, but are fixed over time, and therefore can be learned by experimentation. 
- The objective, roughly speaking, is to maximize the cumulative number of successes over $T$ periods, where $T$ is relatively large compared to the number of arms $K$.
- The “==arms==" in this problem might represent different banner ads that can be displayed on a website. Users arriving at the site are shown versions of the website with different banner ads. 
- A success is associated either with a click on the ad, or with a conversion or a sale of the item being advertised. The parameters $θ_k$ represent either the ==click-through rate(CTR)== or ==conversion-rate(CVR)== among the population of users who frequent the site. The website hopes to balance exploration and exploitation in order to maximize the total number of successes.

### Bandit Setting
To gain the most out of it we first have to have the good knowledge regarding the success probabilities(CTR or CVR for our used case) $(θ_1, ..., θ_K)$ of the banners. This can be done through experimentation:—
- The mean rewards $θ = (θ_1, ..., θ_K)$ are unknown, but fixed over time.
- An action $x_1$ is applied, and a reward $r_1 \in \{0, 1\}$ is generated with success probability $Pr(r_1 = 1|x_1, θ) = θ_{x_1}$. After observing $r_1$, the agent applies another action $x_2$, observes a reward $r_2$, and this process continues.

### Thompson Sampling
Very intuitive algorithm which comprises primarily 4 steps—
- Start with prior over parameters.
- Sample a particular set of parameters from the prior.
- Select $arm = arg max_i \; reward_i \vert parameters$
- Observe reward and update posterior.

#### Beta-Bernoulli
Thompson Sampling using ==Beta Priors==
for $t=1,2, \cdots$ do
	$\qquad$__# Sample Model:__
	$\qquad$for $k=1, \cdots, K$ do
		$\qquad \qquad$Sample $\hat{\theta}_k \sim beta(\alpha_k, \beta_k)$
	$\qquad$end for 
	\
	$\qquad$__# Select and apply action:__
	$\qquad x_t \leftarrow argmax_k \; \hat{\theta}_k$
	$\qquad$Apply $x_t$ and observe $r_t$
	\
	$\qquad$__# Update Distribution:__
	$\qquad (\alpha_{x_t}, \beta_{x_t}) \leftarrow (\alpha_{x_t} +r_t, \beta_{x_t} + 1 - r_t)$
end for

#### Python Implementation
__Update Method__: This method will be used when we'll update the model after every action
```python
self._alpha_values[action] = self.gamma * self._alpha_values[action] + reward  
self._beta_values[action] = self.gamma * self._beta_values[action] + 1 - reward
```

__Batch Update Method__: This method will be used if we're updating the model in regular interval, i.e., in batches
```python
self._alpha_values[action] = self.gamma * self._alpha_values[action] + reward[0]  
self._beta_values[action] = self.gamma * self._beta_values[action] + reward[1]
```



#### How did we get the update formula?
Likelihood Distribution is Bernoulli and Prior distribution is Beta, hence the posterior distribution is 
$$ Posteri \; Distribution \propto Likelihood * Prior $$
Hence, 
$$ \Big( \prod_{i=1}^n \theta^{r_i} \theta^{1-r_i} \Big) * \Big(\frac{\Gamma(α + \beta)}{\Gamma(\alpha) \Gamma(\beta)} \theta^{α -1} (1- \theta)^{β - 1} \Big)  $$
After doing a bit of algebraic manipulation and simplification, 
$$Posteri \; Distribution \propto \theta ^{α + \sum_{i=1}^n r_i -1} (1-\theta)^{β + n - \sum_{i=1}^n r_i - 1}$$

So, each action’s posterior distribution is also beta with parameters that can be updated according to a simple rule:
$$ (\alpha_k, \beta_k) \leftarrow (\alpha_k, \beta_k) + \Big(\sum_{t=0}^T I_{x_t = k} r_t, \sum_{t=0}^T I_{x_t = k} (1 - r_t) \Big) $$