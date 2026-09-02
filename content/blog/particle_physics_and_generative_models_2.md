This is follow up of my previous blog post

Recall in the previous blog post we derived a very simple Ordinary Differential Equation (ODE) for the motion of a small particle in a gas: $$\gamma \dot x = - \nabla E(x)$$

## Introducing randomeness

As we explained previously, under some assumptions on E (has a minima and is coercive), the dynamic yielded by the equation leads to the particle reaching a local minima of energy and progressively slowing down.
This is a good start but its not quite enough.
Indeed our models leads to our particle slowing down completely.
But in the real world, small particles in a fluid are subjected to *brownian motion*. Brownian motion is the result of the particle in the fluid being hit by a multitude of fluid particles moving at random.
For now our model is incomplete, we need more: lets introduce randomeness !

Lets turn $x$ and $\dot x$ into random variables: $X_t$ $and \dot X_t$. Lets add a random force in the system $dWt$ following a wiener process.

$p_t$ the distribution

Process with added stochasticity: $$0 = - \gamma \dot X_t - \nabla E(X_t) + \eta dW_t$$


### OLD at equilibrium

The distribution of the particle's position will evolve along time. One could ask towards what this distribution will converge as time increases, if it converges for anything at all.

Let's assume there is a stable distribution i.e an equilibrium.

Let $\mathcal{F}[p] = \int p log \frac{p}{p_{\infty}}$

Show the distribution at equilibrium is the boltzman distribution.
Use the focker planck equation at equilibrium.
Explain the link between the deterministic SDE and the random trajectory.
Give intuitive interpretation using histograms.
Show optionnaly that the KL divergence is strictly decreasing.

Explain strict Lyapunov functional

## Generative Models

### Denoising Models.

A layman's way to understand modern generative models: they learn to remove noise iteratively. Starting from pure noise, they generate data. Assume our real data is distributed according to $p(x)$.

Let $x$ be a data point sampled from $p$. Let $z$ be random normal gaussian noise sampled from $\mathcal N$, and $\sigma>0$ some positive noise scale. Let $y=x+\sigma z$ a noisy version of $x$, distributed following a distribution we call $p_{\sigma}$. For now we'll fix the noise scale.

We want to learn how to denoise this perturbed datapoint y. One way is to learn an denoising operator $D_{\sigma}(\cdot; \theta)$ that learns to remove the additive noise. The operator should satisfy $y + \sigma D_{\sigma}(y; \theta) \simeq x$ (Notice we rescale the denoiser by the amount of noise so the outputs of the network are normalized). by minimizing the mean square error objective: $$\mathbb{E}_{x \sim p; z \sim \mathcal N} [||D_{\sigma}(x+\sigma z) + \frac{y - x}{\sigma}||²]$$.

### Stein Score functions and Denoising Score Matching

So far it feels like what we are doing has no connection to our previous section.

Lets get Langevin dynamics back in the loop. We showed we can sample from some distribution $p$ with a Langevin Dynamic obtained from the gradient of the energy which is equal to the gradient of the log of the probability density. This function $\nabla \log p$ is called the Stein score of the distribution.

What is the score of our target distribution $p$ ? Short answer is we don't know and its completely intractable. No luck.

But remember we can also generate some a distribution from clean data: $p_{\sigma}$, what's its score ? A priori its untractable as well.

But we can use a little trick: we have access to $p_{\sigma}(y|x)$, its just a Gaussian. Gaussians have a closed form Stein score: $$\nabla log p_{\sigma}(y|x) = \frac{x - y}{\sigma}$$

T
