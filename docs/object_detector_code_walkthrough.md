---
docs/object_detector_code_walkthrough.md
```md
# object_detector.py — Code Walkthrough (Flow + Functions)

## High-level flow
- Sets up camera + subscribes to ROS topics for RGB and depth images.
- Subscribes to `/camera/color/image_raw` and processes frames using `image_callback`.
- Converts RGB → HSV and detects **red/green/blue** regions using contour analysis.
- For each object:
  - computes pixel location + bounding box
  - retrieves depth
  - converts pixel+depth to **3D world coordinates**
  - computes real-world dimensions
- Publishes list of detected objects on `/object_detection`.

---

## Methods called when an image message is received
1. `image_callback(msg)` triggers on `/camera/color/image_raw`
2. Convert ROS Image → OpenCV BGR
3. Convert BGR → HSV
4. For each color (red/green/blue):
   - `get_mask()` → binary mask
   - `get_contours()` → contours
   - `get_detected_objects()` → object list (center/size/depth/color)
5. Draw bounding boxes + labels on image (for debugging/visualization)
6. Publish object properties using `publish_detected_objects()`

---

## Function descriptions

### `__init__`
Initializes camera settings, topics, transforms, HSV color thresholds, pinhole model, and publishers/subscribers.

### `get_image_dimensions`
Reads an image and extracts `height, width` for bounds checking and indexing.

### `get_camera_homogeneous_tranforms`
Builds camera pose transform using Gazebo model states + rotation; computes inverse for world↔camera conversions.

### `get_color_image`
Waits for a color image and converts ROS → OpenCV BGR.

### `get_depth_image`
Waits for depth image and converts ROS → OpenCV float32 depth image.

### `get_pinhole_camera_model`
Builds pinhole camera model from `/camera/color/camera_info` intrinsics.

### `get_model_position_from_gazebo`
Calls Gazebo service to obtain model pose (camera in world frame).

### `get_mask`
Generates binary mask from HSV thresholds for a color.

### `get_workbench_depth`
Uses black region (workbench) to estimate table depth as a reference.

### `get_pixel_depth`
Returns depth value at `(u, v)` if within bounds.

### `compute_mass_center`
Computes contour centroid using image moments.

### `get_detected_objects`
Filters contours, computes center/size/depth/color, and returns object list.

### `get_3D_point_from_pixel`
Projects pixel to 3D ray using pinhole model, scales using depth, converts camera→world.

### `get_pixel_from_3D_point`
Transforms world→camera then projects to pixel using pinhole model.

### `convert_point_from_camera_to_world`
Applies homogeneous transform (camera→world).

### `convert_point_from_world_to_camera`
Applies inverse transform (world→camera).

### `image_callback`
Full pipeline: convert image, segment by color, detect objects, draw boxes, publish results.

### `publish_detected_objects`
Builds and publishes message containing:
- position (x,y,z)
- dimensions
- color label

### `get_box_dimensions`
Converts bounding box corners to 3D and computes width/length.

### `get_contours`
Extracts external contours from a mask.

---

## Flowchart (Mermaid)
```mermaid
flowchart TD
A[/camera/color/image_raw/] --> B[image_callback]
B --> C[ROS Image -> OpenCV BGR]
C --> D[BGR -> HSV]
D --> E{For each color}
E --> F[get_mask]
F --> G[get_contours]
G --> H[get_detected_objects]
H --> I[get_pixel_depth + get_3D_point_from_pixel]
I --> J[get_box_dimensions]
J --> K[publish_detected_objects -> /object_detection]
