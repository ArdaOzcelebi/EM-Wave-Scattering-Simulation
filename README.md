# ⚡ Electromagnetic Wave Scattering & Method of Moments (MoM) Simulator

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Zero Dependencies](https://img.shields.io/badge/Dependencies-Zero%20(Pure%20Vanilla%20JS)-emerald.svg)]()
[![KaTeX Powered](https://img.shields.io/badge/Math-KaTeX%20Rendered-ffc857.svg)](https://katex.org/)
[![HTML5 Canvas](https://img.shields.io/badge/Graphics-HTML5%20Canvas%202D%20%26%203D-00c8ff.svg)]()

An interactive, scientifically rigorous, and visually stunning web-based physics simulation suite demonstrating:
1. **How incident electromagnetic waves induce electric currents on conducting surfaces.**
2. **How those induced surface currents re-radiate to form scattered fields.**
3. **The computational formulation of the Electric Field Integral Equation (EFIE) solved via the Method of Moments (MoM).**
4. **The transition between Perfect Electric Conductors (PEC) and real conductors with finite skin depth ($\delta$).**

---

## 🌟 Interactive Simulations

The repository features two complementary simulation environments:

### 🔬 [Page 1: 2D Field Laboratory & MoM Solver (`index.html`)](index.html)
- **Real-Time Wave Propagation**: Adjust frequency, incident angle ($\theta_i \in [0^\circ, 70^\circ]$), amplitude, and simulation speed.
- **Polarization Control**: Toggle between TM (transverse magnetic) and TE (transverse electric) polarizations.
- **True Skin Depth Physics**: Slide from ideal PEC ($\sigma \to \infty$) to real conductors ($\delta = \sqrt{2/\omega\mu\sigma}$), observing volume current decay and magnetic field penetration.
- **Virtual Field Probe**: Hover or drag across the simulation canvas to inspect localized $\mathbf{E}$, $\mathbf{H}$, and instantaneous Poynting vector flux $\mathbf{S} = \mathbf{E} \times \mathbf{H}$.
- **Interactive Method of Moments Solver**: Solve an interactive patch-based impedance matrix equation $\mathbf{Z}\mathbf{I} = \mathbf{V}$ with live coupling distance controls and error comparison against uncoupled naive solutions.
- **Crisp HiDPI / Retina Graphics**: Automatically adapts to device pixel ratios for sharp vector rendering.

### 🌐 [Page 2: 3D Perspective Wave & Surface Story (`simulation2.html`)](simulation2.html)
- **True 3D Perspective Canvas**: View orthogonal $\mathbf{E}$ (cyan) and $\mathbf{H}$ (green) sinusoids traveling in space with interconnected vector fins and Poynting direction vectors.
- **Full 3D Orbit Controls**: Click and drag to orbit in yaw and pitch, scroll to zoom, and touch gesture support for mobile/tablet.
- **4-Phase Auto-Cycling Narration**:
  - **Phase 1 — Incident Field**: Plane wave propagation in free space.
  - **Phase 2 — Induced Surface Current**: Lorentz force driving mobile electrons $\mathbf{J}_s = \hat{n} \times \mathbf{H}_{\text{total}}$.
  - **Phase 3 — Scattered Field**: Hertzian dipole re-radiation of secondary waves into the upper hemisphere.
  - **Phase 4 — Total Superposition & Standing Waves**: Field cancellation at the PEC surface ($\hat{n} \times \mathbf{E}_{\text{total}} = 0$) and standing wave interference nodes.
- **Interactive Step Navigation**: Jump to any phase, pause/resume narration, or control cycle speeds.

---

## 📐 Theoretical Framework

```
                          [ Incident Field Eᵢ, Hᵢ ]
                                      │
                                      ▼  (Strikes Conductor Boundary S)
           Lorentz Force: F = q(E + v × B) ──> Free Electron Acceleration
                                      │
                                      ▼
                        [ Induced Surface Current Jₛ ]
                                      │
                           Jₛ = n̂ × (H₁ - H₂) = n̂ × H_total
                                      │
                                      ▼  (Every patch radiates as Hertzian dipole)
                        [ Scattered Secondary Field Eₛ ]
                                      │
                   Eₛ(r) = -jωμ ∬ G(r,r') J(r') dS' - ∇(...)
                                      │
                                      ▼
                      [ PEC Boundary Condition at Surface S ]
                               n̂ × (Eᵢ + Eₛ) = 0
                                      │
                                      ▼  (Discretize with RWG basis functions)
                      [ Method of Moments Matrix System ]
                                  Z · I = V
```

### 1. The Incident Electromagnetic Wave
An incident plane wave propagating along wave vector $\mathbf{k}$ with angular frequency $\omega = 2\pi f$:
$$\vec{E}_i(\mathbf{r}, t) = E_0 \cos(\mathbf{k} \cdot \mathbf{r} - \omega t)\,\hat{e}$$
$$\vec{H}_i(\mathbf{r}, t) = \frac{E_0}{\eta} \cos(\mathbf{k} \cdot \mathbf{r} - \omega t)\,(\hat{k} \times \hat{e})$$
where $\eta = \sqrt{\mu/\varepsilon} \approx 376.73\,\Omega$ is the intrinsic wave impedance of free space.

### 2. Boundary Conditions and Current Induction
From Maxwell-Ampère's law in integral form across an infinitesimally thin pillbox across boundary surface $S$:
$$\hat{n} \times (\vec{H}_1 - \vec{H}_2) = \vec{J}_s$$

For a **Perfect Electric Conductor (PEC)**:
- Inside the metal, $\sigma \to \infty \implies \vec{E}_2 = 0$ and $\vec{H}_2 = 0$.
- The boundary condition collapses strictly to:
$$\vec{J}_s = \hat{n} \times \vec{H}_{\text{total}}$$
where $\hat{n}$ is the outward surface normal.

For **real conductors** with finite conductivity $\sigma$:
The wave penetrates with exponential decay characterized by the skin depth:
$$\delta = \sqrt{\frac{2}{\omega \mu \sigma}}$$
producing a volume current density $\vec{J}_v(y) = \sigma \vec{E}(y)$ that transitions into an effective surface current as $\omega \to \infty$.

### 3. Scattered Radiation & The Free-Space Green's Function
Each infinitesimal element of induced current acts as a point source radiating spherical electromagnetic waves:
$$G(\mathbf{r}, \mathbf{r}') = \frac{e^{-jk|\mathbf{r} - \mathbf{r}'|}}{4\pi|\mathbf{r} - \mathbf{r}'|}$$

The total scattered electric field is given by:
$$\vec{E}_s(\mathbf{r}) = -j\omega\mu \iint_S G(\mathbf{r}, \mathbf{r}')\,\vec{J}(\mathbf{r}')\,dS' - \nabla \left( \frac{1}{j\omega\varepsilon} \iint_S G(\mathbf{r}, \mathbf{r}')\,\nabla' \cdot \vec{J}(\mathbf{r}')\,dS' \right)$$

### 4. Electric Field Integral Equation (EFIE) and the Method of Moments (MoM)
Enforcing the tangential electric field boundary condition on the PEC:
$$\hat{n} \times \left( \vec{E}_i(\mathbf{r}) + \vec{E}_s(\mathbf{r}) \right) = 0 \quad \forall \mathbf{r} \in S$$
$$\implies \hat{n} \times \vec{E}_s(\mathbf{r}) = -\hat{n} \times \vec{E}_i(\mathbf{r})$$

To solve for the unknown continuous current distribution $\vec{J}(\mathbf{r}')$, we discretize $S$ into $N$ basis functions (e.g. Rao-Wilton-Glisson / RWG basis functions):
$$\vec{J}(\mathbf{r}') \approx \sum_{n=1}^{N} I_n\,\vec{f}_n(\mathbf{r}')$$

Applying Galerkin testing with testing functions $\vec{f}_m(\mathbf{r})$ yields the dense matrix equation:
$$\sum_{n=1}^{N} Z_{mn}\,I_n = V_m \quad \Longleftrightarrow \quad \mathbf{Z}\,\mathbf{I} = \mathbf{V}$$

where:
- **Impedance Matrix Element**:
  $$Z_{mn} = j\omega\mu \iint_S \iint_S \vec{f}_m(\mathbf{r}) \cdot G(\mathbf{r}, \mathbf{r}')\,\vec{f}_n(\mathbf{r}')\,dS'\,dS + \text{charge term}$$
- **Excitation Vector Element**:
  $$V_m = \iint_S \vec{f}_m(\mathbf{r}) \cdot \vec{E}_i(\mathbf{r})\,dS$$

Solving the linear system $\mathbf{I} = \mathbf{Z}^{-1}\mathbf{V}$ gives the exact induced currents, enabling computation of the **Radar Cross Section (RCS)**, scattered far-field patterns, and antenna radiation metrics.

---

## 🚀 Quick Start

### Option 1: Direct in Browser (Zero Installation)
Simply double-click [`index.html`](index.html) or [`simulation2.html`](simulation2.html) in any modern web browser (Chrome, Edge, Firefox, Safari).

### Option 2: Local HTTP Server
Using Node.js:
```bash
npx -y serve . --listen 5500
```
Or using Python 3:
```bash
python -m http.server 5500
```
Then navigate to:
- **Laboratory**: [http://localhost:5500/](http://localhost:5500/)
- **3D Story**: [http://localhost:5500/simulation2.html](http://localhost:5500/simulation2.html)

---

## ⌨️ Keyboard Shortcuts

| Shortcut | Action |
|:---:|:---|
| <kbd>Space</kbd> | Toggle Play / Pause simulation |
| <kbd>R</kbd> | Reset time and simulation parameters |
| <kbd>C</kbd> | Toggle Induced Surface Currents ($\mathbf{J}_s$) |
| <kbd>S</kbd> | Toggle Scattered Radiation ($\mathbf{E}_s$) |
| <kbd>P</kbd> | Toggle Virtual Field Probe & Poynting Sensor |
| <kbd>←</kbd> / <kbd>→</kbd> | Step Previous / Next story phase (in 3D mode) |
| <kbd>1</kbd> / <kbd>2</kbd> | Switch between 2D Lab and 3D Story |

---

## 🛠 Project Structure

```text
├── index.html          # 2D Interactive Field Laboratory & MoM Solver
├── simulation2.html    # Fullscreen 3D Perspective Wave & Surface Story
├── package.json        # NPM metadata and local serve scripts
├── README.md           # Documentation, theoretical derivations & guide
├── LICENSE             # MIT License
└── .gitignore          # Git exclusion rules
```

---

## 📚 References & Further Reading

1. **Balanis, Constantine A.** *Advanced Engineering Electromagnetics*. John Wiley & Sons, 2nd Edition, 2012.
2. **Harrington, Roger F.** *Field Computation by Moment Methods*. IEEE Press / Wiley-IEEE, 1993.
3. **Rao, S. M., Wilton, D. R., & Glisson, A. W.** "Electromagnetic scattering by surfaces of arbitrary shape." *IEEE Transactions on Antennas and Propagation*, 30(3), 409-418, 1982.
4. **Gibson, Walton C.** *The Method of Moments in Electromagnetics*. CRC Press, 3rd Edition, 2021.

---

## 📄 License

This project is licensed under the [MIT License](LICENSE) — feel free to use, modify, and distribute for educational, academic, and commercial applications.
