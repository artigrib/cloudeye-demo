# CloudEye: from a phone walkthrough to a robot-ready map

[90-second demo](https://www.linkedin.com/feed/update/urn:li:activity:7504007563198464000/)

## What it does
Walk a room with a phone → metric point cloud → traversability map → per-robot verdict (path, tightest gap, "can't reach") → USD export into Isaac Sim.

## Pipeline
phone video (15 fps frames)
→ MapAnything (facebook/map-anything-apache) — metric point cloud
→ NVIDIA nvblox — TSDF/ESDF on GPU; the ESDF is the only map the planner reads
→ SAM3 segmentation + Gemma naming (via OpenRouter / Vertex AI)
→ reachability per robot radius; unobserved space is never free
→ Nemotron parses typed commands into robot missions
→ nvblox mesh + occupancy → USD → Isaac Sim

## Numbers from the demo
- 8 robot platforms (TurtleBot3, Go2, Husky A200, Jackal, LIMO, ROSbot XL…)
- own_0902_140657: TurtleBot3 21/24 objects reachable; own_0901_173903: Husky 10/17
- nvblox stage on an RTX 4090: ~150 s per room; full GPU stage ~4.5 min
- USD export validated with usdchecker, 0 errors

## Known limits
- Monocular reconstruction drifts; floating points become obstacles (multi-view filter in progress)
- Walls with gaps: unobserved ≠ free, but observed floor behind a gap is still a hole
- Semantics not yet run on 15 fps scenes

## Code
Private while the backend has no auth. Happy to walk through it, DM.
