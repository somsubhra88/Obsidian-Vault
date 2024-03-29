# Background
Earlier Click Through Rate(CTR) was the metric we optimized using Bandit Algorithm, and predominantly we're using [[Thompson Beta Sampling]]. This time we wanted to Optimize Conversion Rate(CVR); while we were trying to optimize this, we faced several challenges - 

- **Delayed Feedback** - Conversion feedback is delayed, so an immediate reward was not available; hence updation of the Bandit was not possible even after taking action.
- **Anonymous Feedback** - It is quite possible that before conversion, a customer has experienced/seen several different banners, and at the time of the conversion, it is challenging to attribute this conversion to all the banners he has seen previously; moreover, it will be entirely illogical to give the entire credit to the last seen flag.
- **Aggregated Feedback** - It is observed that a customer has purchased multiple products at the same time; in such a scenario, the effect of various banners is confounded among themselves, and it's hard to segregate them.

Because of all these challenges, we decided to go with an Algorithm Named "_Bandit Algorithm for Delayed, Aggregated and Anonymous Feedback_" this algorithm has complexity, and its worthiness is yet to be proven. Initial simulation results show promising results, but we must validate this with accurate data and rigorous simulation. So, we have decided to go with a simple implementation and slowly move towards a more complex algorithm. We decided to go ahead with minimal code changes.

## Method We have adopted
We hypothesized that the Probability of conversion given there is a click is invariant to time for a particular banner, i.e. $Pr(Conversion \vert Click)$ is invariant to time and can be estimated from historical data. So, the Probability of a click given an impression depends upon the campaign, i.e. $Pr(Click \vert Impression)$ is campaign specific. We are already optimizing CTR, which is nothing but finding the banner having the best odds of a click, i.e., we'll exploit the banner with the maximum CTR we have estimated through the Bandit Experimentation. Our idea was elementary; we'll calculate the $Pr(Conversion \vert Click)$ from the historical data and feed into the algorithm as pre-calculated weights, and the positive rewards(number of clicks) will be multiplied by these weights to get the approximate conversions; this can be considered feedback from the environment.


Now, we have to figure out which algorithm fits into this construct and start our feasibility checking with all the plausible algorithms - 

- **Thompson Beta Sampling**- We hypothesized that there is a possibility that Thompson Beta Sampling will not converge because it is primarily meant for integer rewards.
- **Thompson Normal Sampling**- This may fit into our construct, but we found several drawbacks with this algorithm - 
	- First, rewards are mainly not normally distributed; the closest distribution is Uniform distribution.
	- Second, it has never been tested with accurate data, and we didn't do much simulation study for this; we're a little skeptical about it.
- **Upper Confidence Bound**- As this algorithm doesn't require any prior assumptions on the rewards distribution, we thought this algorithm was a perfect fit for our construct, and we went ahead with this.

## Simulation Study
Business stakeholders were not very comfortable with the policy change and wanted to validate our hypothesis through a simulation study. So, we have done a simulation study to check/compare the following things - 
- Convergence Rate
- Regret Bound
- Estimation of the Bandit Arms Mean

For simulation, we have used two different policies and two different environments; with this setup, we have conducted multiple simulations, all possible permutations and combinations we have tried, and most of the case trends are the same. As there aren't any significant differences or trends in all those simulations, I have provided the plots for one of the simulations.

#### Polices Used
- Upper Confidence Bound 
- Thompson Beta Sampling

#### Environment Used
- Bernoulli Environment - Produces success(1) or failure(0) with some probability.
- Fractional Reward Environment - Produces success values ranging from 0 to 1 and failure(0) with a certain probability.

#### Simulation Parameters
I have tested with multiple combinations - 
- Number of Arms - Ranging from 5 arms to 12 arms.
- Arms Mean - Ranging from 0.01 to 0.20, and the means are equally spaced but randomly assigned.
- Number of Episodes - 10,000


### Regret Plot

In the Multi-Arm Bandit problem, the maximum possible reward would be obtained if the best arm/banner was chosen at every step. Every time a non-optimal banner is selected, the total potential compensation that can be obtained reduces further from this theoretical maximum. As a result, you **_regret_** choosing this bandit and wish instead that you’d chosen the best one. As the term _regret_ implies, you may have no way to know in advance that you’re making a non-optimal choice; only in hindsight do you realize your mistake.

The regret $\mathcal{L}$ is calculated by taking the difference between the reward obtained by the implemented policy and the reward that would have been obtained if instead the optimal policy had been followed, over a total of $T$ time steps:
$$
\mathcal{L} = T \cdot \mathbb{E}\big[ R_t \vert A_t = a_* \big] - \sum_{i=1}^T \mathbb{E} \big[ R_t \vert A_t = a \big]
$$
Theoretically, regrets for all the standard algorithms are asymptotically converged to an upper bound. The different algorithm has different upper bound, the algorithm which is having the lowest upper bound, is considered to be better in terms of regret. So, to do a comparison study we tend to plot the regrets over the number of episodes and try to visualize the outcomes.

![[Simulation of Thompson Sampling Regret.png]]


##### Observations
1. The best policy and environment combination is Thompson Sampling in Bernoulli Environment; after a long 10k iterations, the real regret is close to 25, which is very promising when looking at the rest of the plots.
2. The Worst among all is Upper Confidence Bound in Bernoulli Environment; its performance is worse than UCB in the fractional reward environment. We have hypothesized that UCB in Fractional Reward will perform better, and simulation results also show similar trends.
3. Thompson Sampling in the Fractional Reward environment also performs pretty well; though it's not the best in regret values, it is still way better than UCB in any situation. So we can conclude that Thompson Sampling in the Fractional Reward environment also performs descent; we can't expect the performance as good as the Thompson Sampling in Bernoulli Environment, but it is ten times better than any UCB algorithms.


### Average Reward Plot(Convergence Test)

The regret plot indicates the potential loss of opportunity will occur over time or during the campaigns, but it doesn't tell the convergence rate. We'll only be able to reduce the loss of opportunity when we stop exploring. We can have the luxury of halting the exploration when we're confident about the best-performing banner. So, as soon as we can estimate the mean of the arms, the more we can exploit the best arm/banner.

We plotted the average cumulative rewards against the episodes to test the convergence rate. As regret is converged in all the scenarios, convergence is guaranteed, but we wanted to see how soon each combination converges to the mean of the best-performing arm.

![[Simulation of Thompson Sampling Reward.png]]


##### Observations
1. After one thousand episodes, all the algorithms stopped exploration and started exploiting the best arm, which has a mean response rate is $20\%$. 
2. As the regret of the regret of UCB algorithms were in the initial period so, it is taking time to converge, but in case of Thompson Sampling regret was pretty low so it didn't take much time to converge.
3. From the above two observations, we can conclude that convergence starting point is more or less same for all the algorithms. Our second hypothesis was that using of fractional reward instead of integer reward may hit the convergence rate, but from the above plot we don't see such possibilities.


## Conclusion
Above simulation study shows that Thompson Sampling can also be used for Fractional Reward as well, though it's performance is degraded in compare to Thompson Sampling with Integer Reward, but still it's way better than Upper Confidence Bound algorithm. So, we can go ahead implement the same we did for UCB1. If we decide to make changes in the code of Thompson Sampling we need be very cautious, because of the following reasons - 
- Currently Thompson Sampling is predominantly used by the Product team(PITARI) and touching this means we need to be very careful.
- After incorporating the new feature, i.e. widget values, we need to make sure that existing features remain unchanged and the API calls will be unchanged
- Rigorous testing is very much essential, current testing script can be used but it does just few used cases. To make things in proper way we should design proper test cases and examples. Integration testing is another piece; for this we need to work with the Pitari team closely.