# qcar2_msgs

`qcar2_msgs` is a ROS 2 interface-only package that defines custom messages used by QCar2 perception, planning, and lateral control modules.

This repository contains message contracts only (`msg/*.msg`). It does not provide runtime nodes, executables, or libraries.

## Package Scope

- Build type: `ament_cmake`
- Interface generation: `rosidl_default_generators`
- Runtime interface export: `rosidl_default_runtime`
- Message dependencies: `std_msgs`, `geometry_msgs`

## Message Catalog

### Perception Messages

`msg/Detection2D.msg`
- 2D detector output with class metadata and bounding box (`center_x`, `center_y`, `width`, `height` in pixels).
- Includes depth-aware localization fields:
  - Raw and sampled depth (`depth`, `depth_sample`)
  - Sample pixel coordinates (`depth_sample_u`, `depth_sample_v`)
  - 3D position in camera frame (`position_x`, `position_y`, `position_z`)

`msg/Detection2DArray.msg`
- Timestamped array wrapper for `Detection2D[]` with `std_msgs/Header`.

`msg/Object3D.msg`
- 3D object localization output with class metadata, confidence, and `geometry_msgs/Point position`.
- `position` is documented as map-frame position (meters).
- Includes scalar distance from `base_link` origin.

`msg/Object3DArray.msg`
- Timestamped array wrapper for `Object3D[]` with `std_msgs/Header`.

### Guidance and Path Messages

`msg/LateralGuidance.msg`
- Lateral guidance diagnostics and control-related state for path tracking.
- Includes:
  - Tracking geometry (`u_star`, `closest_point`, `cross_track_error_m`)
  - Heading terms (`psi_vehicle_rad`, `psi_path_tangent_rad`, `psi_vfg_rad`)
  - Curvature terms (`curvature_1pm`, lookahead and feedforward curvature fields)
  - Weight/vector terms (`w1_to_path`, `w2_tangent`, `v1_to_path_norm`, `v_vfg_norm`)
  - Status code and message (`status`, `status_msg`)
- Status constants:
  - `STATUS_OK=0`
  - `STATUS_NO_PATH=1`
  - `STATUS_TF_FAIL=2`
  - `STATUS_INVALID_PATH=3`
  - `STATUS_NUMERIC_FAIL=4`
  - `STATUS_LIMITED=5`

`msg/PhQuintic.msg`
- One PH quintic segment encoded as G1 Hermite data.
- Contains start/end points, start/end tangents, branch selector, and exact arc length.
- Comments indicate planar use (z unused) and map-frame convention for geometry.

`msg/PhQuinticPath.msg`
- Trajectory container with `header`, `traj_id`, and ordered `PhQuintic[] segments`.
- Intended for G1-continuous multi-segment path representation.

### Legacy Message

`msg/BezierCurve.msg`
- Legacy Bezier representation (`degree`, `control_points`, `length_m`, `curve_id`).
- Retained for compatibility with older integrations.

## Interface Maintenance Notes

When modifying message schemas:
1. Update the corresponding file in `msg/`.
2. Keep `CMakeLists.txt` `rosidl_generate_interfaces(...)` entries in sync.
3. Verify dependent publisher/subscriber packages are updated to the new schema.

## Repository Notes

- `package.xml` currently still has placeholder values for:
  - `<description>`
  - `<license>`
