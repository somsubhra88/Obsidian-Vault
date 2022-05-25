We know that the MSE is great for learning outliers, while the MAE is great for ignoring them.

**The Huber Loss Function**

The Huber Loss offers the best of both worlds by balancing the MSE and MAE together. We can define it using the following piecewise function:

$$ 
L_{\delta }(a)={
\begin{cases}
	{\frac {1}{2}}{a^{2}}&{\text{for }}|a|\leq \delta,\\
	\delta \cdot \left(|a|-{\frac {1}{2}}\delta \right),&{\text{otherwise.}}
\end{cases}}
$$
where $a$ can be considered as the difference between predicted value and the original value, i.e., $a=y - \hat{f}(x)$.

```note-green
What this equation essentially says is: for loss values less than delta, use the MSE; for loss values greater than delta, use the MAE. This effectively combines the best of both worlds from the two loss functions!

Using the MAE for larger loss values mitigates the weight that we put on outliers so that we still get a well-rounded model. At the same time we use the MSE for the smaller loss values to maintain a quadratic function near the centre.
```