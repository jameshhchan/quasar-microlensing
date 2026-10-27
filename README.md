# Quasar Microlensing Lab

**▶ Open the app: [jameshhchan.github.io/quasar-microlensing](https://jameshhchan.github.io/quasar-microlensing/)**

Visualizing microlensing light curves of lensed quasars.

Stars in a lensing galaxy split each image of a lensed quasar into many micro-images. As the stars move, their network of caustics sweeps across the quasar's accretion disk and its brightness flickers. This page lets you watch that happen and see how the light curve depends on the lens model and on the size of the source. It runs entirely in your web browser, with nothing to install.

New here? Click **Take the tour** at the top of the page for a guided walk-through.

## What you can do

- **Source plane:** see the magnification map and its caustics, drag sources across it, and choose a colour scheme (Ocean, Magma, Grey, Viridis or Cosmic).
- **Lens plane:** watch the stars move, with their critical curves and the distorted micro-images of each source. Outline the star field and the ray-shooting region, or zoom out to see the whole star field.
- **Sources:** add up to four sources, each with its own colour and light curve. Choose a Gaussian, an accretion disk with an ISCO hole and a broad-line region, or an image of your own. A small plot shows each source's light profile and half-light radius.
- **Lens model:** set the convergence κ, shear γ, fraction of mass in stars and stellar mass function (equal, Salpeter or log-uniform). Compare the stars drawn with the expected mass distribution, and use a seed to bring back a star field.
- **Light curve:** follow each source's magnification over time, in μ or in Δm = −2.5 log(μ/μ₀), with reference curves for Gaussian sources of different sizes.
- **Explore a moment:** pause, then click any time on the light curve to see both planes at that moment, or drag the time line to move through time. Drag across the plot to zoom in and use the slider to look further back.

## How it works

All lengths are in units of θ<sub>E</sub>, the Einstein radius of a 1 M☉ star. Click a topic to expand it.

<details>
<summary><b>Lens model</b></summary>

Each image of the quasar sits in a region of the lensing galaxy with total convergence κ and external shear γ (shear angle fixed at 0°). A fraction f⋆ of κ is in stars (κ⋆ = f⋆ κ) and the rest is smooth matter (κ<sub>s</sub> = κ − κ⋆). A light ray at lens-plane position θ reaches the source plane at

```math
\begin{aligned}
\beta_1 &= (1-\kappa_s-\gamma)\,\theta_1 - \sum_i m_i\,\frac{(\theta-\theta_i)_1}{|\theta-\theta_i|^2} \\
\beta_2 &= (1-\kappa_s+\gamma)\,\theta_2 - \sum_i m_i\,\frac{(\theta-\theta_i)_2}{|\theta-\theta_i|^2}
\end{aligned}
```

where star *i* has mass m<sub>i</sub> (in M☉) and position θ<sub>i</sub>.

If all the mass were smooth, the image would be magnified by the **macro magnification**

```math
\mu_0 = \frac{1}{\left|(1-\kappa)^2 - \gamma^2\right|}
```

shown as ▼ on the colour bars, and the image is a minimum (both 1 − κ ∓ γ > 0) or a saddle point (determinant < 0).

</details>

<details>
<summary><b>Magnification</b></summary>

- **Source-plane map:** built by inverse ray shooting on the GPU. Rays are fired from a grid on the lens plane through the lens equation and counted where they land; μ in each pixel is the ray density there divided by the density with no lensing. Choose 1M, 4M or 16M rays per frame under Ray shooting. While paused, frames keep adding up and the map gets smoother.
- **Lens-plane map:** the point-source magnification at each position, computed directly with no ray shooting:

  ```math
  \mu(\theta) = \frac{1}{|\det A|},\qquad \det A = (1-\kappa_s)^2 - (\gamma - S_1)^2 - S_2^2,
  ```

  with S<sub>1</sub> = Σ m<sub>i</sub> (Δθ₁² − Δθ₂²)/|Δθ|⁴ and S<sub>2</sub> = Σ 2 m<sub>i</sub> Δθ₁ Δθ₂/|Δθ|⁴, where Δθ = θ − θ<sub>i</sub>.
- **Critical curves** are where det A = 0 on the lens plane; **caustics** are where they land on the source plane.
- **Light curves:** for each source, μ is its total lensed flux divided by its unlensed flux,

  ```math
  \mu = \frac{\int I(\beta(\theta))\,d^2\theta}{\int I(\beta)\,d^2\beta},
  ```

  computed every frame by sampling the source through the lens equation over the ray-shooting region. In magnitudes, Δm = −2.5 log₁₀(μ/μ₀), so negative is brighter than the smooth-mass prediction.

</details>

<details>
<summary><b>Star field and ray-shooting region</b></summary>

Stars fill a circle of radius

```math
R_\star = \max\!\left(1.2\sqrt{L_x^2 + L_y^2},\ 8\sqrt{2}\right) + 3,
```

which encloses the ray-shooting region, a rectangle of half-widths

```math
L_{x,y} = \frac{1.25\,L_s}{|1-\kappa \mp \gamma|} + 3.5,
```

where L<sub>s</sub> is the magnification map half-width. The region is stretched along x because rays from a wide strip of the lens plane all land in the small source field. The number of stars is set so their total mass gives the chosen κ⋆:

```math
N = \frac{\kappa_\star R_\star^2}{\langle m \rangle}.
```

Positions are uniform in the circle. Set how much of each plane you see under **View**, or use **Whole field** below each plane.

</details>

<details>
<summary><b>Stellar mass function</b></summary>

- **Equal:** every star has m = 1 M☉.
- **Salpeter:** dN/dm ∝ m<sup>−2.35</sup>, between 0.1 and 1 M☉ (⟨m⟩ ≈ 0.22 M☉) or 0.1 and 10 M☉ (⟨m⟩ ≈ 0.31 M☉).
- **Log-uniform:** dN/d log m = constant between 0.1 and 10 M☉ (⟨m⟩ ≈ 2.15 M☉).

The small plot under the menu compares the stars drawn in this field with the expected distribution, with the mean ⟨m⟩ and standard deviation σ of both.

</details>

<details>
<summary><b>Motion</b></summary>

Each star moves with its own random velocity plus a common drift:

```math
\mathbf{v}_i = \sigma_v\,\mathbf{u}_i + \mathbf{v}_{\rm drift},
```

where **u**<sub>i</sub> is drawn from a 2D unit Gaussian. Velocities are in θ<sub>E</sub> per time unit. At the edge of the star field, stars either bounce back inward or wrap around to the opposite side. Positions, masses and velocities all come from the random seed, so the same seed and settings give the same star field.

</details>

<details>
<summary><b>Source profiles</b></summary>

- **Gaussian:** I(r) ∝ exp(−r²/2σ²), with half-light radius r<sub>½</sub> ≈ 1.18 σ.
- **Accretion disk:** a face-on thin disk at a single wavelength,

  ```math
  I(r) \propto \frac{1}{e^{\xi}-1},\qquad \xi = \left(\frac{r}{s}\right)^{3/4}\left(1-\sqrt{\frac{r_{\rm in}}{r}}\right)^{-1/4},
  ```

  where s is the disk scale and r<sub>in</sub> is the ISCO radius; there is no light inside the ISCO. Setting the disk scale to 0 removes the disk.
- **Broad-line region (BLR):** a ring at radius R<sub>BLR</sub>, with a Gaussian cross-section of width 0.15 R<sub>BLR</sub> and peak brightness 0.28 of the disk's peak. Setting R<sub>BLR</sub> to 0 removes it.
- **Image:** any picture, using its brightness (luminance) as the source's light.

The light profile plot shows the mean brightness against radius (linear or log), with the **half-light radius**, the radius that encloses half of the light, marked by a dashed line.

The optional comparison curves in the light curve are Gaussian sources with σ = 0.02, 0.1 and 0.4 θ<sub>E</sub>, placed at the position of the first source (normally A).

</details>

## Requirements

A recent desktop browser (Chrome, Edge, Firefox or Safari) with WebGL 2. A computer with a dedicated graphics card runs it most smoothly. On slower machines, choose "Fast" under Ray shooting.

## Note

This website is for demonstration and educational purposes. For scientific research, please use [microcaustics](https://github.com/JFagin/microcaustics).

## Sharing

Please share the app, link to it freely, and use screenshots or screen recordings in talks, teaching materials, papers and posts, with credit to "Quasar Microlensing Lab by James Chan" and a link to the website. See [LICENSE](LICENSE).

Scan to open the app:

<img src="quasar-microlensing-qr.png" alt="QR code linking to jameshhchan.github.io/quasar-microlensing" width="180">

Feel free to use this QR code on slides, posters and handouts. Download it as [PNG](https://raw.githubusercontent.com/jameshhchan/quasar-microlensing/main/quasar-microlensing-qr.png) or [SVG](https://raw.githubusercontent.com/jameshhchan/quasar-microlensing/main/quasar-microlensing-qr.svg) (SVG stays sharp at any size).

## Contact

Created by [James Chan](https://sites.google.com/view/jameshhchan/home). If you have any questions, please reach out to me: [jchan@amnh.org](mailto:jchan@amnh.org)

This project is supported by [FutureLens](https://futurelens-cuny.github.io).

© 2026 James Chan. All rights reserved.
