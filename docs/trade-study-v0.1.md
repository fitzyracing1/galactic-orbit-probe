---
title: "Galactic Free-Flyer — Trade Study v0.1"
subtitle: "A spacecraft that leaves the Solar System and coasts on its own Galactic orbit"
author: "Joshua Almeida (fitzyracing1) — Ping Lite design lineage"
date: "20 Oct 2026 — DRAFT, not reviewed"
---

# 1. Summary

The goal is a craft that leaves the Sun and then moves around the Milky Way on its own orbit, the way a free-floating star does. This study compares three versions:

| | V1 Near-term probe | V2 Laser-sail swarm | V3 Von Neumann seed |
|---|---|---|---|
| v_inf | 17–~100 km/s | 0.1–0.2 c | design choice: 50–300 km/s |
| Life | ~100 y active | ~20–50 y active | ~10^6 y |

**Key computed result (§2).** We integrated orbits in MWPotential2014 (galpy). Every excess speed achievable today or soon (17–100 km/s) leaves the craft **bound to the Galaxy** on a mildly perturbed disk orbit. That orbit is a "free-floating star" from day one. At 17 km/s, the craft is **0.6–5 kpc from the Sun after 250 Myr**, depending on launch direction. At 0.1c the craft travels at about 58× the local escape speed (513 km/s in this model), so it is **not** bound. It leaves the Galaxy on a nearly straight line and is about 7.7 Mpc away after 250 Myr. A probe that is meant to "orbit the Galaxy" therefore does not need relativistic speed. Speed only decides *which* Galactic orbit the craft ends up on.

**Recommendation (§8).** The first step is a V1-class probe: an Interstellar-Probe-type mission at about 35 km/s or faster, with Am-241 power and Ping Lite cFS autonomy. It should also be the testbed for long-life autonomy and archival storage. That serves the goals of V2 and V3 without blocking on them.

# 2. Galactic orbit computation

**Method** (scripts: `build/orbits.py`, results: `build/orbits.json`, `build/sun.json`).

- Potential: galpy `MWPotential2014`, which combines a power-law bulge with a cutoff, a Miyamoto–Nagai disk and an NFW halo (Bovy 2015). Scaling: R0 = 8 kpc, v_c = 220 km/s.
- Sun: z = 20.8 pc. Peculiar velocity (U,V,W) = (11.1, 12.24, 7.25) km/s (Schönrich, Binney & Dehnen 2010). This gives Galactocentric (v_R, v_T, v_z) = (−11.1, 232.24, 7.25) km/s.
- Excess speed: v_inf is added as a vector to the solar velocity in the Galactocentric cylindrical frame. Prograde means +v_T, retrograde −v_T, radial-in toward the Galactic Centre, radial-out away from it, and vertical +v_z. The Sun's own gravity is ignored after escape; v_inf is already the heliocentric asymptotic speed.
- Integration: DOP853, 250 Myr, 25,001 steps. The Sun is integrated alongside so its separation from the craft can be tracked.
- Derived quantities:
  - **Guiding radius** R_g: the radius of a circular orbit with the same L_z (galpy `rl`).
  - **Eccentricity** e = (r_apo − r_peri)/(r_apo + r_peri).
  - **Vertical amplitude**: z_max.
  - **Radial period** T_r: the mean interval between successive peri- or apocentres. "—" means it is longer than 250 Myr, or the orbit is unbound.
- Local escape speed in this model: **513 km/s**. This is lower than measured values of about 530–550 km/s (e.g. Piffl et al. 2014; Deason et al. 2019) because MWPotential2014's halo is light. The 300 km/s prograde case (|v| = 532 km/s) is therefore *marginally unbound in this model*. In a heavier halo it would probably be bound on a very wide orbit. Treat that case as model-sensitive.

![Orbits over 250 Myr: x–y (top) and R–z (bottom) for 17, 50, 100 and 300 km/s. Black = Sun.](build/orbits.png)

![Separation from the Sun vs time. Left: 17–300 km/s; right: 0.1c (log).](build/separation.png)

**Results table** (Δ = separation from the Sun in kpc. For comparison, the Sun's own orbit in this model has z_max ≈ 0.1 kpc.)

| v_inf | dir | v_gal km/s | bound? | R_g kpc | r_peri–r_apo kpc | e | z_max kpc | T_r Myr | Δ1 Myr | Δ10 | Δ100 | Δ250 (kpc) |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 17 | prograde | 249.6 | yes | 9.2 | 7.97–10.83 | 0.153 | 0.121 | 201.1 | 0.017 | 0.172 | 2.938 | 4.885 |
| 17 | retrograde | 215.6 | yes | 7.81 | 7.48–8.17 | 0.044 | 0.102 | 162.4 | 0.017 | 0.172 | 2.976 | 5.285 |
| 17 | radial-in | 234.0 | yes | 8.5 | 7.62–9.59 | 0.114 | 0.114 | 181.5 | 0.017 | 0.177 | 1.175 | 0.865 |
| 17 | radial-out | 232.4 | yes | 8.5 | 7.98–9.09 | 0.065 | 0.108 | — | 0.017 | 0.176 | 1.358 | 1.249 |
| 17 | vertical | 233.8 | yes | 8.5 | 7.93–9.24 | 0.077 | 0.398 | 181.5 | 0.017 | 0.16 | 0.303 | 0.583 |
| 50 | prograde | 282.6 | yes | 10.59 | 7.98–15.19 | 0.311 | 0.145 | — | 0.051 | 0.505 | 8.281 | 15.163 |
| 50 | retrograde | 182.7 | yes | 6.51 | 5.44–8.03 | 0.192 | 0.099 | 136.4 | 0.051 | 0.505 | 8.018 | 13.691 |
| 50 | radial-in | 240.3 | yes | 8.5 | 6.95–10.79 | 0.217 | 0.123 | 188.4 | 0.051 | 0.519 | 3.047 | 1.357 |
| 50 | radial-out | 235.6 | yes | 8.5 | 7.4–9.94 | 0.147 | 0.115 | 183.1 | 0.051 | 0.519 | 4.546 | 4.566 |
| 50 | vertical | 239.4 | yes | 8.5 | 7.95–9.71 | 0.1 | 1.31 | 188.8 | 0.051 | 0.478 | 1.816 | 2.793 |
| 100 | prograde | 332.5 | yes | 12.76 | 7.99–26.16 | 0.532 | 0.209 | — | 0.102 | 1.01 | 15.552 | 30.751 |
| 100 | retrograde | 132.9 | yes | 4.7 | 3.22–8.02 | 0.427 | 0.098 | 111.5 | 0.102 | 1.009 | 11.359 | 14.343 |
| 100 | radial-in | 257.5 | yes | 8.5 | 6.07–13.39 | 0.376 | 0.143 | 210.7 | 0.102 | 1.039 | 5.365 | 3.145 |
| 100 | radial-out | 248.8 | yes | 8.5 | 6.44–12.11 | 0.306 | 0.129 | — | 0.102 | 1.038 | 10.433 | 10.441 |
| 100 | vertical | 256.0 | yes | 8.5 | 7.97–11.37 | 0.176 | 2.982 | 211.8 | 0.102 | 0.973 | 4.418 | 8.481 |
| 300 | prograde | 532.4 | **no** | — | 8.0–96.4 | 0.847 | 0.132 | — | 0.307 | 3.035 | 40.313 | 95.153 |
| 300 | retrograde | 69.0 | yes | 2.64 | 1.37–8.01 | 0.708 | 0.102 | 95.2 | 0.307 | 3.027 | 2.899 | 7.445 |
| 300 | radial-in | 388.3 | yes | 8.5 | 3.95–47.9 | 0.848 | 0.419 | — | 0.307 | 3.124 | 17.461 | 56.439 |
| 300 | radial-out | 370.7 | yes | 8.5 | 8.0–43.23 | 0.688 | 0.18 | — | 0.307 | 3.108 | 35.085 | 35.665 |
| 300 | vertical | 385.3 | yes | 8.5 | 8.0–45.71 | 0.702 | 30.438 | — | 0.307 | 2.99 | 24.416 | 48.424 |
| 0.1c | prograde | 30211.5 | **no** | — | 8.0–7723.35 | 0.998 | 1.865 | — | 30.659 | 306.602 | 3085.314 | 7719.912 |
| 0.1c | retrograde | 29747.0 | **no** | — | 8.0–7604.57 | 0.998 | 1.865 | — | 30.659 | 306.539 | 3045.957 | 7608.007 |
| 0.1c | radial-in | 29991.2 | **no** | — | 0.07–7659.03 | 1.0 | 1.687 | — | 30.657 | 306.257 | 3050.597 | 7667.121 |
| 0.1c | radial-out | 29969.0 | **no** | — | 8.0–7669.35 | 0.998 | 1.869 | — | 30.662 | 306.885 | 3080.73 | 7661.206 |
| 0.1c | vertical | 29987.4 | **no** | — | 8.0–7666.06 | 0.998 | 7665.833 | — | 30.659 | 306.583 | 3066.371 | 7666.134 |

**What the numbers show**

- **17 km/s (Voyager class).** The orbit is nearly solar: R_g is 7.8–9.2 kpc, e is 0.04–0.15, z_max is 0.10–0.40 kpc, and T_r is about 160–200 Myr. Separation from the Sun grows to 1–5 kpc after 100–250 Myr. Most of that growth comes from differential rotation (shear), not from the kick itself. The Voyagers already are "free-floating stars" in this sense.
- **50–100 km/s.** Launch direction now matters a lot. A prograde kick at 100 km/s produces R_g = 12.8 kpc, apocentre 26 kpc and e = 0.53. A retrograde kick at 100 km/s produces R_g = 4.7 kpc and a pericentre of 3.2 kpc, which falls into the bar/inner-disk region. A vertical kick at 100 km/s produces z_max = 3.0 kpc, a thick-disk orbit that spends most of its time above the dust layer. That is relevant to erosion (§4) and to stay-away-from-everything archival goals.
- **300 km/s.** These orbits are halo-like: apocentres of 43–96 kpc and z_max up to 30 kpc in the vertical case. Retrograde at 300 km/s leaves only 69 km/s of Galactic speed, so the craft plunges to a pericentre of 1.4 kpc from the Galactic Centre.
- **0.1c.** v_gal ≈ 30,000 km/s, about 58 × v_esc, so the craft is unbound in every direction. It moves essentially in a straight line, 30.7 kpc per Myr, and reaches about 7.7 Mpc in 250 Myr. The Galaxy's potential (Φ of order v_c² ≈ (220 km/s)²) has a negligible effect at that speed. A Starshot-class craft therefore cannot hold a Galactic orbit. It is an intergalactic projectile unless it brakes, for example with a magnetic sail. Within its ~20-year active life it covers only about 2–4 ly.

# 3. Propulsion and Δv

| | V1 Near-term probe | V2 Laser-sail swarm | V3 Von Neumann seed |
|---|---|---|---|
| Primary | SLS/Starship-class launch with kick stages, Jupiter gravity assist, optional solar Oberth burn | Ground phased-array laser (~100 GW class, ~1 km scale) pushing a ~1 g sail plus chip for minutes (Lubin 2016; Parkin 2018) | V1-type launch, then a long-life electric or fusion stage (Freitas 1980 used a Daedalus-type fusion stage) |
| v_inf / Δv | Voyager 1 is about 17 km/s. The Interstellar Probe baseline is about 7 AU/yr ≈ 33 km/s (McNutt et al. 2019) | 0.1–0.2 c = 30,000–60,000 km/s | Must be well above 0.1c to cross the Galaxy within ~10^6 y. A 50–300 km/s coast instead turns it into a Galactic "orbit seeder" |
| Computed (`build/budgets.py`) | Oberth burn Δv after Jupiter has brought the craft to near-parabolic: 1/5/10 km/s at 3 R☉ gives v_inf = **26.7/59.9/85.0 km/s**; at 10 R☉ it gives 19.8/44.5/63.3 km/s. NTP stage at Isp 900 s and mass ratio 3 gives 9.7 km/s. Chemical at Isp 450 s and mass ratio 5 gives 7.1 km/s | Kinetic energy of 1 g at 0.2c: **1.85 × 10^12 J** (the laser's delivered energy is far higher because of sail coupling efficiency; see Parkin 2018) | — |
| Note | A 3 R☉ perihelion needs a heat shield beyond Parker Solar Probe's (about 9.86 R☉). This is a TRL gap. | Atmosphere, beam pointing and sail survival dominate the difficulty | Decelerating at the target needs a magsail or similar |

Time to 1000 AU (computed): **279 y at 17 km/s, 95 y at 50 km/s, 47 y at 100 km/s.** A 100-year V1 life reaches the very local ISM, about 350–2000 AU. On Galactic scales the craft then simply coasts.

# 4. Power over lifetime

Decay fractions (computed): **Pu-238** (t½ 87.7 y) keeps 0.92, 0.67, 0.45, 0.14 and 0.0004 of its power at 10, 50, 100, 250 and 1000 y. **Am-241** (t½ 432.6 y) keeps 0.98, 0.92, 0.85, 0.67 and 0.20. Am-241 has about 0.2× the specific thermal power of Pu-238 (0.114 vs 0.57 W/g, per the ESA RPS programme, Ambrosi et al. 2019). Thermocouple degradation is ignored here, so these figures are optimistic (Voyager's MMRTG-predecessor MHW-RTGs lose about 4 W/yr in total).

| | V1 | V2 | V3 |
|---|---|---|---|
| Source | Pu-238 RTG for a 50 y primary mission, or Am-241 RPS for 100+ y | Beamed during boost only. In cruise: a tiny Pu-238/Am-241 betavoltaic, or ISM kinetic heating (at 0.2c each H atom carries 19 MeV, computed) | Fission (small Kilopower/KRUSTY-class reactor, TRL 5–6) for active phases; Am-241 or Th/U fuel for the long timeline; hibernation in between |
| Hibernation | Duty-cycle the instruments; the 100 y end of life is set by power | Most of the cruise is spent unpowered | Mandatory. More than 99.9% of 10^6 y is dormant, with wake-ups triggered by stellar proximity (estimate) |
| 100 y power (estimate) | Am-241, 300 We BOL → about 255 We, before thermocouple loss | mW class | n/a: life ≫ isotope half-lives. Needs fission or harvesting from target-system material |

# 5. Autonomy stack (subsumption priorities)

Use a Brooks (1986)-style subsumption hierarchy in which higher layers override lower ones. Layers 0–3 can be implemented as **cFS apps on Ping Lite's flight software** (`github.com/fitzyracing1/new-space-station/software`; local copy `/workspace/space-station/software/cFS`). cFS's Software Bus, Scheduler, Health & Safety and Stored Command apps map directly onto these layers.

| Priority | Layer | V1 | V2 | V3 |
|---|---|---|---|---|
| 0 (highest) | Survive: thermal, power-safe mode, radiation upsets, memory scrubbing | cFS HS + watchdog | Hard-wired on the chip | Plus self-repair |
| 1 | Attitude toward Earth or the target, collision avoidance | ACS app | Sail attitude by photon pressure / passive | Plus navigation by pulsars/stars |
| 2 | Communicate / store data | DSN schedule, onboard archive | Burst comms through the swarm | Relay chain back to Sol |
| 3 | Science | Fields/particles, dust, Lyα | Flyby imaging | Target survey |
| 4 | Replicate / build | — | — | ISRU, fabrication, copy-fidelity checks (Freitas 1980), plus hard-coded replication limits (an ethics and safety requirement) |

# 6. Dust and cosmic-ray erosion (computed, `build/budgets.py`)

Assumptions: local ISM n_H ≈ 0.2 cm⁻³ (Frisch, Redfield & Slavin 2011), dust-to-gas mass ratio 0.01, grain density 2500 kg/m³.

| Speed | H atom energy | Gas energy deposited per m² per ly | Dust energy per m² per ly |
|---|---|---|---|
| 17 km/s | 1.5 eV | 457 J | 6.4 J |
| 100 km/s | 52 eV | 1.6 × 10^4 J | 222 J |
| 0.1 c | 4.7 MeV | 1.4 × 10^9 J | 2.0 × 10^7 J |
| 0.2 c | 19 MeV | 5.9 × 10^9 J | 8.0 × 10^7 J |

Single grains: a 1 µm grain carries 1.9 × 10⁻⁷ J at 17 km/s but **2.4 J at 0.2c**. A 10 µm grain carries **2.35 kJ at 0.2c**, roughly the energy of half a gram of TNT, deposited at one point. Hoang et al. (2017) analysed this regime for Starshot. Their conclusion is that gas bombardment produces surface damage to a limited depth, while rare large-grain impacts can be catastrophic. They recommend an edge-on cruise attitude plus a thin shield. At ≤100 km/s, V1 and V3 see erosion that is negligible over ~10^3 ly (estimate: sputtering at eV energies is near threshold). Their dominant long-term damage is **Galactic cosmic rays**. Those cause bit-flips and cumulative total dose in electronics over 10^6 y. This is unquantified here and is a driver for V3 (estimate: radiation-hard storage plus shielding, or repair).

# 7. Data storage and return

| | V1 | V2 | V3 |
|---|---|---|---|
| Return path | X/Ka band, or optical, to the DSN. One-way light time **13.9 h at 100 AU** and **139 h at 1000 AU** | Optical downlink from a gram-scale chip across **4.37 y** of light time (Alpha Cen); photon-starved, received with the same large array | **~3,262 y per kpc** one way, so effectively no return; use relays or self-report |
| Archive | Rad-hard flash; an etched-metal "golden record" as payload | None beyond SRAM | **5D fused-silica storage** (Zhang et al. 2014: about 360 TB per disc and thermally stable for very long times) and/or **DNA** (Church, Gao & Kosuri 2012). DNA needs cold, shielded storage; cosmic-ray damage over 10^6 y is an open question |

# 8. Mass, cost, TRL, science, risks

| | V1 Near-term probe | V2 Laser-sail swarm | V3 Von Neumann seed |
|---|---|---|---|
| Mass | ~500–900 kg spacecraft (Interstellar Probe study class) | ~1 g per sail-craft, about 1000 craft (Parkin 2018) | Freitas 1980 REPRO: ~10^10 kg class (paper's figure); a nanotech seed might be far smaller (speculative) |
| Cost class | Flagship, ~$1–3 B (estimate, IP study order) | ~$10 B class for the beamer (Parkin 2018 system-model order) | Not estimable |
| TRL | 4–6 overall (Am-241 RPS 4–5; Oberth heat shield 3) | 1–3 (beamer, sail materials, comms) | 1 (replication) |
| Science | Heliopause/VLISM, dust, Galactic cosmic rays, extragalactic background light, KBO flybys | Alpha Cen flyby imaging; but not a Galactic orbit (unbound) | Wide sampling of the Galaxy, a persistent presence |
| Top risks | Power decay, autonomy over decades, launch Δv margin, DSN continuity | Grain impacts, sail survival under the beam, comms link budget, cost | Copy errors, control and ethics, power, 10^6-y radiation dose |
| Galactic-orbit goal | **Yes**, bound and nearly solar | **No**, escapes the Galaxy | Yes, if launched at < ~400 km/s |

# 9. Recommendation: a near-term first step

1. Fly a **V1 "Galactic Pathfinder"**: an Interstellar-Probe-class mission with a Jupiter gravity assist and a solid kick stage. Run an Oberth study for a later block. Target v_inf ≥ 35 km/s, Am-241 power for a 100+ y life, and **cFS/Ping Lite autonomy** with subsumption layers 0–3 plus a 5D-glass archive payload.
2. Use the computed orbits to choose a direction. Vertical or prograde gives a cleaner, longer-lived "own" orbit with a larger z excursion. This only matters if the archive is the point; for heliospheric science the direction is driven by the nose.
3. In parallel, start low-cost, low-TRL work: an Am-241 thermal-to-electric life test, a long-duration cFS soak test in sim (`software/sim`), and 5D glass under proton and heavy-ion beams.
4. Do not pursue V2 for this goal. It is excellent for Alpha Cen but cannot stay in the Galaxy. Treat V3 as research only.

# References

- Bovy, J. 2015, *galpy: A Python Library for Galactic Dynamics*, ApJS 216, 29.
- Schönrich, R., Binney, J., Dehnen, W. 2010, MNRAS 403, 1829.
- Piffl, T. et al. 2014, A&A 562, A91 (escape speed). Deason, A. et al. 2019, MNRAS 485, 3514.
- Lubin, P. 2016, *A Roadmap to Interstellar Flight*, JBIS 69, 40.
- Parkin, K. L. G. 2018, *The Breakthrough Starshot system model*, Acta Astronautica 152, 370.
- Hoang, T. et al. 2017, *The interaction of relativistic spacecrafts with the interstellar medium*, ApJ 837, 5.
- McNutt, R. L. Jr. et al. 2019, *Near-term interstellar probe: First step*, Acta Astronautica 162, 284; JHU APL Interstellar Probe study report (2021).
- Freitas, R. A. Jr. 1980, *A self-reproducing interstellar probe*, JBIS 33, 251.
- Ambrosi, R. M. et al. 2019, *European Radioisotope Thermoelectric Generators (RTGs) and Radioisotope Heater Units (RHUs) for Space Science and Exploration*, Space Sci. Rev. 215, 55.
- Frisch, P. C., Redfield, S., Slavin, J. D. 2011, ARA&A 49, 237.
- Zhang, J., Gecevičius, M., Beresna, M., Kazansky, P. G. 2014, Phys. Rev. Lett. 112, 033901.
- Church, G. M., Gao, Y., Kosuri, S. 2012, Science 337, 1628.
- Brooks, R. A. 1986, IEEE J. Robotics & Automation 2, 14.
