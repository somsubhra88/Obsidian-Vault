# Business Problem
Display of coupons based on the following conditions - 
- User segment (demography (e.g. gender, age, area) x Attributes (e.g. occupation, family, preference))
- User preference and usage of coupons
- User shopping behaviour (e.g. purchase/browsing/favourite items, service usage history)

# Objective
We must display and rank coupons to increase user click-through rate (CTR)**. 

# Solution Proposal
Before talking about the solutions we want to propose, we want to make certain assumptions about the data. 

## Data Availability
Based on the data availability, we can tweak the algorithms; for the time being, we are assuming the following data is available and use this data for an innovative and efficient display of the coupons.

### User/Customer Data
- Demographic Information
- Shopping Behavior 
- Usage of Coupons
	- coupons displayed(impression)
	- coupons clicked(click-through rate)
	- purchases using coupons(conversion)

_we are under the impression that all the above data is available in the CDNA database and can be used for this purpose_

### Coupons Data
As of now, we are still determining the kind of data available for coupons. We are making some assumptions to propose, but we can tweak the algorithm based on the data availability.
- Coupons Visual Attributes
- Coupons Value Proposition
- Coupons Display Period(lifetime)
- Coupons Impression, Click-through Rate and Conversion Rate

_as per our latest discussion, we believe we have the above data available in the GSP database_


## Solution Proposals
First, we will talk about the overview of the solution and then the background and maths behind the solution - 

### Overview
- At time $t$ a user arrives on our website with some characteristics($u_t$)
- One or several coupons with some characteristics($m_t$) could be recommended to the user
- For each coupons a context $A \in \mathbb{R}^d$ will be built based on user features and coupon features, i.e. concatenation of $u_t$ and $m_t$
- By choosing a context $A$ the associated product is displayed to the user
- Click will be consider as a positive reward and non-click as a negative reward, our model predict the probability of a click of a given context. A reward $X_t$ depending on $A_t$ is then observed $Pr[X_t = 1 | A_t] = \mu(A_t) + \epsilon_t$

