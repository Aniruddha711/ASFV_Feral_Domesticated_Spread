# ASFV domestic and wild pig spread model

**We model ASFV spread as a **layered system** that couples **within-farm infection dynamics** with **between-farm spatial dissemination**. Viral transmission occurs not only through **direct pig contact**, but also via **environmental contamination, vehicle-mediated transfer, and feral pig movement across the landscape**. By embedding barn-level epidemiological processes within a **spatial grid**, we capture how **local outbreaks escalate into broader regional spread**, enabling the identification of **high-risk transmission corridors** and evaluation of **targeted biosecurity strategies**.**

---

# Spatial_ASFV_Spread

### 1. Define the core parameters  
xi_1 = 0.98
xi_2 = 0.01
eta_1 = 0.98
eta_2 = 0.01
Q_j = 25
Q_i = 5e6
Q_in = 5e6
epsilon_1 = 0.08
epsilon_2 = 0.7
Q_shed = 1000
V_b = 1e3
V_b_shed = 1e3
C_hum = 0.1
ninfected = 1
NN = 1e-9
p = 0.04
MortalityH = 1
T = 175
kappa_1 = 0.4
kappa_2 = 0.25
kappa_3 = 0.4
kappa_4=0.25 #Between pixel transmission
zeta_1 = 0.9

'''
# Feral ↔ Barn coupling params 
beta_fp = 0.0015     # feral → farm pigs (via pixel env) per day per load unit
beta_pf = 0.1     # farm/barn → feral (via pixel env) per day per load unit
kappa_fp = 0.5     # reachability of pixel viral load to farm pigs
kappa_pf = 0.5     # reachability to feral pigs
K_V = 1e3          # scaling for viral load

eta_f = 0.8        # fraction of feral shedding that enters pixel environment
Q_f_shed = 1e3     # feral infected shedding per day (same units as your env load)
epsilon_pix = 0.15 # daily pixel-level decay/cleaning for env viral load

# Feral progression (simple SEIR)
We define a  schematic illustrating the transmission and population dynamics. This diagram outlines the progression of pigs through seven
compartments: Susceptible (S ), Exposed (E), Clinical (C), Sub-Clinical (S c), Chronic-Carrier (Cc), Detected (D), and Recovered (R). λi
represents the force of infection, indicating the rate at which pigs transition from the Susceptible (S ) to the Exposed (E) compartment.
i = 1, 2, ..., 7 defines the various pathways to exposure. θ = 1, 2, 3, specifies the transitions of Exposed pigs (E) to Clinical (C), Sub-Clinical
(S c), or Chronic-Carrier (Cc) compartments after the latency period. κ describes the status changes of pigs within the Clinical (C), Sub-Clinical
(S c), or Chronic-Carrier (Cc) compartments. δ is the detection rate of Infected pigs, encompassing those in the Clinical (C), Sub-Clinical (S c),
or Chronic-Carrier (Cc) compartments. γ denotes the recovery rate for pigs in each compartment.
<img width="4041" height="1717" alt="SchematicSIR" src="https://github.com/user-attachments/assets/79bf69b4-5bfb-4bbc-a1d3-cb428c516cf9" />

sigma_EI = 1/4.0   # E→I per day (mean incubation ~4d)
gamma_IR = 1/30.0   # I→R per day (mean infectious ~7d)
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

<img width="3505" height="2036" alt="CompleteRoutes" src="https://github.com/user-attachments/assets/598d67f8-0d8e-445c-bdd1-276669cfd075" />



## Class: `Pen`

Lowest unit in the hierarchy:

> **Pixel → Room → Barn → Pen**
<img width="8315" height="13984" alt="SpatialSchematic" src="https://github.com/user-attachments/assets/1bf877a6-03e8-4cd0-937c-c055919a93be" />

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

<img width="1544" height="2024" alt="TruckNetwork" src="https://github.com/user-attachments/assets/100ca14d-73ef-42f8-8629-008a430988a2" />

---


