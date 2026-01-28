# Panda Pick-and-Place — Object Detection (ROS)

This repo contains my perception pipeline for a simulated pick-and-place task.  
The node detects **red/green/blue blocks** from an RGB camera stream, estimates **3D positions** using depth + camera model, and publishes detected objects for a state machine/controller to use.

## What it does
- Subscribes to:
  - `/camera/color/image_raw` (RGB)
  - `/camera/depth/image_raw` (Depth)
  - `/camera/color/camera_info` (Intrinsics)
- Segments blocks by color (HSV masking + contours)
- Computes:
  - pixel center + bounding box
  - depth at center
  - 3D position (camera frame → world frame)
  - box dimensions from projected corners
- Publishes detected objects to:
  - `/object_detection`

## Repo structure
```text
scripts/
  object_detector.py
docs/
  object_detector_code_walkthrough.md
  state_machine_code_walkthrough.md
  ```

  ## How to run 
  ```bash
  rosrun pick_and_place object_detector.py
  ```
