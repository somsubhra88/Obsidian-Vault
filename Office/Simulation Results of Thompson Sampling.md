# Background
Earlier Click Through Rate(CTR) was the metric we optimized using Bandit Algorithm and predominantly we're using [[Thompson Beta Sampling]]. This we wanted to optimize Conversion Rate(CVR), while we're trying to optimize this we faced several challenges - 

- **Delayed Feedback** - Conversion feedback is delayed and so an immediate reward was not available, hence updation of the Bandit was not possible even after taking an action
- **Anonymous Feedback** - It is quite possible that before a conversion a customer has experienced several different banners and at the time of the conversion, it is challenging to attribute this conversion to all the banners he has seen previously, moreover it will be entirely illogical to give the entire credit to the last seen banner.
- **Aggregated Feedback** - It is observed that a customer has purchased multiple products at the same time, in such a scenario the effect of multiple banners is confounded among themselves and it's hard to segregate them.

Because of all these challenges we decided to go with an Algorithm Named "_Bandit Algorithm for Delayed, Aggregated and Anonymous Feedback_", this algorithm has its own complexity and its worthiness of the algorithm is yet to be proven. Initial simulation results show promising results, but we need to validate this with proper data and rigorous simulation. So, we have decided to go with a simple implementation first and then slowly we'll move towards a more complex algorithm. We decided to go ahead with minimal code changes.

#### Method We have adopted
Our hypothesis was that the Probability of conversion given there is a click is invariant to time for a particular banner, i.e. $Pr(Conversion \vert Click)$ is invariant to time and can be estimated from historical data. So, the Probability of a click given an impression depends upon the campaign, i.e. $Pr(Click \vert Impression)$ is campaign specific. We are already optimizing, CTR which is nothing but finding the banner having b


and started our feasibility checking with all the plausible algorithms - 

- **Thompson Beta Sampling**- We hypothesised that there is a possibil