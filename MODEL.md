# Model

The physics and numbers behind [Quasar Microlensing Lab](https://jameshhchan.github.io/quasar-microlensing/). Back to the [README](README.md).

All lengths are in units of $`\theta_E`$, the Einstein radius of a $`1\,M_\odot`$ star.

> **Note:** the setup here (star field size, ray-shooting region, ray counts and resolution) is chosen to run smoothly in a web browser, not to be optimal for research. For well-chosen parameters, please use [microcaustics](https://github.com/JFagin/microcaustics).

**Contents:** [Lens model](#lens-model) · [Magnification](#magnification) · [Star field and ray-shooting region](#star-field-and-ray-shooting-region) · [Stellar mass function](#stellar-mass-function) · [Motion](#motion) · [Source profiles](#source-profiles)

## Lens model

Each image of the quasar sits in a region of the lensing galaxy with total convergence $`\kappa`$ and external shear $`\gamma`$ (shear angle fixed at $`0^\circ`$). A fraction $`f_\star`$ of $`\kappa`$ is in stars ($`\kappa_\star = f_\star\,\kappa`$) and the rest is smooth matter ($`\kappa_s = \kappa - \kappa_\star`$). A light ray at lens-plane position $`\theta`$ reaches the source plane at

```math
\begin{aligned}
\beta_1 &= (1-\kappa_s-\gamma)\,\theta_1 - \sum_i m_i\,\frac{(\theta-\theta_i)_1}{|\theta-\theta_i|^2} \\
\beta_2 &= (1-\kappa_s+\gamma)\,\theta_2 - \sum_i m_i\,\frac{(\theta-\theta_i)_2}{|\theta-\theta_i|^2}
\end{aligned}
```

where star $`i`$ has mass $`m_i`$ (in $`M_\odot`$) and position $`\theta_i`$.

If all the mass were smooth, the image would be magnified by the **macro magnification**

```math
\mu_0 = \frac{1}{\left|(1-\kappa)^2 - \gamma^2\right|}
```

shown as ▼ on the colour bars. The image is a minimum if both $`1-\kappa\mp\gamma > 0`$, and a saddle point if $`(1-\kappa)^2 - \gamma^2 < 0`$.

## Magnification

**Source-plane map:** built by inverse ray shooting on the GPU. Rays are fired from a grid on the lens plane through the lens equation and counted where they land; $`\mu`$ in each pixel is the ray density there divided by the density with no lensing. Choose 1M, 4M or 16M rays per frame under Ray shooting. While paused, frames keep adding up and the map gets smoother.

**Lens-plane map:** the point-source magnification at each position, computed directly with no ray shooting:

```math
\mu(\theta) = \frac{1}{|\det A|},\qquad \det A = (1-\kappa_s)^2 - (\gamma - S_1)^2 - S_2^2,
```

```math
S_1 = \sum_i m_i\,\frac{\Delta\theta_1^2 - \Delta\theta_2^2}{|\Delta\theta|^4},\qquad
S_2 = \sum_i m_i\,\frac{2\,\Delta\theta_1\,\Delta\theta_2}{|\Delta\theta|^4},\qquad
\Delta\theta = \theta - \theta_i .
```

**Critical curves** are where $`\det A = 0`$ on the lens plane; **caustics** are where they land on the source plane.

**Light curves:** for each source, $`\mu`$ is its total lensed flux divided by its unlensed flux,

```math
\mu = \frac{\int I(\beta(\theta))\,d^2\theta}{\int I(\beta)\,d^2\beta},
```

computed every frame by sampling the source through the lens equation over the ray-shooting region. In magnitudes, $`\Delta m = -2.5\log_{10}(\mu/\mu_0)`$, so negative is brighter than the smooth-mass prediction.

## Star field and ray-shooting region

Stars fill a circle of radius

```math
R_\star = \max\!\left(1.2\sqrt{L_x^2 + L_y^2},\ 8\sqrt{2}\right) + 3,
```

which encloses the ray-shooting region, a rectangle of half-widths

```math
L_{x,y} = \frac{1.25\,L_s}{|1-\kappa \mp \gamma|} + 3.5,
```

where $`L_s`$ is the magnification map half-width. The region is stretched along $`x`$ because rays from a wide strip of the lens plane all land in the small source field. The number of stars is set so their total mass gives the chosen $`\kappa_\star`$:

```math
N = \frac{\kappa_\star R_\star^2}{\langle m \rangle}.
```

Positions are uniform in the circle. Set how much of each plane you see under **View**, or use **Whole field** below each plane.

## Stellar mass function

- **Equal:** every star has $`m = 1\,M_\odot`$.
- **Salpeter:** $`dN/dm \propto m^{-2.35}`$, between $`0.1`$ and $`1\,M_\odot`$ ($`\langle m \rangle \approx 0.22\,M_\odot`$) or $`0.1`$ and $`10\,M_\odot`$ ($`\langle m \rangle \approx 0.31\,M_\odot`$).
- **Log-uniform:** $`dN/d\log m = \text{constant}`$ between $`0.1`$ and $`10\,M_\odot`$ ($`\langle m \rangle \approx 2.15\,M_\odot`$).

The small plot under the menu compares the stars drawn in this field with the expected distribution, with the mean $`\langle m \rangle`$ and standard deviation $`\sigma`$ of both.

## Motion

Each star moves with its own random velocity plus a common drift:

```math
\mathbf{v}_i = \sigma_v\,\mathbf{u}_i + \mathbf{v}_{\rm drift},
```

where $`\mathbf{u}_i`$ is drawn from a 2D unit Gaussian. Velocities are in $`\theta_E`$ per time unit. At the edge of the star field, stars either bounce back inward or wrap around to the opposite side. Positions, masses and velocities all come from the random seed, so the same seed and settings give the same star field.

## Source profiles

**Gaussian:** $`I(r) \propto \exp\left(-r^2/2\sigma^2\right)`$, with half-light radius $`r_{1/2} = \sqrt{2\ln 2}\,\sigma \approx 1.18\,\sigma`$.

**Accretion disk:** a face-on thin disk at a single wavelength,

```math
I(r) \propto \frac{1}{e^{\xi}-1},\qquad \xi = \left(\frac{r}{s}\right)^{3/4}\left(1-\sqrt{\frac{r_{\rm in}}{r}}\right)^{-1/4},
```

where $`s`$ is the disk scale and $`r_{\rm in}`$ is the ISCO radius; there is no light inside the ISCO. Setting the disk scale to $`0`$ removes the disk.

**Broad-line region (BLR):** a ring at radius $`R_{\rm BLR}`$, with a Gaussian cross-section of width $`0.15\,R_{\rm BLR}`$ and peak brightness $`0.28`$ of the disk's peak. Setting $`R_{\rm BLR}`$ to $`0`$ removes it.

**Image:** any picture, using its brightness (luminance) as the source's light.

The light profile plot shows the mean brightness against radius (linear or log), with the **half-light radius** $`r_{1/2}`$, the radius that encloses half of the light, marked by a dashed line.

The optional comparison curves in the light curve are Gaussian sources with $`\sigma = 0.02`$, $`0.1`$ and $`0.4\,\theta_E`$, placed at the position of the first source (normally A).
