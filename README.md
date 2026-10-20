# Franck-Condon-Overlap-Factor-Private

`index.html` is a single-page web app for Franck–Condon overlap factors between two harmonic potential energy surfaces
(ground state g, excited state e). Notation follows J. L. McHale, *Molecular Spectroscopy*, 2nd ed. (CRC Press, 2017).

**Inputs:** ω_g and ω_e (cm⁻¹), the displacement d = R_e,0 − R_g,0 (Å, default 0), the reduced mass μ (amu; the force
constants k = μω² follow from it), the starting level v″ and how many excited levels to show. v″ is a menu: v″ = 0 is
the cold ground state, v″ ≥ 1 a hot state, and each option gives its Boltzmann population at 298 K. Clicking a ground
level in the PES plot also makes it the start. Buttons swap ω_g ↔ ω_e and load four examples (same R₀, displaced, both, identical states). Everything
recalculates as you type or drag.

**Output:**

- The two PES with their vibrational levels and the wavefunctions χ_v drawn on each level. Vertical excitation arrows go
  from v″ to every v′, each one as wide as its FCF. Every excited level is shaded with its overlap integrand χ′χ″ (blue
  where it adds, red where it cancels), all on one scale; the selected level is shaded darker.
- A table to the right: for each v′ the analytical overlap ⟨χ′_v′|χ″_v″⟩, its value, the FCF and the % of the whole
  transition (Σ over all v′ is 1 by completeness, so % = 100 × FCF; a footer says how much goes to levels not shown).
  Rows are labelled **parity forbidden** (d = 0 and v′ + v″ odd), **orthogonal** (exactly zero: parity, or identical
  states with v′ ≠ v″) and **≈ orthogonal** (FCF < 10⁻⁶ for any other reason).
- The overlap integrand of the selected pair (both wavefunctions and their product, with the blue and red areas), with a
  menu listing every v″ → v′ integrand and its FCF, and
  the stick spectrum: FCF against ν̃ − ν̃₀₀ = v′ω_e − v″ω_g.
- All the equations at the bottom, rendered with KaTeX: potentials, levels, wavefunctions (χ_v = N_v H_v(α^½ q) e^(−αq²/2),
  α = μω/ħ), the Condon approximation, the sum rule, parity and orthogonality, McHale's same-R₀ problem worked through
  (FC₀₀ = 2(ω_gω_e)^½/(ω_g+ω_e), FC₁₀ = 0, FC₂₀ = (ω_gω_e)^½/(ω_e+ω_g) · ((ω_e−ω_g)/(ω_e+ω_g))²), the Poisson
  progression, and the general closed form below.

## The general formula

From the Hermite generating function, for any ω_g, ω_e, d and any v′ = m, v″ = n:

⟨χ′_m|χ″_n⟩ = I₀₀ √(m! n! / 2^(m+n)) Σ_k γ^k/k! · P_(m−k) Q_(n−k),

with P_j = Σ_i ρ^i b′^(j−2i) / (i!(j−2i)!), Q_j = Σ_i (−ρ)^i b″^(j−2i) / (i!(j−2i)!), ρ = (ω_e−ω_g)/(ω_e+ω_g),
γ = 2√(1−ρ²), b′ = −2α_e^½ α_g d/(α_g+α_e), b″ = 2α_g^½ α_e d/(α_g+α_e) and
I₀₀ = (2√(ω_gω_e)/(ω_g+ω_e))^½ exp(−α_gα_e d²/2(α_g+α_e)). The table writes this out as a polynomial for each row
(exact rational coefficients). The page also integrates the two wavefunctions numerically (trapezoid rule, 6001 points)
and prints the largest difference under the table, typically below 10⁻¹⁵.

## Use

Open `index.html` in a browser. It needs no server, no build step and no network: KaTeX is bundled in `vendor/katex/`.
If KaTeX cannot load, the page still works and shows the LaTeX source. To publish it, serve the repository root from any
static host, for example GitHub Pages with "Deploy from a branch" set to the repository root.

## Files

| path | what |
|---|---|
| `index.html` | the app: markup, the math core (`<script id="core">`), the UI and the equations |
| `vendor/katex/` | KaTeX 0.16.47 (MIT licence): `katex.min.js`, `katex.min.css`, the woff2 fonts |
| `tests/validate.mjs` | checks the core inside `index.html` and every LaTeX string the page renders |

## Tests

```
node tests/validate.mjs     # about 6 s
```

- The closed form matches numerical integration to 7e-14 (ω from 150 to 2500 cm⁻¹, |d| ≤ 0.2 Å, v′ ≤ 20, v″ ≤ 6)
  and to 1e-10 at large displacements (v′ ≤ 24, v″ ≤ 15).
- Σ_v′ FC = 1 to 1e-15 from v″ = 0, 1, 3.
- McHale's FC₀₀, FC₂₀, the sign of ⟨χ′₂|χ″₀⟩ and the general ⟨χ′_2n|χ″₀⟩ match to 1e-14; displaced equal
  frequencies give the Poisson progression e^(−S) S^v′/v′!.
- Parity-forbidden, orthogonal and ≈ orthogonal labels are set exactly where they should be.
- Every table polynomial evaluates to the same number as the overlap (228 cases, 2e-16).
- KaTeX parses all 22 equations and 400 table formulas in strict mode.
