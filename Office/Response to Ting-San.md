#### Does the Uplift model assess what action will improve users' purchase probability?
Uplift modeling is a prescriptive technique that predicts how each customer will respond to a marketing action. Actor-Critic will help us recommend actions based on the previous history of recommendations and customer profiles. If we club these together, we can iteratively recommend a sequence of steps to maximize conversion.

#### Are users already happy and going to purchase anyway, so we should waste advertising dollars?
An Uplift A2C model uses historical data to differentiate between four types of users (see image below).

![[Pasted image 20220902142744.png]]
If the model can identify "Persuadables" and "Sleeping Dogs" accurately, marketers can determine which customers to target to drive incremental conversions with a marketing campaign. Hence, we should do anything to "Sure Things."

#### Will our ads annoy users and, therefore, negatively impact their probability of purchasing?
A bunch of customers are very sensitive to ads; we can profile such customers based on their return visits and the number of ads they see (impression).

#### Are they persuadable and, therefore, the right target to advertise? Or they have already made up their mind not to purchase. 
Here, the fundamental challenge is to identify "Persuadables" and "Sleeping Dogs" - both of them are sensitive to advertisements. We'll specifically target "Persuadables" and, in ideal situations, avoid the "Sleeping Dogs", but technically, avoiding is not possible, so our approach will be a minimalistic targeting approach.

#### How do we learn the best action to take that optimizes the final purchase, not the local optimization action?
Final Purchase means we have successfully acquired that customer, and our approach will change from that point in time. Our focus will move towards the retention of that customer by engaging them, e.g., through pop-up messages, email campaigns, etc.

Customer acquisition will be more focused on a return visit and click-through rate. In contrast, for customer retention, we will focus on nudging the customer to explore other services, like asking to write a review and signing up for different services(cross-selling). 

When we are in the customer acquisition phase, we should focus on the discoverability of our products. This can be done by ranking all the products based on purchase probability, but if we have too many products, this approach fails to provide good results. To overcome this issue, we recommend A2C Uplift Modeling, which can tackle many actions(here, which product to recommend).

During the retention phase, we will concentrate on providing appropriate discounts, Offers, etc. We have two options here - we can give uniform discounts to every or personalize the deals based on the customer profile and our historical interaction with the customer(e.g., number of ads shown, number of email campaigns, etc.).

#### How does the contextual bandit or an efficient algorithm to balance explore and exploit fit here? Is it for learning the best action at each step?
This is the learning to maximize overall rewards, so as you know, the initial stage model will explore, and it’s not optimal. In the begining of the experiment exploration wilFor example, Thompson sampling will solve the trade-off between exploring and exploiting by choosing an arm based on the posterior distribution.