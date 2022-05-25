# Why is not technically feasible to implement Thompson-Sampling for CVR optimization?
In [[Thompson Beta Sampling]] begin with an independent prior belief over each $θ_k$. Take these priors to be **beta-distributed** with parameters $α = (α_1, . . . , α_K)$ and $β = (β_1, . . . , β_K)$. In particular, for each action $k$, the prior **Probability Density Function** of $θ_k$ is 
$$p(\theta_k) = \frac{\Gamma(\alpha_k + \beta_k)}{\Gamma(\alpha_k) \Gamma(\beta_k)} \theta_k^{\alpha_k - 1} (1 - \theta_k)^{\beta_k - 1} $$

Hence, these shape parameters $α = (α_1, . . . , α_K)$ and $β = (β_1, . . . , β_K)$ have to be strictly positive for all the time. Technically, there isn't any restrictions over these shape parameters, but we do have special meaning for these parameters. 

The parameters $(α_k, β_k)$ are sometimes called **pseudo-counts**, since $α_k$ or $β_k$ increases by one with each observed success or failure, respectively. A beta distribution with parameters $(α_k, β_k)$ has mean $α_k/(α_k + β_k)$, and the distribution becomes more concentrated as $α_k + β_k$ grows.

![[Screenshot 2022-05-17 at 1.27.39 PM.png]]

Instead of integer update if we have fraction update, this growth will be pretty slow and exploration phase will be continued. 

#### Impact of using fractional rewards
- First, there will be a hit in convergence. 
- Second, it'll no longer be a Beta-Bernoulli conjugate prior distribution, Beta is the Prior Distribution and Bernoulli is the likelihood distribution, so—
	- As likelihood distribution is not a Bernoulli distribution, it has to be some other distribution with an explicit form of PDF
	- We need to find the posterior distribution for this Prior and Likelihood pair
	- Once we have the closed form, then only we can have the new update formula
	
	In simpler term, if the rewards are not integer, ==we need to change the update rule==. To avoid all these hiccups we tend to use well-defined conjugate pairs which have closed form of posterior distribution, the full list of conjugate priors can be found [here](https://en.wikipedia.org/wiki/Conjugate_prior)
	\
	![[Pasted image 20220518113216.png]]


#### Is there any hack?
As weights are fixed for every bandit(banner), so the update formula we can use $$ (\alpha_k, \beta_k) \leftarrow (\alpha_k, \beta_k) + \Big(w_k\sum_{t=0}^T I_{x_t = k} r_t, \sum_{t=0}^T I_{x_t = k} (1 - w_k r_t) \Big) $$
But this may have convergence issue as _Regret Bound_ and the _Convergence Rate_ is not well-defined. By doing rigorous simulation study we can achieve some confidence, but there is no guarantee.

# Have we done any study in offline mode for change in results?
We haven't done any specific simulation study for this purpose, but in general TS works better in compare to UCB.


# What are the potential benefits of using UCB vs TS?
When we made this decision to go with UCB for Conversion Rate Optimization, there were a couple of reasons —
- We have decided to go with minimal code changes and if we want to modify Thompson-Beta Sampling Algorithm code it will create multiple issues—
	- As TS code was in production we don't want to touch the existing workflow
	- Even if we accommodate the changes in TS code, then we need to make sure that without weights it should work as before it was. From a coding perspective, it's a challenge. 
	- For CTR used case, rewards are integer whereas in case of CVR it'll be fraction as we are multiplying with some precalculated weights, so no longer it'll be a Beta-Bernoulli conjugate prior. In such cases, we are not very sure about the convergence of such conjugate prior.
- The reason we chose UCB —
	- There isn't any assumptions regarding the rewards, as we're not presuming any prior distribution.
	- Easy to implement, minimal code change.


# Do we gain with respect to speed which was the primary ask from Yokochi-san?
We haven't tested particularly this aspect of the improvement, with minimal code changes we can't expect much of a difference in performance.

### Why weight has to be included in reward calculation when click distribution still follows Bernoulli (For example, sampling by Beta distribution and arm selection by weight and random variable would be also applicable)?
There is a physical interpretation of the weights - 
$$
\begin{align}
& \text{We wanted to find the Probability of conversion given that there is an impression, i.e.} \\
& i.e. Pr\big(conversion \; \vert \; impression) = Pr(conversion \; \vert \; click) \cdot Pr(click \; \vert \; impression \big) \\
& i.e. Pr\big(conversion \; \vert \; impression) = \text{Predefined Weight} \cdot Pr(click \; \vert \; impression)
\end{align}
$$
### Why other conjugate prior distributions were not explored well such as Gaussian TS
### Why the policy will be changed to UCB without any verification when UCB was not utilized at all in past.