# Autonomous Building Information Collection

## What is BIM?
An acronym for Building Information Modeling/Management. In brief, it's the digital twins that multiple tenants utilize to collaborate on the successful delivery of a building, or construction project. 

## What are the current methods used in BIM collection? 
It's currently industry method to utilize tools like Laser scanners, [[photogrammetry]], manual documentation, electrical panel schematics, and any other form of diagrams previously developed by CAD departments or architects to plan the assembly of massive building orchestrations. 
1. 3D Laser Scanning (3D CAD)
2. 2D Laser Scanning (2D CAD)
3. Schematics for pluming, Wiring, HVAC, etc.. 
4. Architectural designs 

## Types of Data Sources
- Schematics
- System Manuals
- Issues/Tasks associated with an area/component
- Measurements
- System Meta Data (electrical values, nut types, etc..)
- Blueprints
- CAD (2d/3D)
- Excel sheets of asset metadata
- Hand drawn layouts
- Software as a Service platforms (NetBox for rack management and documentation)

## Types of Data
- Wiring
  - Type (Electrical, Data (copper, fiber, air), Conduit)
  - Gauges
  - Age
  - Path
  - Circuit Types
  - Material
  - Sheathing 
  - Watts/Volts (current + max)
  - Length
  - Girth (outer)
  - life cycle (install date, end of life, warranty)
- Plumbing
  - Type (potable water, milk, chemical)
  - PSI (current + max)
  - Material (copper, cast iron, plex, pvc)
  - Length
  - Girth (inner, outer)
  - life cycle (install date, end of life, warranty)
- Flooring
  - Type (concrete, carpet, tile, wood)
  - Finish
  - Barriers
  - Fillers
  - Capacity
  - Scratch resistant ratings
  - Primary use
  - life cycle (install date, end of life, warranty)
- Walls
  - ...
- Lights
  - ...
- HVAC
  - ...
- Windows
  - ...
- Fire Suppression
  - ...
- Compliance
  - ADA
    - Signage
      - Type (...)
  - Fire Marshal 
    - Signage 
      - Type (...)
  - Health
    - Signage
      - Type (...)
- IT
  - Type (Intercom, Wireless, Network, Surveillance, Door Access)






# Challenges & Constraints
- Photo to Mesh quality for collection to model is not up to par without human interaction. There are some great platforms, but workflow is not documented; along with there being a lack of automated workflow. (upload thousands of photos and let the computer take over)
- NeRF technology recently released with no true workflow practiced. Generative AI uses photos to "fill the gaps" for photogrammetry - accuracy is the winner with this one. 
- AI for object segmentation has been around, but no good workflow developed. Object segmentation is useful for providing modelers with a singular object out of the gate to work with rather than attempting to segment billions of points for each little object they may need. 
- AI for text identification and correlation has no workflow, but the technology is prominent. This will be helpful for capturing asset tags, qr codes, hand-written text, etc...
- Lack of a data catalog for CAD/Modeling to easily find all of the necessary data they need when modeling. All photos, scans, videos should be searchable by meta data and image detection (ai). Something along the lines of Google image search where you can upload a reference image and it'll populate where that source is from and high confidence matches. 
- Collection times require extensive planning, expensive hardware/software, and long processing times with manual experienced human interaction. 

# Goals
- Automate Building Information Collection allowing the human to verify and assign. 
  - This may mean automating the scan to mesh process so the computer can calculate and process by area/room rather than attempting to generate the entire building at once with no form of path reference. 
- Visualize BIM data quicker with fewer human hours involved
- Lower the cost of entry for the initial ingestion cycle when onboarding a new client site. 
- Optimized for low touch collection, reducing the needs for clearances, training, tethering, and so much more that goes into being authorized to enter a space. 
- Integrate with information management platforms 
  - A good example of this would be with NetBox. If a survey crew captures a site with B12 patch port in a room, NetBox api should tell our system that B12 feeds back to the main distribution frame in building B on rack #1, U3-U5 with switch Meraki 225-48P last managed by Technician #1. 
    - It should also be able to highlight a path going back to that location in a digital twin, following the visible conduit and "generating" a path through the non-visible conduit. 
    - This provided information should also calculate the anticipated length of the runs, if not calculated in the switch or cable management platform (netbox). 
- Auto calculate identified objects by groups. # of exit signs, # of visible fire extinguishers, # of fire strobes, # of suppressions etc... for building compliance and planning. These items should be visible, therefore, automatically collected using traditional photo/video. 


--- 

# Resources: 
https://constructible.trimble.com/construction-industry/what-is-bim-building-information-modeling 

