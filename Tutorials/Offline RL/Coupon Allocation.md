# Business Objective
Currently in most places discounting on products works very unidirectional, as the same amount of discount every time and for every customer. We believe there are essentially three questions that need to be answered—
- <font color="Red">Who to give the coupon to? </font>This particular question will be answered by the purchase probability (this is an entirely different project which is already on the verge of finishing)
- <font color="red">What coupon to give? </font> We want to answer this particular piece of the puzzle, we have planned to use historical data to train an RL agent which will eventually interact with the system going forward, and recommended the discount percentage
- <font color="red">When to give?</font> We are currently not focusing on answering this particular question, but we are hoping that while we'll be training the RL agent in an offline fashion at that time also we can have this question to be answered.


# Coupon System
Different coupon system has different objectives, In most the cases there are primarily two objectives
```note-green
- Customer Revisit or interaction with the e-commerce platform or the increase the click-through rate, eventually all these boils down to the same objective, i.e. retention of the customers
- The second most challenging and optimistic goal is to increase the conversion, i.e. increasing the purchase
```

```note-grey-background
Obviously, the larger value to distribute coupons is the higher retention and conversion. However, it may bring huge financial loss to the platform when coupon allocation is too costly. Thus, it is a key problem for the platform to decide on an appropriate value for each coupon, and maximize the users’ retention while limiting the cost not to exceed a fixed budget.
```

The budget-constrained coupons allocation problem is usually formulated as a [[Constrained Markov Decision process (CMDP)]], and then can be converted into a Lagrangian dual problem. [[Batch Reinforcement Learning]] methods are used to avoid potential financial risks in the learning process, as the budget cannot be recovered once dispensed. Unfortunately, a key issue is that the policy needs to be re-learned every time when the value of $\lambda$ is updated until the budget constraint is satisfied. Such a repetitive policy learning process brings a great computation overhead.


# Problem Formulation
We have a pre-collected dataset, $\mathcal{D} = {(s_i, a_i, r_i, c_i=d(s_i, a_i), s_{i+1})}_{i=1}^M$, generated from historical behavioral policies. The goal of batch policy learning under constraints is to learn a policy $\pi \in \Pi$ from $\mathcal{D}$ that maximize the primary objective cost while satisfying constraints:
$$ max_{pi \in \Pi} J(\pi) = \mathbb{E}_{\tau \sim \pi, \mu} \left[ \sum_{t=0}^T \gamma^t r_t \right] $$
subject to 
$$ C(\pi) = \mathbb{E}_{\tau \sim \pi, \mu} \left[ \sum_{t=0}^T \gamma^t c_t \right] \le d_0 $$
Clearly this is equivalent to the min-max problem: 
$$ min_{\lambda \in \mathbb{R}^{\ge 0}} max_{\pi \in \Pi} L(\pi, \lambda) $$
To solve the Lagrangian dual problem, it is needed to find an optimal value of Lagrangian variable $\lambda^∗$ and the corresponding optimal policy $\pi^∗_{\lambda^∗}$.


# Proposed Approach

This approach comprises predominantly 3 steps- 

![[Screenshot 2022-05-19 at 1.05.05 PM.png]]

## λ – Generalization of data
we will extend the data using following equations
$$L(\pi, \lambda) = J(\pi) - \lambda * (C(\pi) - b) = \mathbb{E}\Big[ \sum_{t=1}^T r(s_t, a_t) - \lambda c(s_t, a_t) \Big] + \lambda b$$

We can consider the cost reward function $r^\lambda = r(s_t, a_t) - \lambda c(s_t, a_t)$. Therefore, the offline RL method can be used to develop an optimal offline policy maximizing $\mathbb{E}\big[ \sum_{t=1}^T r^\lambda (s_t, a_t) \big]$.

The proposed $\lambda$-generalization method first increases the size of training dataset under different values of $lambda$. For each transition tuple $(s_i, a_i, r_i, c_i, s_{i+1})$, we enlarge it into $\{(s_i, a_i, r^{\lambda^j}_i , c_i, s_{i+1}, \lambda_j )\}^L_{j=1}$ where $\lambda_j \in \{\lambda_1, \lambda_2, \cdots, \lambda_L\}$. Therefore, the original training dataset $\mathcal{D} = \{(s_i, a_i, r_i, c_i, s_{i+1})\}_{i=1}^M$ is enlarged into $\mathcal{D}^\prime = \{(s_i, a_i, r^{\lambda_j}_i, c_i, s_{i+1}, λ_j)\}^{M,L}_{i,j=1,1}$, which is L times as the size of D.

## Offline Policy Learning
```note-green-background
There are two types of popular offline RL methods
 - BCQ method: BCQ addresses the problem of extrapolation error via ensuring the match between the state-action pairs generated using the learned policy and the fixed training dataset.
 - REM method: REM has high robustness and strong generalization ability with the multi-head network.
```

For policy learning, an improved offline RL algorithm called **Resemble Batch-Constrained Q-learning** (R-BCQ) is used, which combines the advantages of two popular offline RL methods: [[Batch Constrained deep Q-learning (BCQ)]] and [[Random Ensemble Mixture (REM)]]. Specifically, R-BCQ addresses the problem of extrapolation error like BCQ, and retains strong generalization ability like REM.

![[Screenshot 2022-05-18 at 6.43.45 PM.png]]
### Algorithm Explanation
#### Generative Model
A Generative Model $G_\omega(s, \lambda)$ which, given the state as input, produces an action. Using a generative model, this way assumes we pick actions using:
$$ argmax_a P^G_\mathcal{B}(a|s, \lambda) $$
or in other words, the most likely action given the state and $\lambda$, with respect to the data in the batch. This is difficult to model in high dimensional continuous control environments, so we approximate it with a [Variational Autoencoder](https://avandekleut.github.io/vae/)

The generative model is updated with the loss $$ L(\omega) = \mathbb{E}_{s,a,\lambda^\prime \sim \mathcal{D}^\prime} \Big[ -log G_\omega(a \vert s, \lambda) \Big] $$

#### Value Target 
Loss function is mostly inspired from the Concept of [[Random Ensemble Mixture (REM)]].
$$ 
L(\theta) = \mathbb{E}_{s, a, r^\lambda, \lambda \sim \mathcal{D}^\prime} 
\Bigg[\mathcal{l}_\delta \Big(
r^\lambda + \gamma \; max_{a^\prime} \sum_i α_i Q_i^\prime(s^\prime, a^\prime, \lambda) - \sum_i \alpha_i Q_i(s, a, \lambda)
\Big) \Bigg]
$$
where $l_\delta$ is the [[Huber loss]].

## Policy Evaluation

- The proposed REME method employs a multi-head evaluation network to evaluate the policy and uses an iterative update mechanism to train the evaluation network. 
- Specifically, each head of the evaluation network represents an evaluated value of the given policy, and a convex combination of all heads is used as the final evaluated value. Unlike using the max operator when calculating the target value in the policy learning, we compute the target value using the evaluated policy $\pi$ in policy evaluation. 
- Combined with the $\lambda$-generalization method, the policy evaluation network is able to evaluate the policy $\pi$ with different values of $\lambda$. The evaluated network with parameters $\hat{\theta}$ is updated according to the loss:
$$ 
L(\hat{\theta}) = \mathbb{E}_{s, a, r^\lambda, \lambda \sim \mathcal{D}^\prime} 
\Bigg[\mathcal{l}_\delta \Big(
r + \gamma \; \sum_i α_i \hat{Q}_i(s^\prime, \pi(s^\prime, \lambda), \lambda) - \sum_i \alpha_i \hat{Q}_i(s, a, \lambda)
\Big) \Bigg]
$$