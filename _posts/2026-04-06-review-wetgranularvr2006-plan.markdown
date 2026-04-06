---
layout: post
title: Review report — WetGranularVR2006 plan
date: 2026-04-06 10:00:00 +0000
description: Scientific review of the WetGranularVR2006 implementation plan, covering formula fidelity, conservation, and model scope.
img: how-to-start.jpg
tags: [dem, wet-granular, review, simulation]
---
## Objective
Review the WetGranularVR2006 implementation plan against the capillary-cohesive contact model described in Richefeu, Radjaï, and El Youssoufi (2006), with emphasis on formula fidelity, tangential-force physics, liquid-volume conservation, parameter consistency, and relevant modeling approximations.

## Findings

### 1. Formula Verification
Overall, the implementation plan is scientifically close to the paper for the normal capillary law, but a few details should be stated more explicitly to avoid misinterpretation.

- **Capillary force form:** The plan reproduces the piecewise law in Eq. (4) of the paper:
  - for overlap/contact, \(f_n^c = -\kappa R\)
  - for separation within bridge range, \(f_n^c = -\kappa R \exp(-\delta_n/\lambda)\)
  - for separation beyond rupture, \(f_n^c = 0\)
- **\(\kappa\):** The plan matches Eq. (5):
  - \(\kappa = 2\pi \gamma_s \cos\theta\)
- **\(\delta_{max}\):** The plan matches Eq. (6):
  - \(\delta_{max} = (1 + \theta/2) V_b^{1/3}\)
- **\(\lambda\):** The plan matches Eq. (7) and the text immediately below it, provided the implementation uses:
  - \(r = \max(R_i/R_j, R_j/R_i)\)
  - \(R' = 2R_iR_j/(R_i+R_j)\) as the **harmonic mean**
  - \(\lambda = 0.9\, r^{-1/2}\, \sqrt{V_b/R'}\)
- **Radius used in force amplitude:** The plan correctly uses the **geometric mean** \(R = \sqrt{R_iR_j}\) in the capillary force amplitude, consistent with the paper’s explicit statement that this fits better than the harmonic mean for the force prefactor.

**Important clarification:** the plan introduces `lambdaMode` and `lambdaConst`, allowing a constant-\(\lambda\) option. That is a software convenience, not part of the Richefeu 2006 model itself. If this option is retained, it should be documented as a non-paper extension.

### 2. Physics Correctness
The proposed tangential law is broadly physically correct and consistent with Eq. (8) in the paper, but only if its admissible domain is interpreted carefully.

The paper gives a viscous-regularized Coulomb law:
\[
\mathbf{f}_t = -\min\left(\gamma_t\|\dot{\delta}_t\|,\, \mu(f_n - f_n^c)\right) \frac{\dot{\delta}_t}{\|\dot{\delta}_t\|}.
\]
Since \(f_n = f_n^e + f_n^d + f_n^c\), the Coulomb bound becomes:
\[
\mu(f_n - f_n^c) = \mu(f_n^e + f_n^d).
\]
So the implementation choice to use only the **repulsive part** (`fnElastic + fnViscous`) in the Coulomb limit is physically consistent with the paper.

This is a good choice for two reasons:
- capillary attraction should not artificially enlarge the friction cone;
- friction should act only when there is actual contact-mediated normal compression.

That said, two caveats matter:
- In the paper, tangential friction is part of the contact law; it is not clearly intended to act across a separated gap sustained only by a liquid bridge. The plan’s choice to disable friction when there is no repulsive contact is therefore physically reasonable and arguably preferable.
- The damping term \(f_n^d = 2\alpha_n\sqrt{mk_n}\,\dot{\delta}_n\) is copied from the paper, but sign conventions must remain consistent with the codebase definition of positive/negative normal velocity. If `vn` sign is opposite to the paper convention, the implemented force direction could invert. The plan should explicitly state the sign convention check as a validation item.

### 3. Conservation Analysis
The proposed rupture redistribution is **conservative at the reservoir level**, but it is **not identical** to the redistribution rule stated in the paper.

What the paper says:
- when a bridge ruptures, liquid is redistributed **among the contacts belonging to the same particle** in proportion to grain sizes.

What the plan proposes:
- on rupture, the bridge volume is split between the two particles and added to their `free_liquid_volume` reservoirs, weighted by particle diameter (with equal split fallback if needed);
- future bridge creation then draws from reservoir state.

Assessment:
- **Mass conservation:** yes, the arithmetic split is conservative. The transferred amounts sum exactly to the ruptured \(V_b\), so no liquid is lost or created in that step.
- **Local physical equivalence to the paper:** not exact. The paper describes immediate redistribution to neighboring contacts of each particle, whereas the plan introduces an intermediate per-particle storage variable. That is a modeling approximation.
- **System-level consequence:** this reservoir formulation can still be acceptable as an implementation strategy, but only if bridge creation/consumption rules later ensure total liquid conservation over all interactions and particles. By itself, the rupture step is conservative; the full lifecycle is conservative only if seeding and re-assignment are also conservative.

So the right conclusion is: **conservative in bookkeeping, approximate relative to the paper’s local redistribution mechanism**.

### 4. Parameter Consistency
Parameter naming is mostly coherent, but units and semantics should be tightened.

**Consistent items:**
- `knContact` corresponds to \(k_n\), unit **N/m**.
- `alphaN` corresponds to \(\alpha_n\), dimensionless.
- `gammaT` corresponds to \(\gamma_t\), unit **N·s/m**.
- `mu` is dimensionless friction coefficient.
- `gammaS` corresponds to liquid surface tension \(\gamma_s\), unit **N/m**.
- `theta` is the contact angle.
- `V_bridge` / `V_b` is liquid bridge volume, unit **m^3**.

**Items needing clarification:**
- `theta` must be explicitly documented as **radians**, because the formulas use `cos(theta)` and \((1 + \theta/2)\). Using degrees would silently produce wrong physics.
- `kappa` should not be described as an independent input parameter if the implementation computes it from `gammaS` and `theta`. In the paper, \(\kappa\) is derived, not free.
- The paper’s Table 1 labels \(\kappa\) with unit **N/m**, but in Eq. (4) it multiplies a length scale \(R\), producing force. The implementation should therefore treat `gammaS` as the primary dimensional input and compute force directly; avoid exposing `kappa` separately unless its dimensional meaning is documented carefully.
- The plan mixes `V_b` and `V_bridge` names. This is manageable, but one should define a single convention: e.g. `V_b` for per-interaction state, `V_bridge` for input parameter / default initialization.
- `lambdaMode` is a software control parameter, not a physical parameter from the paper. It should be clearly marked as such.

### 5. Missing Physics / Gaps
The implementation plan is suitable for a focused v1 force law, but it contains several approximations relative to the physical system studied in the paper.

1. **Bridge distribution physics is simplified.**
   The paper assumes liquid bonds are distributed in all gaps within debonding distance, with redistribution among contacts after rupture. The plan replaces this with a local reservoir mechanism. This is computationally convenient, but it changes the local liquid-topology dynamics.

2. **Bridge nucleation/history is underspecified.**
   The plan initializes a bridge from a default `V_bridge` if none exists. That does not by itself reproduce a finite total water content or history-dependent liquid partitioning. Without additional rules, it risks repeated creation of fresh liquid from a parameter rather than from conserved inventory.

3. **No explicit lubrication force.**
   The paper’s Section 2 describes the modeled forces as normal repulsion, capillary cohesion, Coulomb friction, and normal damping. For the target paper, that is fine. But physically, if users expect broader wet-contact realism, hydrodynamic lubrication during approach/separation is missing.

4. **No rolling/torsion resistance.**
   This is also consistent with the paper as presented, but worth flagging because wet granular aggregates often show effects that such resistances can influence.

5. **Contact-angle and wetting assumptions are narrow.**
   The paper simulations assume perfectly wettable particles, effectively \(\theta = 0\). The implementation allows arbitrary `theta`, which is mathematically consistent with the formulas but extends beyond the exact calibration discussed in the paper.

6. **Validity of exponential capillary fit is limited.**
   The paper states the fit is excellent at contact and small gaps. It is still an explicit approximation to Laplace–Young solutions, not a first-principles exact bridge solver.

7. **No explicit statement of pendular-state validity.**
   The model applies to isolated liquid bridges in the pendular regime. It should not be presented as valid for funicular/capillary states with coalesced liquid clusters.

## Recommendations
1. **Keep the normal capillary formulas as written, but document them explicitly as the paper-correct default.** State clearly that \(R\) is geometric mean in force amplitude and \(R'\) is harmonic mean in \(\lambda\).
2. **Mark `lambdaMode` / `lambdaConst` as implementation extensions.** They should not be described as part of Richefeu 2006.
3. **Clarify the conservation claim.** Say that rupture redistribution is mass-conservative in the implementation bookkeeping, but it approximates the paper’s contact-level redistribution through per-particle reservoirs.
4. **Add an explicit bridge lifecycle conservation statement.** The plan should specify that bridge creation must draw from conserved particle/system liquid inventory; otherwise total liquid can drift.
5. **Document units rigorously.** In particular: `theta` in radians, `gammaS` in N/m, `V_bridge` in m^3, `gammaT` in N·s/m.
6. **Add a sign-convention test for the normal damping term.** This is the easiest place for a physically silent bug.
7. **State model scope clearly.** Pendular regime, isolated capillary bridges, exponential fit approximation, no lubrication/rolling resistance unless added separately.

## Conclusion
The WetGranularVR2006 plan is scientifically solid as a v1 implementation of the Richefeu 2006 wet-contact law. The core capillary formulas for \(\kappa\), \(\delta_{max}\), and \(\lambda\) are consistent with the paper, and the viscous-regularized Coulomb tangential law is physically appropriate if friction is limited to the repulsive contact contribution. The main issue is not formula correctness but modeling interpretation: the proposed reservoir-based volume redistribution is conservative in bookkeeping yet only an approximation of the paper’s contact-level redistribution rule, and bridge nucleation must be tied to conserved liquid inventory to avoid nonphysical liquid creation. With those clarifications, the plan is ready for implementation; without them, it remains scientifically plausible but underspecified in liquid-conservation semantics.
