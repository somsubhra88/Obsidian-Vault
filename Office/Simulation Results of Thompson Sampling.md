# Background
Earlier Click Through Rate(CTR) was the metric we optimized using Bandit Algorithm and predominantly we're using [[Thompson Beta Sampling]]. This we wanted to optimize Conversion Rate(CVR), while we're trying to optimize this we faced several challenges - 

- **Delayed Feedback** - Conversion feedback is delayed and so an immediate reward was not available, hence updation of the Bandit was not possible even after taking an action
- **Anonymous Feedback** - It is quite possible that before a conversion a customer has experienced several different banners and at the time of the conversion, it is challenging to attribute this conversion to all the banners he has seen previously, moreover it will be entirely illogical to give the entire credit to the last seen banner.
- **Aggregated Feedback** - It is observed that a customer has purchased multiple products at the same time, in such a scenario the effect of multiple banners is confounded among themselves and it's hard to segregate them.

Because of all these challenges we decided to go with an Algorithm Named "_Bandit Algorithm for Delayed, Aggregated and Anonymous Feedback_", this algorithm has complexity and the worthiness of the algorithm is yet to be proven. Initial simulation results show promising results, but we need to validate this with proper data and rigorous simulation. So, we have decided to go with a simple implementation first and then slowly we'll move towards a more complex algorithm. We decided to go ahead with minimal code changes.

## Method We have adopted
We hypothesized that the Probability of conversion given there is a click is invariant to time for a particular banner, i.e. $Pr(Conversion \vert Click)$ is invariant to time and can be estimated from historical data. So, the Probability of a click given an impression depends upon the campaign, i.e. $Pr(Click \vert Impression)$ is campaign specific. We are already optimizing, CTR which is nothing but finding the banner having the best odds of a click, i.e. we'll exploit the banner which is having maximum CTR we have estimated through the Bandit Experimentation. Our idea was very simple, we'll calculate the $Pr(Conversion \vert Click)$ from the historical data and feed into the algorithm as pre-calculated weights, and the positive rewards(number of clicks) will be multiplied by these weights to get the approximate conversions, this can be considered as the feedback from the environment.


Now, we have to figure out which algorithm fits into this construct and started our feasibility checking with all the plausible algorithms - 

- **Thompson Beta Sampling**- We hypothesized that there is a possibility that Thompson Beta Sampling will not converge because it is primarily meant for integer rewards.
- **Thompson Normal Sampling**- This may fit into our construct but we found several drawbacks with this algorithm - 
	- First, rewards are particularly not normally distributed, the closest distribution is Uniform distribution
	- Second, it was never been tested with real data and we didn't do much simulation study as well for this, we're a little skeptical about it
- **Upper Confidence Bound**- As this algorithm doesn't require any prior assumptions on the rewards distribution, so we thought this algorithm is perfectly fitted to our construct and we went ahead with this

## Simulation Study
As business stakeholders were not very comfortable with the policy change, and they want to validate our hypothesis through a simulation study. So, we have done a simulation study to check/compare the following things - 
- Convergence Rate
- Regret Bound
- Estimation of the Bandit Arms Mean

### Regret Plot

In the Multi-Arm Bandit problem, the maximum possible reward would be obtained if the best arm/banner was chosen at every time step. Every time a non-optimal banner is selected the total possible reward that can be obtained reduces further from this theoretical maximum. As a result, you **_regret_** choosing this bandit and wish instead that you’d chosen the best one. As the term _regret_ implies, you may have no way to know in advance that you’re making a non-optimal choice, only in hindsight do you realize your mistake.

The regret $\mathcal{L}$ is calculated by taking the difference between the reward obtained by the implemented policy and the reward that would have been obtained if instead the optimal policy had been followed, over a total of $T$ time steps:
$$
\mathcal{L} = T \cdot \mathbb{E}\big[ R_t \vert A_t = a_* \big] - \sum_{i=1}^T \mathbb{E} \big[ R_t \vert A_t = a \big]
$$

![[Simulation of Thompson Sampling Regret.png]]

The above image is for a regret plot for two different policies and two different environments, 
Polices used - 
- Upper Confidence Bound
- Thompson Beta Sampling

Environment Used - 
- Bernoulli Environment - Produces success(1) or failure(0) with some probability
- Fractional Reward Environment - Produces success values ranging from 0 to 1 and failure(0) with a certain probability





### Average Reward Plot(Convergence Test)

![[Simulation of Thompson Sampling Reward.png]]

