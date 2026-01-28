---

## Copy-paste: docs/state_machine_code_walkthrough.md
```md
# pick_and_place_state_machine.py — Walkthrough (High-level)

## Flow
- Robot runs four states: **Home → Selecting Object → Picking & Placing → Done**
- Starts at Home, checks for objects.
- If objects exist: select one → pick/place → return Home → repeat.
- If none: transition to Done.

## How perception connects to the state machine
- `object_detector.py` updates/publishes detected objects.
- Controller reads detections and decides:
  - `are_objects_on_workbench()`
  - `select_random_object()`
  - `move_object()`

## Brief function descriptions
- `are_objects_detected`: checks if detections exist; if not, end task.
- `on_enter_home`: moves robot home; checks for objects; transitions accordingly.
- `on_enter_selecting_object`: selects one object; triggers next transition.
- `on_enter_picking_and_placing`: executes pick & place; returns home to continue loop.
- `on_enter_done`: final shutdown/logging.
- `create_state_machine_graph`: optionally generates a PNG of the machine graph.

## Flowchart (Mermaid)
```mermaid
flowchart LR
H[Home] -->|objects exist| S[Selecting Object]
S --> P[Picking & Placing]
P --> H
H -->|no objects| D[Done]
