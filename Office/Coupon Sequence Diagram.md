Before answering those questions, I would like to explain the process. When a user comes to our platform, we will first identify the customer, extracting the easy id, and this identification process will be done from the coupon platform itself. The coupon platform will feed this information to RL Engine Prediction API. RL Engine will do three things asynchronously - first, fetching the user features from CDNA; second, extracting the coupon attributes from GSP and finally, CTR & CVR information from RAT. Once we have all three components of contexts, RL Engine will form all the possible contexts. RL Engine will next pull the Cached Model from the Redis Cluster and feed the recently developed contexts. The Cached Model throws the probability of each context, which means the likelihood of clicking each coupon. RL Engine will order the coupons based on the probability and then sends them back to the coupon platform. Then coupon platform will display the coupons in that order.

We are sceptical about the system's throughput and latency, except involving RAT; we can do the rest in the allowable duration. To get the actual throughput, we need to do a PoC.
![[Coupon Sequence Diagram (2).jpeg]]

1. Where and when are we going to display the personalized coupon? On the next load? Or right after the page loaded?

It will be displayed right after the page load, and the coupon platform will show these on the personalized coupon sections.

  

2. How fast does this return the personalised coupon?

I have yet to determine an exact number at this moment, as we are going to do batch processing of the sorting, and the throughput will be within the stipulated time window.

  

3. Will this impact the load page view? There might be a performance issue. Coupon PF Concern: number of API calls in the Coupon Portal already. Coupon PF Concern: number of API calls in the Coupon Portal already

There may be a delay, but we can do a hardware timing test before estimating the impact.

  

4. will RIT adjust the update frequency if it is not real-time?

Updation is not real-time; the updation frequency is 1 hour.

  

5. Will the request only go to every page load? No request on the background? Does RIT have a cost model to store the data?

I don't have any idea. Need more clarification.

  

6. Does the RL engine have an API that accepts easy_id as input and returns coupons based on the coupon data history inside?

So far, we have yet to test the linear bandit in real life, but our system can accept the easy id as input and returns coupons based on the model predictions.

7. Can the RIT RL engine not only sort out the display from the existing but also give potential coupon candidates (kinda like Coupon Advance)?