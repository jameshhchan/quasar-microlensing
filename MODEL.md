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

**Source-plane map:** made on the GPU from the ray-shooting region of the lens plane, by either of two methods (Map method):

- **Inverse ray shooting (IRS):** rays are fired from a grid of points on the lens plane, mapped through the lens equation, and counted where they land. $`\mu`$ in each pixel is the number of rays there divided by the number with no lensing. Simple and robust, but noisy unless many rays are used. Choose 1M, 4M or 16M rays per frame; while paused, frames add up and the map gets smoother.
- **Inverse polygon mapping (IPM):** the lens plane is cut into small triangles instead of points, and each triangle's corners are mapped through the lens equation. Each triangle's area is spread over its image, so a pixel gets $`\mu = A_{\rm lens}/A_{\rm image}`$ from every image that covers it (images smaller than a pixel go into that pixel whole). Because the triangles cover the source plane without gaps, the map is smooth with far fewer samples, which helps most while the stars move.

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

Positions are uniform in the circle. Scroll to zoom into either plane, or use **Whole field** below each plane to see all of it.

## Stellar mass function

Every mass function except equal mass is drawn between a lowest and a highest mass, set with the two handles of the mass-range slider (log scale, $`0.01`$ to $`100\,M_\odot`$). The default range is $`0.1`$–$`1\,M_\odot`$, which suits the old stellar populations of typical lens galaxies, where heavier stars have already burned out.

- **Equal:** every star has the same mass, $`1\,M_\odot`$ by default (the slider then has one handle).
- **Salpeter:** $`dN/dm \propto m^{-2.35}`$ ($`\langle m \rangle \approx 0.22\,M_\odot`$ for $`0.1`$–$`1\,M_\odot`$).
- **Chabrier:** the Chabrier (2003) single-star function, log-normal below $`1\,M_\odot`$ and a power law above, joined smoothly at $`1\,M_\odot`$ ($`\langle m \rangle \approx 0.31\,M_\odot`$ for $`0.1`$–$`1\,M_\odot`$):

```math
\frac{dN}{d\log m} \propto \begin{cases} \exp\!\left[-\dfrac{(\log m - \log 0.079)^2}{2\,(0.69)^2}\right], & m \le 1\,M_\odot \\[6pt] m^{-1.3}, & m > 1\,M_\odot \end{cases}
```

- **Kroupa:** the Kroupa (2001) broken power law, continuous at both breaks ($`\langle m \rangle \approx 0.32\,M_\odot`$ for $`0.1`$–$`1\,M_\odot`$):

```math
\frac{dN}{dm} \propto \begin{cases} m^{-0.3}, & m < 0.08\,M_\odot \\[2pt] m^{-1.3}, & 0.08 \le m < 0.5\,M_\odot \\[2pt] m^{-2.3}, & m \ge 0.5\,M_\odot \end{cases}
```

- **Log-uniform:** $`dN/d\log m = \text{constant}`$ (choosing it from a range that tops out at $`1\,M_\odot`$ switches to $`0.1`$–$`10\,M_\odot`$, $`\langle m \rangle \approx 2.15\,M_\odot`$).

Salpeter masses come from the exact inverse of its cumulative distribution and the others from a finely tabulated one in $`\log m`$, with one random number per star, so the same seed gives the same field. The number of stars is $`N = \kappa_\star R_\star^2 / \langle m \rangle`$, up to 512; a very low lowest mass can hit that cap, and the app then warns that $`\kappa_\star`$ is below the set value.

The small plot under the mass range compares the stars drawn in this field with the expected distribution, with the mean $`\langle m \rangle`$ and standard deviation $`\sigma`$ of both.

## Motion

Each star moves with its own random velocity plus a common drift:

```math
\mathbf{v}_i = \sigma_v\,\mathbf{u}_i + \mathbf{v}_{\rm drift},
```

where $`\mathbf{u}_i`$ is drawn from a 2D unit Gaussian. Velocities are in $`\theta_E`$ per time unit. At the edge of the star field, stars either reflect elastically like a ball off a wall ($`\mathbf{v} \to \mathbf{v} - 2(\mathbf{v}\cdot\hat{\mathbf{n}})\,\hat{\mathbf{n}}`$, with $`\hat{\mathbf{n}}`$ the outward normal of the edge; the default), wrap around to the opposite side, or simply leave the field, which then slowly empties. Positions, masses and velocities all come from the random seed, so the same seed and settings give the same star field.

## Source profiles

**Gaussian:** $`I(r) \propto \exp\left(-r^2/2\sigma^2\right)`$, with half-light radius $`r_{1/2} = \sqrt{2\ln 2}\,\sigma \approx 1.18\,\sigma`$.

**Accretion disk:** a thin disk at a single wavelength,

```math
I(r) \propto \frac{1}{e^{\xi}-1},\qquad \xi = \left(\frac{r}{s}\right)^{3/4}\left(1-\sqrt{\frac{r_{\rm in}}{r}}\right)^{-1/4},
```

where $`s`$ is the disk scale and $`r_{\rm in}`$ is the **inner edge** (the ISCO); there is no light inside it. Setting the disk scale to $`0`$ removes the disk.

The disk is seen at **inclination** $`i`$ (0° is face-on), with its major axis along **orientation** $`\theta`$, measured counter-clockwise from the $`+x`$ axis of the source plane. Without GR, $`\theta`$ and $`\theta + 180^\circ`$ look the same, so $`\theta`$ runs 0–179°; with GR on it runs 0–360° and points to the side moving toward us. A point $`(x, y)`$ on the source plane maps to the disk plane as

```math
u = x\cos\theta + y\sin\theta, \qquad v = \frac{-x\sin\theta + y\cos\theta}{\cos i}, \qquad r = \sqrt{u^2 + v^2}.
```

**GR effects** (as in Caustic Crossing) add the Doppler boost and gravitational redshift of a disk on circular orbits around a black hole of spin $`a`$. In units of the gravitational radius $`r_g = GM/c^2`$, with $`R = r/r_g`$ and $`U = u/r_g`$, the orbital angular velocity and time component of the 4-velocity are

```math
\Omega = \frac{1}{R^{3/2} + a}, \qquad u^t = \frac{R^{3/2} + a}{R^{3/4}\sqrt{R^{3/2} - 3R^{1/2} + 2a}},
```

and light from each point is shifted by

```math
g = \frac{\nu_{\rm obs}}{\nu_{\rm em}} = \frac{1}{u^t\,(1 - \Omega \sin i \; U)}, \qquad I \propto \frac{g^3}{\exp(\xi/g) - 1}.
```

The side at $`\theta`$ moves toward us, so it is brighter and bluer.

**This app uses $`a = 0`$** (a non-spinning black hole). The app measures sizes in $`\theta_E`$ and has no black-hole mass, so it takes the inner edge to be the ISCO of a non-spinning hole, $`r_{\rm in} = 6\,r_g`$, which sets $`r_g = r_{\rm in}/6`$. The formulas then reduce to

```math
\Omega = R^{-3/2}, \qquad u^t = \frac{1}{\sqrt{1 - 3/R}}, \qquad g = \frac{\sqrt{1 - 3/R}}{1 - \sin i\,\cos\phi/\sqrt{R}},
```

where $`\phi`$ is the angle around the disk from $`\theta`$. At a given radius, spin changes $`g`$ only slightly (under about 7% at $`6\,r_g`$, under 1% at $`12\,r_g`$); its main effect is to let the disk reach inside $`6\,r_g`$ (down to $`1.24\,r_g`$ for $`a = 0.998`$), where the effects are much stronger. Light is assumed to travel in straight lines (no light bending), which is accurate far from the black hole and approximate within a few $`r_g`$. GR effects need an inner edge; with $`r_{\rm in} = 0`$ they are off. The BLR ring lies in the same plane as the disk, so it is inclined too.

**Broad-line region (BLR):** a ring at radius $`R_{\rm BLR}`$, with a Gaussian cross-section of width $`0.15\,R_{\rm BLR}`$ and peak brightness $`0.28`$ of the disk's peak. Setting $`R_{\rm BLR}`$ to $`0`$ removes it.

**Image:** any picture, using its brightness (luminance) as the source's light.

The light profile plot shows the mean brightness against radius (linear or log), with the **half-light radius** $`r_{1/2}`$, the radius that encloses half of the light, marked by a dashed line.

The optional comparison curves in the light curve are Gaussian sources with $`\sigma = 0.02`$, $`0.1`$ and $`0.4\,\theta_E`$, placed at the position of the first source (normally A).
