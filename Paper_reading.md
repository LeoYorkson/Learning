# This .md contains the paper reading from 2024~

## 📖 [Understanding Black-box Predictions via Influence Functions](https://arxiv.org/abs/1703.04730)

### Influence function deriving from Appendix A.

Emprical risk: $$R(\theta) \stackrel{\text { def }}{=} \frac{1}{n} \sum L\left(z_i, \theta\right)$$

Emprical Hessian $$H_{\hat{\theta}} \stackrel{\text { def }}{=} \nabla^2 R(\hat{\theta})=\frac{1}{n} \sum \nabla_\theta^2 L\left(z_i, \hat{\theta}\right)$$

upweight training point $z$ to delete $z$ in emprical risk $$\hat{\theta}_{\epsilon, z}=\arg \min _{\theta \in \Theta}\{R(\theta)+\epsilon L(z, \theta)\}$$

define $$\Delta_ \epsilon=\hat{\theta}_ {\epsilon, z}-\hat{\theta}$$

$\hat{\theta}$ is not related to $\epsilon$ $$\frac{d \hat{\theta}_ {\epsilon, z}}{d \epsilon}=\frac{d \Delta_\epsilon}{d \epsilon}$$

first-order optimality condition: $$0=\nabla R\left(\hat{\theta}_ {\epsilon, z}\right)+\epsilon \nabla L\left(z, \hat{\theta}_{\epsilon, z}\right)$$

Talyor expansion $$0\approx\left[\nabla R(\hat{\theta})+\epsilon\nabla L(z,\hat{\theta})\right]+\left[\nabla^2R(\hat{\theta})+\epsilon\nabla^2L(z,\hat{\theta})\right]\Delta_\epsilon$$

$$\Delta_\epsilon\approx-\left[\nabla^2R(\hat{\theta})+\epsilon\nabla^2L(z,\hat{\theta})\right]^{-1}\left[\nabla R(\hat{\theta})+\epsilon\nabla L(z,\hat{\theta})\right]$$

$\nabla R(\hat{\theta})=0$ $$\Delta_\epsilon\approx-\nabla^2R(\hat{\theta})^{-1}\nabla L(z,\hat{\theta})\epsilon$$

finally $$\left.\frac{d\hat{\theta}_ {\epsilon,z}}{d\epsilon}\right|_ {\epsilon=0}=-H_{\hat{\theta}}^{-1}\nabla L(z,\hat{\theta}) \stackrel{\text { def }}{=} \mathcal{I}_{\mathrm{up},\mathrm{params}}(z)$$

### Influence of upweighting $z$ on the loss at a test point $z_{test}$

$$\begin{aligned}
\mathcal{I}_ {\mathrm{up,loss}}(z,z_{\mathrm{test}})& \overset{\mathrm{def}}{=}\left.\frac{dL(z_{\mathrm{test}},\theta_ {\epsilon,z})}{d\epsilon}\right|_ {\epsilon=0}  \\
&=\nabla_\theta L(z_{\mathrm{test}},\hat{\theta})^\top\frac{d\hat{\theta}_ {\epsilon,z}}{d\epsilon}\Big|_ {\epsilon=0} \\
&=-\nabla_\theta L(z_{\mathrm{test}},\hat{\theta})^\top H_{\hat{\theta}}^{-1}\nabla_\theta L(z,\hat{\theta})
\end{aligned}$$

It can be seen from influence function(z) $\frac{d \hat{\theta}_ {\epsilon, z}}{d \epsilon}=\frac{d \Delta_\epsilon}{d \epsilon}$. Apply an influence $\epsilon$ to the data point $z$, the larger the influence function is, the greater the influence $z$ has on $z_{test}$.



## 📖 [DENOISING DIFFUSION IMPLICIT MODELS](https://arxiv.org/abs/2010.02502)

### New inference distribution

DDIM defines a new inference distribution:

$$q _\sigma(\boldsymbol{x} _{1:T}|\boldsymbol{x} _0):=q _\sigma(\boldsymbol{x} _T|\boldsymbol{x} _0)\prod _{t=2}^Tq _\sigma(\boldsymbol{x} _{t-1}|\boldsymbol{x} _t,\boldsymbol{x}_0)$$

where $q_\sigma(\boldsymbol{x}_T|\boldsymbol{x}_0)=\mathcal{N}(\sqrt{\bar{\alpha}_T}\boldsymbol{x}_0,(1-\bar{\alpha}_T)\boldsymbol{I})$

for all $t > 1$
$$q _\sigma(\boldsymbol{x} _{t-1}|\boldsymbol{x} _t,\boldsymbol{x} _0)=\mathcal{N}\left(\boldsymbol{x} _{t-1};\sqrt{\bar{\alpha} _{t-1}}\boldsymbol{x} _0+\sqrt{1-\bar{\alpha} _{t-1}-\sigma_t^2}\cdot\frac{\boldsymbol{x}_t-\sqrt{\bar{\alpha}_t}\boldsymbol{x}_0}{\sqrt{1-\bar{\alpha}_t}},\sigma_t^2\boldsymbol{I}\right)$$

the new distribution ensure $q_\sigma(\boldsymbol{x}_t|\boldsymbol{x}_0)=\mathcal{N}(\sqrt{\bar{\alpha}_t}\boldsymbol{x}_0,(1-\bar{\alpha}_t)\boldsymbol{I})$, the prove can be seen in the Appendix B.

just using the property above $\boldsymbol{x}_t=\sqrt{\bar{\alpha}_t}\boldsymbol{x}_0+\sqrt{1-\bar{\alpha}_t}\boldsymbol{\epsilon}$

and we can sample $x_{t-1}$ by using below:
$$\boldsymbol{x} _{t-1}=\sqrt{\bar{\alpha} _{t-1}}\underbrace{\left(\frac{\boldsymbol{x} _{t}-\sqrt{1-\bar{\alpha} _{t}}\epsilon _{\theta}(\boldsymbol{x} _{t}, t)}{\sqrt{\bar{\alpha} _{t}}}\right)} _{\text{“ predicted }\boldsymbol{x} _{0}\text{”}} + \underbrace { \sqrt { 1 - \bar{\alpha}  _ { t - 1 }-\sigma _{t}^{2}}\cdot\epsilon _{\theta}(\boldsymbol{x} _{t}, t)} _{\text{“direction pointing to }\boldsymbol{x} _{t}\text{”}} + \underbrace { \sigma _ { t }\epsilon _{t}} _{\text{random noise}}$$

where $\epsilon_t\sim\mathcal{N}(\mathbf{0},\boldsymbol{I})$, and the author defines $\sigma_t=\eta\cdot\sqrt{(1-\bar{\alpha} _{t-1})/(1-\bar{\alpha} _t)}\sqrt{1-\alpha _t}$

if $\eta = 1$, we get DDPM

$$\boldsymbol{x} _{t-1}={\frac{1}{\sqrt{\alpha _{t}}}}\Big(\boldsymbol{x} _{t}-{\frac{1-\alpha _{t}}{\sqrt{1-\bar{\alpha} _{t}}}}\boldsymbol{\epsilon} _{\theta}(\boldsymbol{x} _{t},t)\Big) + \frac{1-\bar{\alpha} _{t-1}}{1-\bar{\alpha}_t}\(1-\alpha_t)\epsilon _{t}$$

if $\eta = 0$, we get DDIM 

$$\boldsymbol{x} _{t-1}={\frac{1}{\sqrt{\bar{\alpha} _t}}} (\boldsymbol{x} _{t} - \sqrt{1 - \alpha _{t}} \epsilon _{\theta}(\boldsymbol{x} _{t}, t))$$

*DDIM Accelerate explanation*

*The original forward process is obtained by using Markov properties, but the forward process of DDIM is not obtained by using Markov properties.(We use a new distribution to obtain)* -> $q_\sigma(\boldsymbol{x}_t|\boldsymbol{x}_0)=\mathcal{N}(\sqrt{\bar{\alpha}_t}\boldsymbol{x}_0,(1-\bar{\alpha}_t)\boldsymbol{I})$

*This shows that in fact, during training, you can not only use 1 as the step size for diffusion, but you can choose a larger step size (actually it has no influence for training, because training can spread in one step, so it is the same as DDPM training).*

Choosing a subset $\[\boldsymbol{x}_{\tau_1},\ldots,\boldsymbol{x} _{\tau_S}\]$ of $1,\ldots,T$

*Then the denoising process will use a larger step size for denoising, thereby obtaining an acceleration effect.*

### code of DDIM sample

```python

if self.args.skip_type == "uniform":
    skip = self.num_timesteps // self.args.timesteps
    seq = range(0, self.num_timesteps, skip)
elif self.args.skip_type == "quad":
    seq = (
        np.linspace(
            0, np.sqrt(self.num_timesteps * 0.8), self.args.timesteps
        )
        ** 2
    )
    seq = [int(s) for s in list(seq)]
else:
    raise NotImplementedError

xs = generalized_steps(x, seq, model, self.betas, eta=self.args.eta)

def compute_alpha(beta, t):
    beta = torch.cat([torch.zeros(1).to(beta.device), beta], dim=0)
    a = (1 - beta).cumprod(dim=0).index_select(0, t + 1).view(-1, 1, 1, 1)
    return a


def generalized_steps(x, seq, model, b, **kwargs):
    with torch.no_grad():
        n = x.size(0)
        seq_next = [-1] + list(seq[:-1])
        x0_preds = []
        xs = [x]
        for i, j in zip(reversed(seq), reversed(seq_next)):
            t = (torch.ones(n) * i).to(x.device)
            next_t = (torch.ones(n) * j).to(x.device)
            at = compute_alpha(b, t.long())
            at_next = compute_alpha(b, next_t.long())
            xt = xs[-1].to('cuda')
            et = model(xt, t)
            x0_t = (xt - et * (1 - at).sqrt()) / at.sqrt()
            x0_preds.append(x0_t.to('cpu'))
            c1 = (
                kwargs.get("eta", 0) * ((1 - at / at_next) * (1 - at_next) / (1 - at)).sqrt()
            )
            c2 = ((1 - at_next) - c1 ** 2).sqrt()
            xt_next = at_next.sqrt() * x0_t + c1 * torch.randn_like(x) + c2 * et
            xs.append(xt_next.to('cpu'))

    return xs, x0_preds
```

## 📖 [Improved Denoising Diffusion Probabilistic Models](https://arxiv.org/abs/2102.09672)

### Set learning Variance(denoising process) to improve log-likelihood

$v$ is a learnable parameter. In $v$, there is a numerical value in each dimension.

$$\Sigma_\theta(x_t,t)=\exp(v\log\beta_t+(1-v)\log\tilde{\beta}_t)$$

$$q(x_t|x_0)=\mathcal{N}(x_t;\sqrt{\bar{\alpha}_t}x_0,\color{red}{\beta_t} \mathbf{I})$$

$$q(x_{t-1}|x_t,x_0)=\mathcal{N}(x _{t-1};\tilde{\boldsymbol{\mu}}(x_t,x_0),\color{red}{\tilde{\boldsymbol{\beta}}_t} \mathbf{I})$$

So, using the objective below as the loss: $$L_{\text{hybrid}} = L _ {\text{simple}} + \lambda L _ {\text{vlb}}$$

$$L_\text{simple}=E _{t,x_0,\epsilon}\left[||\epsilon _t -\epsilon _\theta(x_t,t)||^2\right]$$

$$L _ {\text{vlb}}=\mathbb{E} _{t, x _0,\boldsymbol{\epsilon}}\Big[\frac{(1-\alpha _t)^2}{2\alpha_t(1-\bar{\alpha} _t)\|\boldsymbol{\Sigma} _\theta\|_2^2}\|\boldsymbol{\epsilon} _t-\boldsymbol{\epsilon} _\theta\big(x_t,t\big)\|^2\Big]$$

### Cosine noise schedule

$$\bar{\alpha}_t=\frac{f(t)}{f(0)},\quad f(t)=\cos\left(\frac{t/T+s}{1+s}\cdot\frac\pi2\right)^2$$

### Accelerate 

Sample using an arbitrary subsequence $S$ of $t$ values.

Compute $p(x_{S_{t-1}}|x_{S_{t}})$ as $\mathcal{N}(\mu_\theta(x_{S_t},S_t),\Sigma_\theta(x_{S_t},S_t))$, so it can accelerate the sample process.

*Why it could use the subsequence S of t. Because DDPM is actually a special case of DDIM, DDPM can use shorter step to sample. But the FID may not good enough, and the log-likelihood improved method above can support the shorter step sampling.*

<p align="center">
  <img src="https://github.com/digbangbang/Learning/assets/78746384/628105bf-9454-476d-8703-1b129763b423" alt="legend">
</p>





