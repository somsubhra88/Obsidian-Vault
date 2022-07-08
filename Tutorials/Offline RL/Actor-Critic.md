All Actor-Critic algorithms have two components, learned jointly—an **actor**, which retains a parameterized policy, and a **critic**, which learns a value function to evaluate state-action pairs.

It is common to learn the **advantage function** $A^\pi(s,a) = Q^\pi(s,a) - V^\pi(s)$ as the reinforcing signals in these methods.

### Actor
Actors learn parameterized policies $\pi_{\theta_a}$ using the policy gradient. Actors are also used to select actions during interaction with the environment. The updatation of the actors uses the below formula:
$$
\begin{aligned}
\text{Actor-Critic:}\;  &\Delta_\theta J(\pi_{\theta_a}) = \mathbb{E}\big[A_t^{\pi_{\theta_a}} \Delta_\theta log \; \pi_{\theta_a}(a_t \vert s_t) \big] \\
\text{REINFORCE:}\; &\Delta_\theta J(\pi_{\theta_a}) = \mathbb{E}\big[R_t(\tau) \Delta_\theta log \; \pi_{\theta_a}(a_t \vert s_t) \big]
\end{aligned}
$$
### Critic
Critics are responsible for learning how to evaluate $(s,a)$ pairs and using this to generate $A^\pi$. Let's assume $V_{\theta_c}$ is the parameterized version of Value Function.
$$
A_t(s_t) = \sum_{t^\prime = t}^T \gamma^{t^\prime - t} r_{t^\prime}(s_{t^\prime}, a_{t^\prime}) - V_{\theta_c}(s_t, a_t)
$$
Value network is updated using MSE loss—
$$
L(\theta_c) =  \sum_{t=0}^T\Bigg( \sum_{t^\prime = t}^T \gamma^{t^\prime - t} r_{t^\prime}(s_{t^\prime}, a_{t^\prime}) - V_{\theta_c}(s_t, a_t) \Bigg)^2
$$
Update rule is same as the gradient descent—
$$
\theta_c \leftarrow \theta +  \alpha \Delta_{\theta_c}L(\theta_c)
$$
