#### Uplift model is to access what action will improve users' purchase probability, right?

#### Are users already happy and going to purchase anyway, so we should waste advertising dollars?

#### Will our ads annoy users and, therefore, negatively impact their probability of purchasing?

#### Are they persuadable and, therefore, the right target to advertise? 

#### Or they have already made up their mind not to purchase. 

#### How do we learn the best action to take that optimizes the final purchase, not the local optimization action?  

#### How does the contextual bandit or an efficient algorithm to balance explore and exploit fit here? Is it for learning the best action at each step?
This is the learning to maximize overall rewards, so as you know, the initial stage model will explore, and it’s not optimal. For example, Thompson sampling will solve the trade-off between exploring and exploiting by choosing an arm based on the posterior distribution.