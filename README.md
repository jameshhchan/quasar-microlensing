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

The app solves the lens equation for a field of moving stars embedded in smooth matter with external shear, maps the magnification by inverse ray shooting on the GPU, and integrates each source's light through the lens to get its light curve. For the equations, the star field and ray-shooting setup, the stellar mass functions and the source profiles, see **[How it works](HOW_IT_WORKS.md)**.

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
