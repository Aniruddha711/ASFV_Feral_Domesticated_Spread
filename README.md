# ASFV_Feral_Domesticated_Spread

**We model ASFV spread as a **layered system** that couples **within-farm infection dynamics** with **between-farm spatial dissemination**. Viral transmission occurs not only through **direct pig contact**, but also via **environmental contamination, vehicle-mediated transfer, and feral pig movement across the landscape**. By embedding barn-level epidemiological processes within a **spatial grid**, we capture how **local outbreaks escalate into broader regional spread**, enabling the identification of **high-risk transmission corridors** and evaluation of **targeted biosecurity strategies**.**

---

# Spatial_ASFV_Spread

### 1. Define the core parameters  
(e.g., `beta`, `epsilon`, `xi`, `kappa`, mortality rates, etc.)

---

## Class: `Pen`

Lowest unit in the hierarchy:

> **Pixel → Room → Barn → Pen**

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
- Adjustable based on temperature / soil / vegetation.

### Integration

Total viral load at pixel:

