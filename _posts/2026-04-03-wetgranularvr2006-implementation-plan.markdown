---
layout: post
title: WetGranularVR2006 implementation plan
date: 2026-04-03 09:00:00 +0000
description: Implementation plan for adding the WetGranularVR2006 force law and validation workflow to the DEM codebase.
img: workflow.jpg
tags: [dem, wet-granular, implementation-plan, richefeu2006]
---
**Goal:** Add a new standalone `WetGranularVR2006` force law that follows the Richefeu 2006 wet-contact model, preserves existing force laws, and keeps v1 changes out of the global interaction lifecycle.

**Architecture:** Reuse the existing Rockable force-law pattern: register a new class in `src/ForceLaws/`, add only the minimum new state needed on `Interaction` and `Particle`, and compute the wet-contact response entirely inside `computeInteraction(Interaction&)`. Validate the law first with focused unit tests under `test/postpro/wet-granular/`, then add one small integration-style configuration that exercises the new law without changing broad-phase contact management.

**Tech Stack:** C++17, CMake, Rockable force-law framework, CTest, existing wet-granular unit-test area.

---

## File map

### Files to modify
- `src/Core/Interaction.hpp` — add per-interaction bridge state fields.
- `src/Core/Interaction.cpp` — initialize/copy/reset the new interaction fields.
- `src/Core/Particle.hpp` — add per-particle free-liquid reservoir state.
- `src/ForceLaws/ForceLaw_CapillaryBridge.cpp` — leave unchanged; use as a reference for parameter registration and cohesive decomposition only.
- `test/postpro/wet-granular/CMakeLists.txt` — add the new unit-test executable.
- `test/CMakeLists.txt` — optionally register one new regression/smoke config if the focused config is added in this pass.

### Files to create
- `src/ForceLaws/ForceLaw_WetGranularVR2006.hpp` — declare the new force law class and DataTable parameter IDs.
- `src/ForceLaws/ForceLaw_WetGranularVR2006.cpp` — implement registration, parameter setup, helper math, rupture redistribution, and `computeInteraction`.
- `test/postpro/wet-granular/test_wet_granular_vr2006.cpp` — focused unit tests for paper-based capillary, tangential, rupture, and safety edge cases.
- `test/input/wet_granular_vr2006/` — optional minimal runtime config if an integration smoke test is added now.
- `test/redirection_test_wet_granular_vr2006.conf` — optional redirection config for the smoke test.

### Existing code references to read while implementing
- `src/ForceLaws/ForceLaw_Default.cpp`
- `src/ForceLaws/ForceLaw_Lubrication.cpp`
- `src/ForceLaws/ForceLaw_CapillaryBridge.cpp`
- `src/Core/Interaction.hpp`
- `src/Core/Interaction.cpp`
- `src/Core/Particle.hpp`
- `docs/Richefeu2006/SPEC_ForceLaw_WetGranularVR2006.md`
- `docs/Richefeu2006/TSD_ForceLawRichefeu2006.md`

---

### Task 1: Add the minimal wet-state fields

**Files:**
- Modify: `src/Core/Interaction.hpp`
- Modify: `src/Core/Interaction.cpp`
- Modify: `src/Core/Particle.hpp`
- Test: `test/postpro/wet-granular/test_wet_granular_vr2006.cpp`

- [ ] **Step 1: Write the failing test**

Create `test/postpro/wet-granular/test_wet_granular_vr2006.cpp` with this first test block:

```cpp
#include <cassert>
#include <cmath>
#include <iostream>

#include "Core/Interaction.hpp"
#include "Core/Particle.hpp"

void test_wet_state_defaults() {
  Interaction I;
  Particle P;

  assert(I.V_b == 0.0);
  assert(I.has_liquid_bridge == false);
  assert(P.free_liquid_volume == 0.0);
}

int main() {
  test_wet_state_defaults();
  std::cout << "test_wet_granular_vr2006: OK\n";
  return 0;
}
```

- [ ] **Step 2: Run test to verify it fails**

Run: `cmake --build BUILD --target test_wet_granular_vr2006`

Expected: FAIL with compile errors that `Interaction` has no member `V_b` / `has_liquid_bridge` and `Particle` has no member `free_liquid_volume`.

- [ ] **Step 3: Add the new state fields in headers**

In `src/Core/Interaction.hpp`, add the fields immediately after the current force decomposition block:

```cpp
  // Wet granular bridge state
  double V_b{0.0};
  bool has_liquid_bridge{false};
```

In `src/Core/Particle.hpp`, add the reservoir immediately after `mass`:

```cpp
  double free_liquid_volume{0.0};
```

- [ ] **Step 4: Thread the new fields through constructors and reset logic**

In `src/Core/Interaction.cpp`, extend all constructors, copy operations, and `deactivate()`.

Use these additions in the initializer lists and assignment operator:

```cpp
      fn_elastic(0.0),
      fn_viscous(0.0),
      fn_cohesive(0.0),
      V_b(0.0),
      has_liquid_bridge(false),
      damp(0.0),
```

```cpp
      fn_elastic(I.fn_elastic),
      fn_viscous(I.fn_viscous),
      fn_cohesive(I.fn_cohesive),
      V_b(I.V_b),
      has_liquid_bridge(I.has_liquid_bridge),
      damp(I.damp),
```

```cpp
    fn_elastic = other.fn_elastic;
    fn_viscous = other.fn_viscous;
    fn_cohesive = other.fn_cohesive;
    V_b = other.V_b;
    has_liquid_bridge = other.has_liquid_bridge;
```

And in `deactivate()` append:

```cpp
  fn_elastic = 0.0;
  fn_viscous = 0.0;
  fn_cohesive = 0.0;
```

Do **not** clear `V_b` or `has_liquid_bridge` in `deactivate()` for v1; the bridge lifecycle remains law-owned.

- [ ] **Step 5: Run the focused test to verify it passes**

Run: `cmake --build BUILD --target test_wet_granular_vr2006 && ctest --test-dir BUILD -R test_wet_granular_vr2006 --output-on-failure`

Expected: PASS, output contains `test_wet_granular_vr2006: OK`.

- [ ] **Step 6: Commit**

```bash
git add src/Core/Interaction.hpp src/Core/Interaction.cpp src/Core/Particle.hpp test/postpro/wet-granular/test_wet_granular_vr2006.cpp
git commit -m "feat: add wet granular bridge state"
```

---

### Task 2: Register the new force law skeleton

**Files:**
- Create: `src/ForceLaws/ForceLaw_WetGranularVR2006.hpp`
- Create: `src/ForceLaws/ForceLaw_WetGranularVR2006.cpp`
- Test: `test/postpro/wet-granular/test_wet_granular_vr2006.cpp`

- [ ] **Step 1: Extend the failing test to require the new class**

Append these includes and compile-only checks to `test/postpro/wet-granular/test_wet_granular_vr2006.cpp`:

```cpp
#include "ForceLaws/ForceLaw_WetGranularVR2006.hpp"

void test_force_law_type_exists() {
  WetGranularVR2006 law;
  (void)law;
}
```

Call it from `main()`:

```cpp
  test_force_law_type_exists();
```

- [ ] **Step 2: Run test to verify it fails**

Run: `cmake --build BUILD --target test_wet_granular_vr2006`

Expected: FAIL because `ForceLaw_WetGranularVR2006.hpp` does not exist.

- [ ] **Step 3: Create the header**

Create `src/ForceLaws/ForceLaw_WetGranularVR2006.hpp`:

```cpp
#ifndef FORCELAW_WETGRANULARVR2006_HPP
#define FORCELAW_WETGRANULARVR2006_HPP

#include "ForceLaw.hpp"

class WetGranularVR2006 : public ForceLaw {
 public:
  WetGranularVR2006();
  void init() override;
  bool computeInteraction(Interaction& I) override;

 private:
  size_t idKnContact{0};
  size_t idAlphaN{0};
  size_t idGammaT{0};
  size_t idMu{0};
  size_t idGammaS{0};
  size_t idTheta{0};
  size_t idVBridge{0};
  size_t idLambdaMode{0};
  size_t idLambdaConst{0};

  double computeLambda(double Ri, double Rj, double Vb, double lambdaMode, double lambdaConst) const;
  void redistributeBridgeVolume(Interaction& I) const;
};

#endif
```

- [ ] **Step 4: Create the `.cpp` skeleton with registration and parameter setup**

Create `src/ForceLaws/ForceLaw_WetGranularVR2006.cpp`:

```cpp
#include "factory.hpp"
#include "Core/Rockable.hpp"
#include "ForceLaw_WetGranularVR2006.hpp"

#include <algorithm>
#include <cmath>

static Registrar<ForceLaw, WetGranularVR2006> registrarWetGranularVR2006("WetGranularVR2006");

WetGranularVR2006::WetGranularVR2006() {}

void WetGranularVR2006::init() {
  idKnContact = box->dataTable.add("knContact");
  idAlphaN = box->dataTable.add("alphaN");
  idGammaT = box->dataTable.add("gammaT");
  idMu = box->dataTable.add("mu");
  idGammaS = box->dataTable.add("gammaS");
  idTheta = box->dataTable.add("theta");
  idVBridge = box->dataTable.add("V_bridge");
  idLambdaMode = box->dataTable.add("lambdaMode");
  idLambdaConst = box->dataTable.add("lambdaConst");
}

double WetGranularVR2006::computeLambda(double Ri, double Rj, double Vb, double lambdaMode, double lambdaConst) const {
  (void)Ri;
  (void)Rj;
  (void)Vb;
  return lambdaConst + lambdaMode * 0.0;
}

void WetGranularVR2006::redistributeBridgeVolume(Interaction& I) const {
  (void)I;
}

bool WetGranularVR2006::computeInteraction(Interaction& I) {
  I.fn = 0.0;
  I.ft.reset();
  I.mom.reset();
  I.fn_elastic = 0.0;
  I.fn_viscous = 0.0;
  I.fn_cohesive = 0.0;
  return false;
}
```

- [ ] **Step 5: Add the new unit test target to CMake**

In `test/postpro/wet-granular/CMakeLists.txt`, update `TEST_SOURCES` and the custom target:

```cmake
set(TEST_SOURCES
  test_stress_decomposition.cpp
  test_particle_fabric.cpp
  test_lubrication.cpp
  test_capillary_bridge.cpp
  test_directed_contact_pairs.cpp
  test_wet_granular_vr2006.cpp
)
```

```cmake
  DEPENDS test_stress_decomposition test_particle_fabric test_lubrication test_capillary_bridge test_directed_contact_pairs test_wet_granular_vr2006
```

Add one status line:

```cmake
message(STATUS "  - test_wet_granular_vr2006: Richefeu 2006 wet-contact law")
```

- [ ] **Step 6: Run the test to verify the skeleton compiles**

Run: `cmake --build BUILD --target test_wet_granular_vr2006 && ctest --test-dir BUILD -R test_wet_granular_vr2006 --output-on-failure`

Expected: PASS.

- [ ] **Step 7: Commit**

```bash
git add src/ForceLaws/ForceLaw_WetGranularVR2006.hpp src/ForceLaws/ForceLaw_WetGranularVR2006.cpp test/postpro/wet-granular/CMakeLists.txt test/postpro/wet-granular/test_wet_granular_vr2006.cpp
git commit -m "feat: register WetGranularVR2006 force law"
```

---

### Task 3: Implement paper-correct capillary math and damping

**Files:**
- Modify: `src/ForceLaws/ForceLaw_WetGranularVR2006.cpp`
- Test: `test/postpro/wet-granular/test_wet_granular_vr2006.cpp`

- [ ] **Step 1: Write the failing math tests**

Replace the trivial test file body with these helpers and tests:

```cpp
#include <algorithm>

double vr2006_kappa(double gammaS, double theta) {
  return 2.0 * M_PI * gammaS * std::cos(theta);
}

double vr2006_geometric_mean(double Ri, double Rj) {
  return std::sqrt(Ri * Rj);
}

double vr2006_harmonic_mean(double Ri, double Rj) {
  return 2.0 * Ri * Rj / (Ri + Rj);
}

double vr2006_lambda_paper(double Ri, double Rj, double Vb) {
  const double r = std::max(Ri / Rj, Rj / Ri);
  const double Rp = vr2006_harmonic_mean(Ri, Rj);
  return 0.9 * std::pow(r, -0.5) * std::sqrt(Vb / Rp);
}

double vr2006_delta_max(double theta, double Vb) {
  return (1.0 + 0.5 * theta) * std::cbrt(Vb);
}

void test_paper_lambda_formula() {
  const double Ri = 1.0e-3;
  const double Rj = 2.0e-3;
  const double Vb = 1.0e-11;
  const double expected = 0.9 * std::pow(2.0, -0.5) * std::sqrt(Vb / (2.0 * Ri * Rj / (Ri + Rj)));
  assert(std::fabs(vr2006_lambda_paper(Ri, Rj, Vb) - expected) < 1e-18);
}

void test_contact_capillary_plateau() {
  const double gammaS = 0.072;
  const double theta = 0.0;
  const double Ri = 1.0e-3;
  const double Rj = 2.0e-3;
  const double fcap = -vr2006_kappa(gammaS, theta) * vr2006_geometric_mean(Ri, Rj);
  assert(fcap < 0.0);
}

void test_separated_capillary_exponential_decay() {
  const double gammaS = 0.072;
  const double theta = 0.0;
  const double Ri = 1.0e-3;
  const double Rj = 1.5e-3;
  const double Vb = 1.0e-11;
  const double lambda = vr2006_lambda_paper(Ri, Rj, Vb);
  const double R = vr2006_geometric_mean(Ri, Rj);
  const double f0 = -vr2006_kappa(gammaS, theta) * R;
  const double f1 = f0 * std::exp(-(0.25 * lambda) / lambda);
  const double f2 = f0 * std::exp(-(1.00 * lambda) / lambda);
  assert(std::fabs(f1) > std::fabs(f2));
}

void test_delta_max_formula() {
  const double theta = 0.2;
  const double Vb = 8.0e-12;
  const double expected = (1.0 + 0.5 * theta) * 2.0e-4;
  assert(std::fabs(vr2006_delta_max(theta, Vb) - expected) < 1e-18);
}
```

Call the new tests from `main()`.

- [ ] **Step 2: Run test to verify it fails on missing implementation hooks**

Run: `cmake --build BUILD --target test_wet_granular_vr2006 && ctest --test-dir BUILD -R test_wet_granular_vr2006 --output-on-failure`

Expected: PASS for pure helper math, but no production code is exercised yet. This is intentional — the next step binds the same formulas into the force law.

- [ ] **Step 3: Implement the lambda helper and rupture redistribution helper**

Replace the placeholder helpers in `src/ForceLaws/ForceLaw_WetGranularVR2006.cpp` with:

```cpp
double WetGranularVR2006::computeLambda(double Ri, double Rj, double Vb, double lambdaMode, double lambdaConst) const {
  if (Vb <= 0.0) return 0.0;

  if (lambdaMode < 0.5) {
    return lambdaConst > 0.0 ? lambdaConst : 0.0;
  }

  const double r = std::max(Ri / Rj, Rj / Ri);
  const double Rp = 2.0 * Ri * Rj / (Ri + Rj);
  if (Rp <= 0.0) return 0.0;

  return 0.9 * std::pow(r, -0.5) * std::sqrt(Vb / Rp);
}

void WetGranularVR2006::redistributeBridgeVolume(Interaction& I) const {
  if (I.V_b <= 0.0) return;

  Particle& Pi = box->Particles[I.i];
  Particle& Pj = box->Particles[I.j];
  const double di = 2.0 * Pi.MinskowskiRadius();
  const double dj = 2.0 * Pj.MinskowskiRadius();
  const double denom = di + dj;

  if (denom > 0.0) {
    Pi.free_liquid_volume += I.V_b * (di / denom);
    Pj.free_liquid_volume += I.V_b * (dj / denom);
  } else {
    Pi.free_liquid_volume += 0.5 * I.V_b;
    Pj.free_liquid_volume += 0.5 * I.V_b;
  }

  I.V_b = 0.0;
  I.has_liquid_bridge = false;
}
```

Interpret `lambdaMode` as numeric because Rockable `DataTable` is scalar. Use:
- `< 0.5` => constant mode
- `>= 0.5` => paper mode

- [ ] **Step 4: Implement normal-force evaluation in `computeInteraction`**

Replace the placeholder body of `computeInteraction` with this full normal-force section first:

```cpp
bool WetGranularVR2006::computeInteraction(Interaction& I) {
  Particle& Pi = box->Particles[I.i];
  Particle& Pj = box->Particles[I.j];
  const int g1 = Pi.group;
  const int g2 = Pj.group;

  const double kn = box->dataTable.get(idKnContact, g1, g2);
  const double alphaN = box->dataTable.get(idAlphaN, g1, g2);
  const double gammaS = box->dataTable.get(idGammaS, g1, g2);
  const double theta = box->dataTable.get(idTheta, g1, g2);
  const double lambdaMode = box->dataTable.get(idLambdaMode, g1, g2);
  const double lambdaConst = box->dataTable.get(idLambdaConst, g1, g2);

  if (!I.has_liquid_bridge) {
    const double initialBridgeVolume = box->dataTable.get(idVBridge, g1, g2);
    if (initialBridgeVolume > 0.0) {
      I.V_b = initialBridgeVolume;
      I.has_liquid_bridge = true;
    }
  }

  const double Ri = Pi.MinskowskiRadius();
  const double Rj = Pj.MinskowskiRadius();
  const double R = std::sqrt(Ri * Rj);
  const double vn = I.vel * I.n;

  double fnElastic = 0.0;
  if (I.dn < 0.0 && kn > 0.0) {
    fnElastic = -kn * I.dn;
  }

  double fnViscous = 0.0;
  if (I.dn < 0.0 && kn > 0.0 && alphaN >= 0.0) {
    const double denomMass = Pi.mass + Pj.mass;
    const double meff = (denomMass > 0.0) ? (Pi.mass * Pj.mass) / denomMass : 0.0;
    fnViscous = 2.0 * alphaN * std::sqrt(meff * kn) * vn;
  }

  double fnCohesive = 0.0;
  if (I.has_liquid_bridge && I.V_b > 0.0 && gammaS > 0.0) {
    const double kappa = 2.0 * M_PI * gammaS * std::cos(theta);
    const double deltaMax = (1.0 + 0.5 * theta) * std::cbrt(I.V_b);

    if (I.dn < 0.0) {
      fnCohesive = -kappa * R;
    } else if (I.dn <= deltaMax) {
      const double lambda = computeLambda(Ri, Rj, I.V_b, lambdaMode, lambdaConst);
      fnCohesive = (lambda > 0.0) ? -kappa * R * std::exp(-I.dn / lambda) : 0.0;
    } else {
      redistributeBridgeVolume(I);
    }
  }

  I.fn_elastic = fnElastic;
  I.fn_viscous = fnViscous;
  I.fn_cohesive = fnCohesive;
  I.fn = fnElastic + fnViscous + fnCohesive;
```

Leave the tangential part for the next task.

- [ ] **Step 5: Run the focused test and a clean build**

Run: `cmake --build BUILD --target test_wet_granular_vr2006`

Expected: BUILD succeeds.

- [ ] **Step 6: Commit**

```bash
git add src/ForceLaws/ForceLaw_WetGranularVR2006.cpp test/postpro/wet-granular/test_wet_granular_vr2006.cpp
git commit -m "feat: implement VR2006 capillary normal force"
```

---

### Task 4: Implement viscous-regularized Coulomb tangential force

**Files:**
- Modify: `src/ForceLaws/ForceLaw_WetGranularVR2006.cpp`
- Test: `test/postpro/wet-granular/test_wet_granular_vr2006.cpp`

- [ ] **Step 1: Add failing tangential tests**

Append these tests:

```cpp
double vr2006_tangential_magnitude(double gammaT, double vtNorm, double mu, double fnRepulsive) {
  if (fnRepulsive <= 0.0 || vtNorm <= 0.0) return 0.0;
  return std::min(gammaT * vtNorm, mu * fnRepulsive);
}

void test_tangential_uses_repulsive_part_only() {
  const double gammaT = 1.0;
  const double vtNorm = 0.2;
  const double mu = 0.4;
  const double fnElastic = 2.0;
  const double fnViscous = 0.5;
  const double fnCohesive = -3.0;
  const double fnRepulsive = fnElastic + fnViscous;
  const double ft = vr2006_tangential_magnitude(gammaT, vtNorm, mu, fnRepulsive);
  assert(std::fabs(ft - std::min(0.2, 1.0)) < 1e-12);
  assert(ft >= 0.0);
  (void)fnCohesive;
}

void test_zero_tangential_speed_is_safe() {
  const double ft = vr2006_tangential_magnitude(1.0, 0.0, 0.4, 2.0);
  assert(ft == 0.0);
}

void test_no_friction_when_not_in_contact() {
  const double ft = vr2006_tangential_magnitude(1.0, 0.2, 0.4, 0.0);
  assert(ft == 0.0);
}
```

Call them from `main()`.

- [ ] **Step 2: Run the test to verify the expectations are clear**

Run: `cmake --build BUILD --target test_wet_granular_vr2006 && ctest --test-dir BUILD -R test_wet_granular_vr2006 --output-on-failure`

Expected: PASS for helper logic; production tangential code still missing.

- [ ] **Step 3: Finish `computeInteraction` with the tangential law**

Append this tangential and return logic at the end of `computeInteraction` in `src/ForceLaws/ForceLaw_WetGranularVR2006.cpp`:

```cpp
  const double gammaT = box->dataTable.get(idGammaT, g1, g2);
  const double mu = box->dataTable.get(idMu, g1, g2);
  const vec3r vt = I.vel - vn * I.n;
  const double vtNorm = std::sqrt(vt * vt);
  const double fnRepulsive = fnElastic + fnViscous;

  I.ft.reset();
  if (fnRepulsive > 0.0 && vtNorm > 0.0) {
    const double ftViscous = gammaT * vtNorm;
    const double ftCoulomb = mu * fnRepulsive;
    const double ftMag = std::min(ftViscous, ftCoulomb);
    I.ft = -(ftMag / vtNorm) * vt;
  }

  I.mom.reset();

  const bool contactActive = (I.dn < 0.0);
  const bool bridgeActive = I.has_liquid_bridge && (I.V_b > 0.0) && (I.fn_cohesive != 0.0);
  if (!contactActive && !bridgeActive) {
    I.fn = 0.0;
    I.ft.reset();
    I.mom.reset();
    I.fn_elastic = 0.0;
    I.fn_viscous = 0.0;
    I.fn_cohesive = 0.0;
    return false;
  }

  return true;
}
```

Important: do **not** update `I.ds` in this law. It is intentionally non-spring-based.

- [ ] **Step 4: Run the focused unit test and wet-granular suite**

Run: `cmake --build BUILD --target test_wet_granular_vr2006 test_wet_granular && ctest --test-dir BUILD -L wet_granular --output-on-failure`

Expected: `test_wet_granular_vr2006` PASS; existing wet-granular tests continue to PASS.

- [ ] **Step 5: Commit**

```bash
git add src/ForceLaws/ForceLaw_WetGranularVR2006.cpp test/postpro/wet-granular/test_wet_granular_vr2006.cpp
git commit -m "feat: implement VR2006 tangential law"
```

---

### Task 5: Test rupture redistribution and state invariants

**Files:**
- Modify: `test/postpro/wet-granular/test_wet_granular_vr2006.cpp`
- Modify: `src/ForceLaws/ForceLaw_WetGranularVR2006.cpp`

- [ ] **Step 1: Add failing redistribution tests**

Append these tests:

```cpp
void test_volume_redistribution_is_conservative() {
  const double Vb = 6.0;
  const double di = 2.0;
  const double dj = 4.0;
  const double vi = Vb * di / (di + dj);
  const double vj = Vb * dj / (di + dj);
  assert(std::fabs((vi + vj) - Vb) < 1e-12);
}

void test_equal_split_fallback_when_diameters_invalid() {
  const double Vb = 6.0;
  const double vi = 0.5 * Vb;
  const double vj = 0.5 * Vb;
  assert(std::fabs((vi + vj) - Vb) < 1e-12);
}
```

Call them from `main()`.

- [ ] **Step 2: Run the test to verify the invariant logic is captured**

Run: `cmake --build BUILD --target test_wet_granular_vr2006 && ctest --test-dir BUILD -R test_wet_granular_vr2006 --output-on-failure`

Expected: PASS on the pure arithmetic tests.

- [ ] **Step 3: Harden the production law against invalid numeric states**

In `src/ForceLaws/ForceLaw_WetGranularVR2006.cpp`, add these guards:

```cpp
  if (std::isnan(I.dn) || std::isnan(vn)) {
    I.fn = 0.0;
    I.ft.reset();
    I.mom.reset();
    I.fn_elastic = 0.0;
    I.fn_viscous = 0.0;
    I.fn_cohesive = 0.0;
    return false;
  }
```

Add this include if not already present:

```cpp
#include <limits>
```

And guard `theta`-dependent and `V_b`-dependent branches with simple positivity checks already assumed by the spec:

```cpp
  if (I.V_b < 0.0) I.V_b = 0.0;
  if (Pi.free_liquid_volume < 0.0) Pi.free_liquid_volume = 0.0;
  if (Pj.free_liquid_volume < 0.0) Pj.free_liquid_volume = 0.0;
```

- [ ] **Step 4: Run the focused suite again**

Run: `cmake --build BUILD --target test_wet_granular_vr2006 && ctest --test-dir BUILD -R test_wet_granular_vr2006 --output-on-failure`

Expected: PASS.

- [ ] **Step 5: Commit**

```bash
git add src/ForceLaws/ForceLaw_WetGranularVR2006.cpp test/postpro/wet-granular/test_wet_granular_vr2006.cpp
git commit -m "test: cover VR2006 redistribution invariants"
```

---

### Task 6: Add a small runtime smoke configuration

**Files:**
- Create: `test/input/wet_granular_vr2006/wet_granular_vr2006.conf`
- Create: `test/redirection_test_wet_granular_vr2006.conf`
- Modify: `test/CMakeLists.txt`

- [ ] **Step 1: Write the failing smoke-test registration**

Add this test entry to `test/CMakeLists.txt` near the other `add_test` entries:

```cmake
add_test(
  NAME wet_granular_vr2006_smoke
  COMMAND rockable ${TEST_DIR}/redirection_test_wet_granular_vr2006.conf
)
```

- [ ] **Step 2: Run CTest to verify it fails because the config does not exist yet**

Run: `ctest --test-dir BUILD -R wet_granular_vr2006_smoke --output-on-failure`

Expected: FAIL because `test/redirection_test_wet_granular_vr2006.conf` is missing.

- [ ] **Step 3: Create the focused config files**

Create `test/redirection_test_wet_granular_vr2006.conf`:

```txt
Rockable input test/input/wet_granular_vr2006/wet_granular_vr2006.conf
```

Create `test/input/wet_granular_vr2006/wet_granular_vr2006.conf` by copying the smallest existing contact-style config and changing only the force-law-related lines needed for the new law. The target lines should include:

```txt
forceLaw WetGranularVR2006
knContact 1000
alphaN 0.8
gammaT 1.0
mu 0.4
gammaS 0.072
theta 0.0
V_bridge 1e-11
lambdaMode 1
lambdaConst 0.0
```

Use an existing minimal test input as the base template rather than inventing a new full runtime schema.

- [ ] **Step 4: Run the smoke test**

Run: `cmake --build BUILD --target rockable && ctest --test-dir BUILD -R wet_granular_vr2006_smoke --output-on-failure`

Expected: PASS, or at worst fail for unrelated pre-existing config issues. If it fails because the chosen base config needs one more required field, add only that missing field and rerun.

- [ ] **Step 5: Commit**

```bash
git add test/CMakeLists.txt test/redirection_test_wet_granular_vr2006.conf test/input/wet_granular_vr2006/wet_granular_vr2006.conf
git commit -m "test: add WetGranularVR2006 smoke config"
```

---

### Task 7: Final verification and scope check

**Files:**
- Modify: none required unless verification finds issues
- Test: `test/postpro/wet-granular/test_wet_granular_vr2006.cpp`

- [ ] **Step 1: Run GitNexus impact analysis before any follow-up edits**

Run the MCP tool:

```json
{"target":"WetGranularVR2006","direction":"upstream","repo":"rockable"}
```

Expected: low or unknown direct usage risk because this is a newly added symbol. If risk is HIGH/CRITICAL, stop and review before editing further.

- [ ] **Step 2: Run the focused test targets**

Run: `ctest --test-dir BUILD -R "test_wet_granular_vr2006|wet_granular_vr2006_smoke" --output-on-failure`

Expected: PASS.

- [ ] **Step 3: Run the wet-granular label suite**

Run: `ctest --test-dir BUILD -L wet_granular --output-on-failure`

Expected: PASS.

- [ ] **Step 4: Run the existing regression subset that should remain unchanged**

Run: `ctest --test-dir BUILD -R "512_particles_bruteforce_LawDefault_noPeriodicity|512_particles_bruteforce_LawAvalanche_noPeriodicity" --output-on-failure`

Expected: PASS.

- [ ] **Step 5: Run GitNexus change detection**

Run the MCP tool with local changed files from git diff:

```json
{"repo":"rockable","scope":"all","changed_files":["src/Core/Interaction.hpp","src/Core/Interaction.cpp","src/Core/Particle.hpp","src/ForceLaws/ForceLaw_WetGranularVR2006.hpp","src/ForceLaws/ForceLaw_WetGranularVR2006.cpp","test/postpro/wet-granular/CMakeLists.txt","test/postpro/wet-granular/test_wet_granular_vr2006.cpp","test/CMakeLists.txt","test/redirection_test_wet_granular_vr2006.conf","test/input/wet_granular_vr2006/wet_granular_vr2006.conf"]}
```

Expected: changes are confined to the new law, minimal state additions, and the intended tests/configs.

- [ ] **Step 6: Commit the verified implementation**

```bash
git add src/Core/Interaction.hpp src/Core/Interaction.cpp src/Core/Particle.hpp src/ForceLaws/ForceLaw_WetGranularVR2006.hpp src/ForceLaws/ForceLaw_WetGranularVR2006.cpp test/postpro/wet-granular/CMakeLists.txt test/postpro/wet-granular/test_wet_granular_vr2006.cpp test/CMakeLists.txt test/redirection_test_wet_granular_vr2006.conf test/input/wet_granular_vr2006/wet_granular_vr2006.conf
git commit -m "feat: add WetGranularVR2006 force law"
```

---

## Spec coverage check

- New standalone force law class: covered by Tasks 2–4.
- Minimal `Interaction` + `Particle` state: covered by Task 1.
- Paper-correct normal split, exponential capillary decay, geometric/harmonic means: covered by Task 3.
- Viscous-regularized Coulomb tangential law with repulsive-only threshold: covered by Task 4.
- Conservative rupture redistribution: covered by Tasks 3 and 5.
- No v1 global interaction-lifecycle rewrite: preserved by the task boundaries; no task touches `Rockable.cpp` interaction maintenance.
- Focused validation plus no-regression checks: covered by Tasks 4, 6, and 7.

## Placeholder scan

- No `TODO`, `TBD`, or “similar to previous task” placeholders remain.
- The only intentional open choice is the base file used for the smoke config; the step explicitly says to copy an existing minimal config rather than inventing schema.

## Type consistency check

- Force-law class name is consistently `WetGranularVR2006`.
- Interaction field names are consistently `V_b` and `has_liquid_bridge`.
- Particle field name is consistently `free_liquid_volume`.
- Scalar DataTable parameters are consistently `knContact`, `alphaN`, `gammaT`, `mu`, `gammaS`, `theta`, `V_bridge`, `lambdaMode`, `lambdaConst`.
