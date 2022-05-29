**Batch Constrained Q-Learning** is an Offline Reinforcement Learning algorithm proposed by [(Fujimoto et al., 2019)](https://arxiv.org/abs/1812.02900) In this paper, they have demonstrated that due to errors introduced by extrapolation, standard off policy deep reinforcement learning algorithms, such as DQN and [[DDPG]], are incapable of learning without data correlated to the distribution under the current policy, making them ineffective for this fixed batch setting. We introduce a novel class of off-policy algorithms, batch-constrained reinforcement learning, which restricts the action space in order to force the agent towards behaving close to on-policy with respect to a subset of the given data.

## Batch-Constrained Reinforcement Learning
- Current off-policy deep reinforcement learning algorithms fail to address [[Extrapolation Error]] by selecting actions with respect to a learned value estimate. 
- To avoid extrapolation error, a policy should induce a similar state-action visitation to the batch. We denote policies which satisfy this notion as batch-constrained. 
- To optimize off-policy learning for a given batch, _batch-constrained_ policies are trained to select actions with respect to three objectives:
	- Pick Similar State-Action pairs present in the batch.
	- Pick Action should lead to similar State.
	- Maximize the Value Function.

## Algorithm: Pseudo Code
![[Screenshot 2022-05-17 at 10.45.21 PM.png]]

1. **Batch** $\mathcal{B}$: Fixed Data-Set (Batch) of Environment Intersections, and no further experience can be acquired.
2. **Horizon** $T$: Number of Epochs for which we'll be training.
3. **Target Network Update Rate** $\tau$: At each time step, target network parameters $\phi^\prime$ can be set to a weighted average of $\phi$ and $\phi^\prime$. This is known as a _Polyak Update_ and can be thought of a “Soft Update”.
4. **Mini-Batch Size** $N$: Every iteration we'll sample $N$ number of experiences, i.e., $(s, a, r, s^\prime)$ from the fixed data $\mathcal{B}$.
5. **Max Perturbation** $\Phi$: To increase the diversity of visited actions, we'll perturb an action $a$ in the range $[−\Phi, \Phi]$
6. **Number of Sampled Actions** $n$: The number of actions we'll generate from the Generative Model
7. **Minimum Weighting** $\lambda$: This is related to Value Target $y$. 
8. **Q-Networks** : Using of two Q-networks $Q_{\theta_1} \; \& \;Q_{\theta_2}$ is inspired from the [[Clipped Double Q-Learning]], this used to avoid the over estimation error. But in our case a convex combination of the two values is used, with a higher weight on the minimum, to form a learning target which is used by both Q-networks: $$y = r + \gamma \;max_{a_i} \left[ \lambda \; min_{j=1,2} Q_{\theta_j^\prime} (s^\prime , a_i) + (1- \lambda) \; max_{j=1,2} Q_{\theta_j^\prime} (s^\prime , a_i) \right] $$
9. **Generative Network**: Plain vanilla Variational Auto Encoder is being used as a Generative model here $\mu, \sigma = E_{\omega_1}(s, a)$, $\tilde{a} = D_{\omega_2}(s, z)$, $z \sim \mathcal{N}(\mu, \sigma)$. The input of the generative model, i.e. the input of the encoder $E_{\omega_1}$ is the concatenated vector of state and action(one hot encode for discrete actions, for continuous action simple action component), output from the Encoder is the mean and the variance vector
10. **Perturbation network** $\xi_{\phi}$: To increase the diversity of visited actions, we introduce a perturbation model $\xi_{\phi}(s, a, \Phi)$, which outputs an adjustment to an action $a$ in the range $[−\Phi, \Phi]$. The perturbation model $\xi_{\phi}$ can be trained to maximize $Q_θ(s, a)$ through the [[Deterministic Policy Gradient Algorithm]].