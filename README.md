# RealSense 3D Reconstruction Studio

A single-file desktop app that scans a bottle with an **Intel RealSense D405** and turns it into a 3D mesh you can inspect and export.

**Pipeline:** D405 RGB-D stream → YOLOv8-seg bottle segmentation → RGB-D odometry + ICP tracking → keyframes → pose graph (loop closure) → TSDF fusion → triangle mesh → synchronized CAD-style inspection.

| Live segmented point cloud | Reconstructed bottle cloud |
|---|---|
| ![live](docs/images/live-segmented-cloud.jpg) | ![cloud](docs/images/bottle-point-cloud.jpg) |

![GUI](docs/images/gui-idle-1.png)

## Features
- Live YOLO segmentation of bottles; click one in the RGB view to select it
- Depth cleaning (range gate + flying-pixel removal) and live segmented point cloud
- Multi-view scanning with hybrid RGB-D odometry, guarded ICP refinement and keyframe selection
- Automatic scan review (keyframe count, azimuth/elevation coverage, registration quality)
- Reconstruction with loop closures, pose-graph optimisation, TSDF fusion and mesh cleanup
- Inspection tab: synced mesh / point-cloud viewports, wireframe, normals, bounding box, standard views
- Export mesh (PLY / OBJ / STL) or point cloud (PLY) in metres or millimetres; save/load projects

## Hardware
- Intel RealSense **D405** (USB 3), ~7-50 cm working range
- Optional NVIDIA GPU for faster YOLO (CPU works)

![D405](docs/images/d405-camera.jpg)

## Install
```bash
git clone https://github.com/<your-user>/realsense-3d-reconstruction-studio.git
cd realsense-3d-reconstruction-studio
python -m venv .venv
.venv\Scripts\activate        # Windows  (Linux/macOS: source .venv/bin/activate)
pip install -r requirements.txt
python realsense_3d_studio.py
```
`yolov8n-seg.pt` is downloaded automatically on first run. Python 3.9-3.11 is recommended (Open3D / pyrealsense2 wheel availability).

## Usage
1. Plug in the D405 and start the app; wait for "Connected" and "YOLO ready".
2. Place the bottle on a table, bring it into range, and click it in the RGB view.
3. **Start Scan**, then move the camera slowly around the *stationary* bottle.
4. **All Sides Captured** → read the review → **Reconstruct 3D Model**.
5. Inspect in the **Inspection** tab, then **Export Model...**.

## Tips and limitations
- Bottle bases are hidden by the table, so meshes are usually not watertight.
- Transparent/reflective bottles give poor depth; opaque or labelled ones scan best.
- Move slowly; if tracking is rejected, return to the last captured view.
- Detection targets the COCO `bottle` class; change the weights field to use a custom model.

## Coordinate conventions
Geometry is in metres. Scan world frame = first keyframe camera (+X right, +Y down, +Z forward). The inspection/export frame is that frame rotated 180° about X and centred on the mesh bounding box.

## License
MIT
