# ASFV_Feral_Domesticated_Spread
We model ASFV spread within and between farms by combining barn-level viral transmission with a spatial grid that includes environmental contamination, vehicle spread, and feral pig movement to identify high-risk areas and guide biosecurity strategies.
# Spatial_ASFV_Spread
#1: Define the parameters. (beta, epsilon, xi, kappa, mortality etc.)
Class Pen
#1: We define a class ‘Pen’ to create and distribute pigs based on status.
#2: We keep track of the first day pigs changed status.
Pen class is the lowest unit of my hierarchy:
Pixel level → Room level → Barn level → Pen level → this class
It models disease spread among pigs in a single pen:
1.	Tracks how many pigs are in each compartment (S, E, Clinical, Subclinical, Carrier, Detected, Dead).
2.	 Tracks spatial location in barn layout.
3.	 Tracks environmental viral loads (fecal, airborne).
4.	Logs daily state for time series analysis.
Class Barn
#1: We define the configurations of the barn (pens, barns, initial infected, size of each pen, viral retention factor and viral dispersal factors within the room, neighbor pixel for dispersal)
• Models a single barn with multiple rooms and pens.
• Simulates disease spread among pigs at barn-level.
• Tracks viral load movement within and between rooms.
• Creates all pens in the barn, with spatial layout and infection seeding.
• Tracks room-level viral loads over time.
• Models retention (viral load staying in a room) and dispersion (spreading to other rooms).
• Uses an interaction matrix to define between-room environmental spread.
• Updates infection pressure in each room to drive new exposures.
• Collects Susceptible → Exposed transitions for analysis.
• Supports spatial mapping to pixels on a farm grid (for higher-level spread modeling).

**Class Pixel**
• Pixel class models grid cells of the farm.
•	Holds Pixel ID, grid location (row, column).
•	Contains list of rooms in that pixel.
•	Tracks neighboring pixels for spread modeling.
• Loads pixels.csv to build:
•	Pixel objects with room assignments.
•	Neighbor relationships between pixels.
• Builds neighbor_dict:
•	Maps each pixel to its neighbors for cross-pixel spread.
• Creates room_pixel_mapping:
•	Maps each room’s CSV file to its Pixel ID.
•	Supports assigning pixel_id to Pen objects in Barn.
• Enables multi-level spatial modeling:
•	Within-pen spread.
•	Between-pens in a room.
•	Between-rooms in a barn.
•	Between-pixels on the farm grid.
• Supports dispersal rules like:
•	100% of viral load stays in the same pixel.
•	80% spreads to immediate neighbors.
•	60% to neighbors-of-neighbors.

**Wild-Pigs Transmission**
Model Overview
Spatially explicit model tracking African swine fever virus (ASFV) transmission from wild (feral) pigs to commercial swine barns.
A single herd of feral pigs moves stochastically between neighboring pixels.
**Pig Movement**
Movement is probabilistic and depends on habitat suitability.
Herds either stay in the same pixel or relocate to a neighbor pixel based on a threshold decision.
Higher habitat suitability → greater chance of staying.
**Viral Load Accumulation**
Viral load is tracked at the pixel level.
When the herd relocates, viral load shifts from the source pixel to the destination pixel, where the herd continues to shed.
Viral load is also contributed by infected farm pigs within the same pixel.
**Accumulation includes:**
Shedding from pens/rooms within barns
Cross-room and intra-pixel dispersal
Inter-pixel contamination from neighboring pixels
Feral pig contributions
**Viral Decay**
Viral particles in the environment decay over time, reducing persistence.
Decay rate can be adjusted to reflect environmental conditions (temperature, soil, vegetation, etc.).
**Integration with Disease Model**
Total viral load at each pixel = farms + feral pigs.
This combined load determines exposure risk for domestic pigs in commercial barns.

