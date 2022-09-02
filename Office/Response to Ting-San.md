#### Does the Uplift model assess what action will improve users' purchase probability?
Uplift modeling is a prescriptive technique that predicts how each customer will respond to a marketing action. Actor-Critic will help us recommend actions based on the previous history of recommendations and customer profiles. If we club these together, we can iteratively recommend a sequence of steps to maximize conversion.

#### Are users already happy and going to purchase anyway, so we should waste advertising dollars?
An Uplift A2C model uses historical data to differentiate between four types of users (see image below).

![[Pasted image 20220902142744.png]]
If the model can identify "Persuadables" and "Sleeping Dogs" accurately, marketers can determine which customers to target to drive incremental conversions with a marketing campaign. Hence, we should do anything to "Sure Things".

#### Will our ads annoy users and, therefore, negatively impact their probability of purchasing?
There are bunch customers 

#### Are they persuadable and, therefore, the right target to advertise? 

#### Or they have already made up their mind not to purchase. 

#### How do we learn the best action to take that optimizes the final purchase, not the local optimization action?  

#### How does the contextual bandit or an efficient algorithm to balance explore and exploit fit here? Is it for learning the best action at each step?
This is the learning to maximize overall rewards, so as you know, the initial stage model will explore, and it’s not optimal. For example, Thompson sampling will solve the trade-off between exploring and exploiting by choosing an arm based on the posterior distribution.