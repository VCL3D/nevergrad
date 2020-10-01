---
layout: default
title: Benchmark Server
nav_order: 5
description: "The Benchmark Server Application"
---

When `performance_capture.exe` is run without the `--benchmark_server` flag and without any additional command line arguments, it can be used to load a project file in order to inspect data and see some visualizations of selected individual error terms.

1. To open a project click `File -> Open`.

2. Use the widget manager to show groups of additional widgets. The first button enable the `Player` and `Visualization` widgets, while the second enables `Error Selector`, `Errors` and `Pose Parameters` widgets

<p align="center">
<img width=100 src="../assets/images/perfcap/widget_manager.png"/>
</p>

2. Use the player widget to navigate throughout the recoding.
    
<p align="center">
<img width=250 src="../assets/images/perfcap/player.png"/>
</p>

3. Use the `Visualizations` widget to enable additional visualizations
    
<p align="center">
<img width=150 src="../assets/images/perfcap/visualizations.png"/>
</p>

    - Show Axis Triad: Enable visualization of the global 3D coordinate system at origin (0,0,0)
    - Show grid: Enables visualization of a virtual grid, aligned with the floor.
    - Show Point Cloud: Enable visualization of raw point cloud of the recording
    - Use Device Color: Visualize the Point Cloud with different color for each camera view-point.
    - Show Frustums: Visualize the locations of each camera viewpoint
    - Frustum Scale: Control the scale of the frustums
    - Point Size: Point size used to rendered the 3D Point Cloud of the recording
    - Show Target: This will show the 3D reconstructed mesh for the point cloud of the current frame. This constitutes the target for the template fitting problem. (i.e fit the animated template to the Target mesh)
    - Show Animated: This will show the animated template at the specified pose (as defined in the `Pose Parameters` widget)
    - Show Chamfer: This will enable color visualization of the Chamfer distance (<img src="https://render.githubusercontent.com/render/math?math=E_D">) on the animated template mesh
    - Split Meshes: Show animated and Target meshes side by side (or otherwise, visualize them in the common global 3D coordinate system)
    - Show Template: Visualizes the template mesh at its initial pose (i.e. in the pose in which it was reconstructed)
    - Show Skeleton: Visualizes the skeleton of the animated mesh
    - Show weights: Visualizes the domminant skinning weights on the animated template in a color-coded manner.Each color is assigned to a different limb.
    - Blend weights: Visualizes the blended skinning weights on the animated template in a color-coded manner.
    - Render 2D Silhouettes: Enables visualization of the silhouette error terms

    A screenshot of the depicted target and animated meshes is given below:
    
<p align="center">
<img width=350 src="../assets/images/perfcap/meshes.png"/>
</p>
    
    Sample visualization of chamfer and silhouette error terms as we move the elbow of the animated template are given next:

<p align="center">
<img width=500 src="../assets/images/perfcap/mesh_error_vis1.gif"/>
</p>

<p align="center">
<img width=500 src="../assets/images/perfcap/mesh_error_vis2.gif"/>
</p>

4. The `Pose Parameters` widget allows controlling the animated pose of the template mesh. Use this widget to set specific individual values to each joint's orientation and the root joint's translation. All values are in degrees, except for the root translation which is measured in meters. Make sure you check `Show Pose Values` in order to have the ability to edit the animatable parameters of the template mesh. Choose skinning type between `Dual Quaternion` and `Linear` (pick `Dual Quaternion` for higher quality skinning). Click reset to zero out all animation parameters (i.e. revert animated mesh to its default pose).

<p align="center">
<img width=350 src="../assets/images/perfcap/pose_params_dont_show.png"/>
</p>
<p align="center">
<img width=400 src="../assets/images/perfcap/pose_params_show.png"/>
</p>

4. Use the `Error selector` to select and enable computation of individual error terms. After making your selection, press `Refresh Frame` once. Evaluation of the error terms is executed after each modification of the animate template's pose parameters in the `Pose Parameters` widget.
    
<p align="center">
<img width=200 src="../assets/images/perfcap/error_selector.png"/>
</p>

5. Use the `Errors` widget to inspect individual error term values and small graphs, as you play with pose parameters in the `Pose Parameters widget`

<p align="center">
<img width=300 src="../assets/images/perfcap/error_vis.png"/>
</p>
