# ASFV domestic and wild pig spread model

**We model ASFV spread as a **layered system** that couples **within-farm infection dynamics** with **between-farm spatial dissemination**. Viral transmission occurs not only through **direct pig contact**, but also via **environmental contamination, vehicle-mediated transfer, and feral pig movement across the landscape**. By embedding barn-level epidemiological processes within a **spatial grid**, we capture how **local outbreaks escalate into broader regional spread**, enabling the identification of **high-risk transmission corridors** and evaluation of **targeted biosecurity strategies**.**

---

# Spatial ASFV spread

### 1. Define the core parameters  
## Model Parameters

| Parameter   | Meaning | Value |
|-------------|---------|-------|
| $\beta_{wp}$  | Within-pen direct transmission rate | min = 1.57, mod = 2.79, max = 4.95 |
| $\beta_{bp}$  | Between-pen direct transmission rate | min = 0.15, mod = 0.28, max = 0.5 |
| $\omega_v$ | Particles shed in faeces post-infection | min = $10^4$, max = $4.96 \times 10^{6}$/g |
| $V_{shed}$ | Particles spread through aerosol | min = $10^{2}$, max = $10^{3.2}TCID_{50}eq./m^3$ |
| $V_{area}$ | Dimension of pen | 25.85 m³ |
| $\beta_{fp}$ | Fecal transmission rate within and between pen | $10^{-2}$ |
| $\beta_{ap}$ | Aerosol transmission rate | min = $6 \times 10^{-3}$, mod = $6 \times 10^{-2}$, max = $9 \times 10^{-2}$ |
| $\epsilon_1$ | Rate at which the virus decays | 0.08 |
| $\epsilon_2$ | Fecal removal per g per day (Fraction) | 0.7 |
| $Q_i$ | Fecal ingestion rate by finisher pigs | 25 g/day |
| $Q_{shed}$ | Average feces shed by a finisher pig | 1000 g/day |
| $C_{\beta}$ | Human transmission rate | 0.01 |
| $1/\theta$ | Length of latency period (days) | 4.501 (95% P.I., 2.417–7.223) |
| $1/\gamma$ | Recovery duration (days) | 44.06 (95% P.I., 33.23–56.394) |
| $1/\mu_s$ | Average mortality rate of Susceptible (S) | 0.002 |
| $1/\mu_e$ | Average mortality rate of Exposed (E) | 0.002 |
| $1/\mu_c$ | Average mortality rate of Clinical (C) | 0.12 |
| $1/\mu_{sc}$ | Average mortality rate of Sub-Clinical ($S_c$) | 0.06 |
| $1/\mu_{cc}$ | Average mortality rate of Chronic-Carrier ($C_c$) | 0.002 |
| $1/\mu_d$ | Average mortality rate of Detected (D) | 0.06 |
| $\kappa_1$ | Transition rate from Clinical (C) → Sub-Clinical ($S_c$) | 0–0.01 |
| $\kappa_2$ | Transition rate from Sub-Clinical ($S_c$) → Clinical (C) | 0–0.1 |
| $\kappa_3$ | Transition rate from Chronic-Carrier ($C_c$) → Clinical (C) | 0–0.01 |
| $\kappa_4$ | Transition from Chronic-Carrier ($C_c$) → Sub-Clinical ($S_c$) | 0–0.01 |
| $\theta_1$ | Exposed pigs → Clinical (C) (Fraction) | 0.8 |
| $\theta_2$ | Exposed pigs → Sub-Clinical ($S_c$) (Fraction) | 0.15 |
| $\theta_3$ | Exposed pigs → Chronic-Carrier ($C_c$) (Fraction) | 0.05 |
| $\nu$ | Daily detection rate of Infected (Passive) | 0.01 |
| $P_{dh1}$ | Detection rate for 10% mortality (Clinical C) | 0.95 |
| $P_{dh2}$ | Detection rate for 10% mortality (Sub-Clinical $S_c$) | 0.75 |
| $P_{dh3}$ | Detection rate for 10% mortality (Chronic-Carrier $C_c$) | 0.5 |
| $P_{dl}$ | Detection rate for low mortality | 0.01 |
| $T_{sen}$ | Test sensitivity (Fraction) | 0.9 |
| $P_{ij}$ | Pig transfer probability per day | 0.05 |
| $\eta_1$ | Fraction of fecal shed within pen | 0.9 |
| $\eta_2$ | Fraction of fecal shed between pens | 0.1 |
| $\xi_1$ | Fraction of fecal ingested within pen | 0.9 |
| $\xi_2$ | Fraction of fecal ingested between pens | 0.1 |


'''
## Feral ↔ Barn Coupling Parameters

| Parameter   | Meaning | Value |
|-------------|---------|-------|
| $\beta_{fp}$ | Transmission: feral → farm pigs (via pixel env) per day per load unit | 0.0015 |
| $\beta_{pf}$ | Transmission: farm/barn → feral (via pixel env) per day per load unit | 0.1 |
| $\kappa_{fp}$ | Reachability of pixel viral load to farm pigs | 0.5 |
| $\kappa_{pf}$ | Reachability of pixel viral load to feral pigs | 0.5 |
| $K_V$ | Scaling factor for viral load | $10^3$ |
| $\eta_f$ | Fraction of feral shedding that enters pixel environment | 0.8 |
| $Q_{f\_shed}$ | Feral infected shedding per day (same units as env load) | $10^3$ |
| $\epsilon_{pix}$ | Daily pixel-level decay/cleaning of environmental viral load | 0.15 |


# Feral progression (simple SEIR)
We define a  schematic illustrating the transmission and population dynamics. This diagram outlines the progression of pigs through seven
compartments: Susceptible (S ), Exposed (E), Clinical (C), Sub-Clinical (S c), Chronic-Carrier (Cc), Detected (D), and Recovered (R). λi
represents the force of infection, indicating the rate at which pigs transition from the Susceptible (S ) to the Exposed (E) compartment.
i = 1, 2, ..., 7 defines the various pathways to exposure. θ = 1, 2, 3, specifies the transitions of Exposed pigs (E) to Clinical (C), Sub-Clinical
(S c), or Chronic-Carrier (Cc) compartments after the latency period. κ describes the status changes of pigs within the Clinical (C), Sub-Clinical
(S c), or Chronic-Carrier (Cc) compartments. δ is the detection rate of Infected pigs, encompassing those in the Clinical (C), Sub-Clinical (S c),
or Chronic-Carrier (Cc) compartments. γ denotes the recovery rate for pigs in each compartment.
<p align="center">
  <img width="900" alt="SchematicSIR" src="https://github.com/user-attachments/assets/79bf69b4-5bfb-4bbc-a1d3-cb428c516cf9" />
</p>

**Figure .** Schematic illustrating the transition of pigs to different compartments: Susceptible (S), Exposed (E), Clinical (C), Sub-Clinical (S<sub>c</sub>), Carrier (C<sub>c</sub>), and Detected (D).  
$\lambda_i$ represents the force of infection, indicating the rate at which pigs transition from the Susceptible to the Exposed compartment.  
$i = 1, 2, \ldots, 7$ defines the various pathways through which exposure can occur.  
$\theta = 1, 2, 3$ specifies the transitions of Exposed pigs to Clinical, Sub-Clinical, or Chronic-Carrier compartments after the latency period.  
$\kappa$ describes the status changes of pigs within the Clinical, Sub-Clinical, and Carrier compartments.  
$\delta$ is the detection rate of Infected pigs, encompassing those in the Clinical, Sub-Clinical, and Carrier compartments.

'''
## Transmission Routes and Model Overview

We develop a comprehensive multi-level modeling framework to investigate the transmission dynamics of African Swine Fever Virus (ASFV) across commercial swine farms in North Carolina. At the farm level, the model simulates viral dissemination across pens, rooms, and barns by incorporating **ten distinct transmission routes** that capture both direct and indirect interactions among pigs. Environmental viral load released by infected rooms is tracked and contributes to spillover risk in neighboring, previously unaffected rooms.

To extend transmission beyond individual farms, we incorporate a **spatially explicit grid system (1 km × 1 km resolution)**. Infected farm units elevate environmental contamination in adjacent cells, creating **ripple effects of infection across the landscape**. 

The framework also integrates **human-mediated transmission**, including virus introduction by farm workers, contaminated trucks, feed transport, and pig movements. To further enhance realism, **feral pig movement** is modeled using habitat-dependent probability functions. Feral pigs stochastically migrate between pixels, shedding viral material into the environment. Their decisions are governed by a Bernoulli process based on habitat suitability, and the viral load in each pixel is dynamically updated over time.

Grounded in **real U.S. farm data**—including barn capacity, pen configurations, and empirically derived viral transfer dynamics—this model provides a practical simulation of ASFV spread under field conditions and enables risk-based intervention testing.

### ASFV Transmission Routes Captured in the Model

| Category                | Route Description |
|-------------------------|------------------|
| **Within-Pen**          | Direct pig-to-pig contact (clinical/subclinical shedders) |
| **Within-Pen (Indirect)** | Fecal / oro-nasal environmental exposure |
| **Between-Pens (Same Room)** | Airborne drift or runoff-based spread |
| **Between-Rooms (Same Barn)** | Ventilation-mediated or movement-assisted spread |
| **Environmental Spillover (Within Barn)** | Viral load released into shared barn atmosphere |
| **Worker-Mediated**     | Farm staff movement between rooms/barns |
| **Truck-Mediated**      | Contamination carried by feed, livestock or service vehicles |
| **Pig Movement (Between Farms)** | Introduction of infected animals |
| **Feral Pig Interface** | Spillover between wild boar and domestic barns |

---
We present a schematic about the multiple routes for ASFV spread.
<p align="center">
  <img width="800" alt="CompleteRoutes" src="https://github.com/user-attachments/assets/598d67f8-0d8e-445c-bdd1-276669cfd075" />
</p>

**Figure .** We illustrate the seven transmission routes: direct nose-to-nose contact within pens, within-pen fecal transmission, within-pen aerosol transmission, between-pen direct nose-to-nose contact, between-pen fecal transmission, between-pen airborne transmission, and human transmission.

## Class: `Pen`

Lowest unit in the hierarchy:

> **Pixel → Room → Barn → Pen**

<p align="center">
  <img width="800" alt="SpatialSchematic" src="https://github.com/user-attachments/assets/ce2c8d5e-a060-497b-82bc-644a2a0d71a4" />
</p>

**Figure .** Hierarchical framework illustrating multi-scale transmission dynamics of ASFV from the raster to the pen level. The model begins at the **Raster Level**, where the entire map of North Carolina is discretized into a grid-based raster structure. Each raster cell (grid) contains multiple swine farms and is subject to environmental contamination. Viral load is transmitted from one grid to its neighbors with spatial decay. Feral pig movement across the raster facilitates spatial dissemination of viral particles to adjacent grids, further amplifying environmental contamination.  

At the **Pixel Level**, within-grid dynamics are captured, wherein farms within the same raster cell may exchange viral load, especially if infected farms act as localized sources of transmission to neighboring susceptible farms.  

At the **Farm Level**, the model tracks viral load generated at each individual farm. An infected farm can house one or more barns, and viral transmission can occur from infected barns to susceptible barns within the same farm boundary.  

At the **Barn Level**, transmission between barns is modeled. Once a barn becomes infected, it can act as a reservoir of viral load that may be transmitted to other barns through shared resources or environmental contamination.  

At the **Room Level**, within-barn dynamics are simulated, where viral particles can move between rooms of an infected barn. Shared ventilation or indirect contact pathways facilitate this route of transmission.  

Finally, at the **Pen Level**, detailed within-room dynamics are modeled. ASFV transmission occurs through direct pig-to-pig contact, environmental contamination, and indirect routes such as aerosol dispersion and human-mediated contamination.

Features:

- Creates and distributes pigs by infection status.
- Tracks first day of status transition.
- Maintains compartment counts: `S`, `E`, `Clinical`, `Subclinical`, `Carrier`, `Detected`, `Dead`.
- Stores spatial location within barn layout.
- Tracks environmental viral loads (fecal, airborne).
- Logs daily time-series data.

---

## Class: `Barn`

Configurable properties:

- Defines rooms, pens, initial infections, pen sizes.
- Includes viral retention and dispersal factors within rooms.
- Maps to neighboring pixels for higher-level spread.

Responsibilities:

- Simulates within-barn disease spread.
- Tracks viral transfer between rooms.
- Builds full pen layout and seeds infections.
- Models environmental retention and dispersal.
- Uses interaction matrix for cross-room contamination.
- Computes exposure pressure across rooms.
- Collects Susceptible → Exposed events.
- Supports mapping to spatial grid.

---

## Class: `Pixel`

Represents grid cells in the farm-level spatial layer.

- Identified by `(row, column)` coordinates.
- Contains list of rooms within that pixel.
- Tracks neighbors for cross-pixel spread.

Data loading:

- Loads `pixels.csv` → builds pixel objects + adjacency.
- Generates `neighbor_dict` for spatial connectivity.
- Generates `room_pixel_mapping` for linking rooms to pixels.

Supports hierarchical spread modeling:

- Within-pen  
- Between-pen (room)  
- Between-room (barn)  
- Between-pixel (landscape)
  
![barn_infectionpng](https://github.com/user-attachments/assets/82e0ae51-d95d-4df0-8c8e-485499df498e)
Average viral loads generated through fecal transmission (top panel) and aerosol transmission (bottom panel) across 24 pens over periods of (A, D) 5, (B, E) 10, and (C,
F) 30 days.
Dispersal rules:

| Path           | Retention |
|---------------|-----------|
| Same pixel    | 100%      |
| Direct neighbor | 80%     |
| Secondary neighbor | 60% |

---

# Wild-Pig Transmission Layer

### Model Overview

Spatially explicit transmission model linking **wild pig herds** with **domestic barns**.

### Movement

- A feral pig herd moves stochastically between pixels.
- Movement is habitat-suitability dependent.
- Higher suitability → higher chance of staying.

### Viral Load Dynamics

- Viral load stored at pixel level.
- Movement shifts viral load between pixels.
- Farms also contribute to pixel viral load.

Sources of contamination:

- Pen/room shedding  
- Cross-room dispersal  
- Pixel-to-pixel contamination  
- Feral pig contributions

### Viral Decay

- Environmental decay reduces persistence.

### Integration

Total viral load at pixel:farms + feral pigs
# Truck Network Transmission Layer

In addition to airborne and wildlife-driven spread, the model includes **vehicle-mediated transmission**, where livestock, feed, service, or rendering trucks act as **mechanical carriers** of virus between farms.

- Each truck follows a **travel route** visiting multiple farms over time.
- If a farm is infected during a visit, the vehicle may **pick up contamination**.
- Upon arriving at the next farm, it may **deposit trace viral material**, contributing to that farm’s exposure pressure.
- The strength of this indirect transmission depends on:
  - **Farm infection status at the time of visit**
  - **Natural decay**

<p align="center">
  <img width="600" alt="TruckNetwork" src="https://github.com/user-attachments/assets/100ca14d-73ef-42f8-8629-008a430988a2" />
</p>

**Figure Y.** Schematic representation of the truck-mediated transmission pathway. Viral particles from **infected farms** contaminate truck surfaces and floors, which act as fomites during farm-to-farm movements. Upon visiting a **susceptible farm**, the contaminated truck deposits surviving viral particles, determined by the environmental survival rate ($\delta$). The deposited viral load is then proportionally distributed across barns, rooms, and pens, with airflow and dust dispersion patterns (modeled via CFD matrices) influencing the allocation. This process determines the effective exposure ($Z_t$) and the force of infection ($\lambda_t = \beta_t \times Z_t$) within susceptible pens.



