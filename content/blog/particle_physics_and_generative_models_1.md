# From First Principles in Particle Physics to Generative AI

If you're like me you've heard time and time again that diffusion models where "inspired by physics". And time and time again you didn't explored that statement further. Well you should have, because it is actually not that difficult to go from elementary principles of physics all the way to full fledged generative models. Lets take that little journey here.

Lets start by trying to characterize the movement of a tiny little particle which will follow us throughout this blogpost.

## Newtonian mechanics

In newtonian mechanics, the motion of our particle can be described through Ordinary Differential Equations (ODEs). They can take many shapes and forms but a relatively common one looks like this: $$m \ddot x = F(x) = -\gamma \dot x - \nabla E(x) $$

$m$ is the mass of our little particle, x its position. So $\ddot x$ is its acceleration. This is the very basic "mass times acceleration equals force" that you should have heard before. In our model we chose to describe the force $F$ as a sole function of the position of the particle. This isn't necessarly true, but it does map well to many scenarios.

Here we chose a specific model for our force: $F(x) = -\gamma \dot x - \nabla E(x)$. $E$ is the potential energy of the particle. $\nabla E(x)$ encodes the intuition that particles tend to go towards region of small energy by following its negative gradient. If that energy were gravitational potential energy for example, it would increase the higher up that particle is. That would mean the particle would feel a force that pushes it down e.g gravitional force. Nothing weird so far.

Note that this energy potential can be whatever we want. We can mostly abstract over it when solving physics problems. Here we'll assume the potential is coercive and admits at least one minima for practical reasons.

But what is that $-\gamma \dot x$ term exactly ? Its a term that describes a "non conservative" force, i.e a force that is *not* derived from an energy. There are many such forces in nature, here we chose a very simple model that assumes the force is inversly proportional to the velocity of the particle. Think for example of a particle in a gas. If that particle moves very fast in the gas, it will very likely collide with other particles and get slowed down. These collisions will average out to a force slowing the particle down. The faster the particle goes, the more it will collide with other particles and get slowed down. For the sake of simplicty we might as well assume its linearly proportional to the particle's velocity. The model is crude but its good enough and is actually coherent with some real physical situations. Namely that of an object moving relatively slowly in a very viscous fuild, think of a spoon dropped in a jar of honey for example.

Together these terms work in unison to give the particle a very intuitive behaviour, the particle moves towards regions of low energy (e.g the ground when the energy is gravity) and it progressively slows down during the dynamic. This is easy to see we set acceleration and speed to zero in the equation: $0 = -\nabla E(x)$ i.e the particle is at equilibrium only when the energy is at a local minima. This means the particle will tend to go and stay close to local minimas of the energy function (recall we made some simple assumptions on the energy to ensure that the minima exists).
This is pretty important so remember that for later.

## Dissecting the ODE

Lets start playing with our little math equation. First, what happens if there is no energy ?

Our equation becomes $m \ddot x = -\gamma \dot x$ e.g $m \dot v = -\gamma v$ with $v$ the particle velocity. This is a very simple ODE with an obvious solution: $v = exp(-\frac{\gamma}{m}t)v_0$, where $v_0$ is our velocity at time 0. In this scenario the particle slows down at an exponential rate. That rate is $-\frac{\gamma}{m}$ and its inverse is often called the characteristic time of the motion. A caracteristic time is essentially the amount of time it takes for the motion of a system to change significantly. Lets keep that in the corner of our heads for now.

Second, what happens if there is no friction e.g $\gamma = 0$ ?

The equation becomes $m \ddot x = -\nabla E(x)$ which isn't very informative ... Does this motion have a characteristic time of its own ? Here everything depends on our energy potential. In our previous scenario, characteristic time was roughly the time scale it took for velocity to change, could we define something smilar here ? The short answer is no, unless we make assumptions on the energy.
How do we choose a relevant assumption ? Lets call dimentional analysis to the rescue ! When in doubt physicists love to simply write formulae that end up being in the same unit as the quantity they want to estimate, then retroactively come up with a totaly logical explanation for that formula. I'm exagerating here of course (but not by that much let's be for real physicists), but that is sort of what i'll be doing here.
Our energy gradient is in Joules per meter or $kg \cdot m \cdot s ^ {-2}$. Divide that by the mass of our particle and we get $m \codt s ^ {-2}$. Take the inverse square root of that formula and we get $\sqrt{\frac{m}{\nabla E(x)}}$ in $s \cdot m^{-0.5}$. Lets just bruteforce things and introduce a distance $d$ in our formula. We get $\sqrt{\frac{m d}{\nabla E(x)}}$ in $s$. Finally we have the right unit. If we can come up with an interpretation of $\frac{\nabla E(x)}{d}$ and give it a numerical value we can come up with a characteristic time for this equation.
Remember we said that particles following the motion we described above tends to end up close to local minimas of energy. We'll make use of that assumption now. Near a local minima the gradient of the energy is going to be close to zero. A typical way we can approximate any sufficiently regular function around a point with null derivatives is with a second order Taylor polynomial. If our energy is a second order polynomial, its gradient will just be a linear function of $x$ ! So something like $\nabla E(X) = k x$ with a scalar $k$ would work (i'm simplifying here because our potential might not be isotropic but forget that for now). This scalar k is exactly in the right unit we want. Intuitively, its related to the width of our potential well. Indeed a parabola will look "wider" if its second order coefficient is close to zero.
Lets re-write our caracteristic time formula: $\sqrt{\frac {m}{k}}$. Its a lot simpler now ! Our caracteristic time is simply the square root of the ratio between a particle mass, and a parameter k, in Joules per squared meters, caracterising the width of a potential well. This result is quite intuitive actually: the higher the mass the higher the characteristic time, meaning it takes more time for motion to change significantly, i.e that heavy particles move slower.
The caracteristic time is inversly related to potential well width. If the well is narrow, k is small. Energy quickly rises if the particle goes too far away from the well, and quickly exerts a force that brings the particle back. The resulting motion is quicker.

## The diffusive regime

We devised two caracteristic times for our ODE: $t_1=\frac{m}{\gamma}$ for the pure friction ODE and $t_2\sqrt{\frac{m}{k}}$ for the pure energy driven ODE. For the sake of our argument lets continue working with our gas analogy and assume the mass of our particle is very small. As the mass tends towards zero, its clear the caracteristic time of the pure friction ODE will get much smaller than the other one, because the $x \mapsto \sqrt x$ tends towards zero much slower than $x \mapsto x$. What will this mean for our full ODE ?

Well this is actually a good thing ! Because of that we will be able to simplify the ODE further. The lower caracteristic time of the friction term means that the related dynamic will happen much faster than the rest. The particle will be brutally slowed down by the friction term, while the energy won't have time to change significantly. This lets introduce a new hypothesis that will simplify our model further:

Initially, our particle will get slowed down mostly by friction, whose associated force will be much higher than the energy related force. After a few $t_1$, the particle gets slowed down do the point where the friction force and energy derived force are of similar amplitude. At that point *acceleration* will essentially be *zero* for the *rest* of the dynamic.

This may not be completely intuitive so lets think of it a little harder.
Non negligeable acceleration means the friction and energy derived terms are significantly different.
This will drive motion in the particle but, again, the friction part will drive it much more reactively.
In the few $t_1$s it will take for motion to change, the energy gradient almost will not have changed at all.
Lets assume its constant.
This leads to a new intermediate simplified version of the equation: $m \dot v = -\gamma v + F$, with F constant.
This is again a very simple ODE which admits $v(t) = exp(-\gamma t / m) - mF/\gamma$ as a solution.
Again, after a few $t_1$s acceleration will be almost null, only this time the velocity will equal $-mF/\gamma$.

We could view this new dynamic as a sort of series of small local equilibria where friction balances out conservative forces.

This is great news because we can now write a even simpler ODE, which we call *overdamped*:

$$0 = -\gamma \dot x - \nabla E(x) $$

This will be one the fondamental bricks on which we will build diffusion models.

## Conclusion

Few ... that was kind of a lot but we made it so far. We took elemental principles of physics and used them to model the motion of an hypothetical, low mass particle in a gas: $$\gamma \dot x = - \nabla E(x)$$
Our model is very simple and relies on quite few assumptions but it will be enough to eventually build a diffusion model.
