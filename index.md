---
layout: default
title: Introduction
nav_order: 1
description: "Documentation page for the Nevergrad Performance Capture Benchmark"
permalink: /
---

# Performance Capture Benchmark

## Description

This pull request regards the addition of a new benchmark to the nevergrad platform for the real-world noisy problem of 3D Human Performance Capture via fitting an animatable template 3D mesh to post-processed sensed 3D data.

The dataset consists of 3D captures of 11 human performances, recorded via the Multi-View Volumetric Capture System of [1]. For the purposes of this benchmark and for each performance, a 3D animatable template mesh has been created along with the selection of one individual target frame. The benchmark aims to assess the performance of nevergrad optimizers to the task of fitting the animatable template mesh (via its animation parameters) to the sensed 3D data of the selected performance's target frame.

To realize the concept, along with this pull request, we release Windows binaries of a Performance Capture Benchmark Server (Perfcap benchmark server) which is able to serve Objective Function evaluations for arbitrary template mesh animation parameter values for each one of the designed experiments. To the nevergrad side, we implemented a python client to this benchmark server, which acts as an ObjectiveFunction proxy for every benchmarked optimizer.

## Definitions

### Project

A **project** consists of sensed 3D data captured during the performance recording of a single human and a constructed animatable template of the performer.

### Experiment

As an **experiment** we define the problem of fitting the subject's template 3D mesh to a specific target frame of the captured performance. Each experiment instance is tightly bound to a project. Further, an experiment instance is bound to a specific objective function parameterization and to a specific optimizer under a pre-defined budget. Objective function parameterization consists of defining a subset of template's animation variables to optimize against (i.e. locking/excluding some degrees of freedom for some joints) and specifying weights of individual error terms.

### Objective Function

The **objective function** of each experiment is a weighted linear combination of five individual error terms:
<!--
$$
E(\mathbf{p})= \lambda_J E_J(\mathbf{p}) + \lambda_D E_D(\mathbf{p}) + \lambda_S E_S(\mathbf{p}) + \lambda_P E_P(\mathbf{p}) +\lambda_A E_A(\mathbf{p}),
$$
-->

<p align="center">
<img src="https://render.githubusercontent.com/render/math?math=E(\mathbf{p})=%20\lambda_J%20E_J(\mathbf{p})%20%2B%20\lambda_D%20E_D(\mathbf{p})%20%2B%20\lambda_S%20E_S(\mathbf{p})%20%2B%20\lambda_P%20E_P(\mathbf{p})%20%2B\lambda_A%20E_A(\mathbf{p}),"/>
</p>

<!-- $\mathbf{p}$-->
where <img src="https://render.githubusercontent.com/render/math?math=\mathbf{p}"> denote the unknown variables, which in our case are the pose parameters that animate the template mesh into a specific pose in order to fit into the current live data.
The different error terms are:
- an extrapolated 3D Chamfer distance metric, <img src="https://render.githubusercontent.com/render/math?math=E_D">
- a surface alignment matching term, <img src="https://render.githubusercontent.com/render/math?math=E_S">
- a penalization term of mesh self-intersections, <img src="https://render.githubusercontent.com/render/math?math=E_P">
- the 2D projective silhouette error, <img src="https://render.githubusercontent.com/render/math?math=E_J">
- an anthropometric penalization of unnatural human poses, <img src="https://render.githubusercontent.com/render/math?math=E_A">

These can be categorized with respect to their domain:

| <img src="https://render.githubusercontent.com/render/math?math=3D">   |      <img src="https://render.githubusercontent.com/render/math?math=2D">      |  Pose |
|:----------:|:-------------:|:------:|
| Chamfer distance (<img src="https://render.githubusercontent.com/render/math?math=E_D">) <!-- $E_D$ --> |  Silhouette error (<img src="https://render.githubusercontent.com/render/math?math=E_J">) <!-- $E_J$ --> | Anthropometric prior (<img src="https://render.githubusercontent.com/render/math?math=E_A">) <!-- $E_A$ --> |
| Surface alignment(<img src="https://render.githubusercontent.com/render/math?math=E_S">) <!-- $E_S$ --> |       |    |
| Self-penetration error (<img src="https://render.githubusercontent.com/render/math?math=E_P">) <!-- $E_P$ --> |  |     |

<!--
Three of these are defined in the three-dimensional space:
- an extrapolated 3D Chamfer distance metric, <img src="https://render.githubusercontent.com/render/math?math=E_D">
- a surface alignment matching term, <img src="https://render.githubusercontent.com/render/math?math=E_S">
- a penalization term of mesh self-intersections, <img src="https://render.githubusercontent.com/render/math?math=E_P">

One in the two-dimensional space:
- the 2D projective silhouette error, <img src="https://render.githubusercontent.com/render/math?math=E_J">

And a regularization term for the unknown variables (pose parameters):
- an anthropometric penalization of unnatural human poses, <img src="https://render.githubusercontent.com/render/math?math=E_A">
-->

<!--
From a data-fitting perspective, the **data terms** are:
- the Chamfer distance,  <img src="https://render.githubusercontent.com/render/math?math=E_D">
- surface alignment, <img src="https://render.githubusercontent.com/render/math?math=E_S">, and,
- silhouette error, <img src="https://render.githubusercontent.com/render/math?math=E_J">,
-->

<!--
while the **constraints** are provided by:
- the self-penetration term, <img src="https://render.githubusercontent.com/render/math?math=E_P">, and,
- the anthropometric prior term, <img src="https://render.githubusercontent.com/render/math?math=E_A">
-->
or a data fitting perspective:

| **Data Terms**   |      **Constraints**      |
|:----------:|:-------------:|
| Chamfer distance (<img src="https://render.githubusercontent.com/render/math?math=E_D">) <!-- $E_D$ --> |  Self-penetration error (<img src="https://render.githubusercontent.com/render/math?math=E_P">) <!-- $E_P$ --> |
| Surface alignment (<img src="https://render.githubusercontent.com/render/math?math=E_S">) <!-- $E_S$ --> |    Anthropometric prior (<img src="https://render.githubusercontent.com/render/math?math=E_A">) <!-- $E_A$ -->   |
| Silhouette error (<img src="https://render.githubusercontent.com/render/math?math=E_J">) <!-- $E_J$ --> |  |

Our complete objective as formulated above is a linear weighted combination of these terms as weighted by the respective weights <img src="https://render.githubusercontent.com/render/math?math=\lambda"> <!-- $\lambda$ -->. More details can be found in [3].

<!--![Animation](./assets/images/animationc.png)-->
<p align="center">
<img width=500 src="./assets/images/animationc.png"/>
</p>

![Errors](./assets/images/errors.png)

## Requirements

This pull request has been tested with Python 3.8.2. As of 30th September 2020, the code is compatible with the latest commit in nevegrad-master. A pre-requisite is to have RabbitMQ installed to a reachable network location.

- [RabbitMQ](https://www.rabbitmq.com/)

Additional requirements for the Nevergrad Performance Capture Benchmark Client:
- aio-pika 6.6.1

Requirements for Performance Capture Benchmark Server:

- Microsoft Windows OS (Tested under Windows 10 Professional)
- NVIDIA CUDA 9.2
- OpenGL 4.6

The benchmark server and the nevergrad benchmark python client may be running at different workstations without problem. Messaging between Nevergrad Benchmark and the Performance Capture Benchmark Server is realized through a RabbitMQ server which must be reachable from the machine hosting the nevergrad benchmark as well as the machine hosting the Performance Capure Benchmark Server.

<p align="center">
<img width=300 src="./assets/images/overview.gif"/>
</p>

## Installation

1. Download the Performance Capture Benchmark Server [binaries](https://github.com/VCL3D/PerformanceCapture/releases/tag/1.0) and extract it to a `root` folder.
2. Download the Performance Capture Benchmark [dataset](https://github.com/VCL3D/PerformanceCapture/releases/tag/dataset_1.0) and extract it to your `root` folder.

## Run

To run the nevergrad Performance Capture Benchmark follow the steps bellow:

1. Start benchmark server by running the following command inside the bin folder
(or edit and run `run_benchmark_server.bat` found inside the same folder)

    `
    performance_capture.exe --benchmark_server --benchmark_server_rmq_uri "amqp://user:pass@127.0.0.1:5672" --benchmark_server_ng_exchange_in "ng-bench.in" --benchmark_server_ng_exchange_out "ng-bench.out"
    `

    Argument descriptions:
    * `--benchmark_server` : sets performance_capture application to benchmark_server mode
    * `--benchmark_server_rmq_uri`: AMQP uri pointing to the RabbitMQ server
    * `--benchmark_server_ng_exchange_in`: The RabbitMQ exchange that the nevergrad python client listens at
    * `--benchmark_server_ng_exchange_out`: The RabbitMQ exchange that the nevergrad python client writes to

2. Edit RabbitMQ connection details in `nevergrad/benchmark/perfcap/experiment_config/rmqsettings.json`

```json
{
    "uri": "amqp://user:pass@127.0.0.1:5672",
    "ng_exchange_in": "ng-bench.in",
    "ng_exchange_out": "ng-bench.out"
}
```

- `uri:` the URI of the RabbitMQ server. Must be the same endpoint as define for Benchmark Server's `--benchmark_server_rmq_uri`
- `ng_exchange_in`: the exchange name that the nevergrad python client listens to. Must be the same exchange name as the one defined in Benchmark Server's `--benchmark_server_ng_exchange_in`.
- `ng_exchange_out`: the exchange name that the nevergrad python client writes to. Must be the same exchange name as the one define in Benchmark Server's `--benchmark_server_ng_exchange_out`.

3. Run the benchmark by executing the standard nevergrad interface for benchmark execution, by defining the Performance Capture Benchmark name (i.e):

    `python -m nevergrad.benchmark perfcap_bench1 --repetitions=10 --plot`

The experiment names can be anything between `perfcap_bench1` and `perfcap_bench11`. Each benchmark executes a pre-defined configuration of an experiment for a pre-defined range of optimizers and budgets.

## Benchmark Server Documentation

When `performance_capture.exe` is run without the `--benchmark_server` flag and without any additional command line arguments, it can be used to load a project file in order to inspect data and see some visualizations of selected individual error terms.

1. To open a project click `File -> Open`.

2. Use the widget manager to show groups of additional widgets. The first button enable the `Player` and `Visualization` widgets, while the second enables `Error Selector`, `Errors` and `Pose Parameters` widgets

    ![WidgetManager](./assets/images/perfcap/widget_manager.png)

2. Use the player widget to navigate throughout the recoding.

    ![Player](./assets/images/perfcap/player.png)

3. Use the `Visualizations` widget to enable additional visualizations

    ![Visualziations](./assets/images/perfcap/visualizations.png)

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

    ![Meshes](./assets/images/perfcap/meshes.png)

    Sample visualization of chamfer and silhouette error terms as we move the elbow of the animated template are given next:

    ![MeshError1](./assets/images/perfcap/mesh_error_vis1.gif)
    ![MeshError2](./assets/images/perfcap/mesh_error_vis2.gif)

4. The `Pose Parameters` widget allows controlling the animated pose of the template mesh. Use this widget to set specific individual values to each joint's orientation and the root joint's translation. All values are in degrees, except for the root translation which is measured in meters. Make sure you check `Show Pose Values` in order to have the ability to edit the animatable parameters of the template mesh. Choose skinning type between `Dual Quaternion` and `Linear` (pick `Dual Quaternion` for higher quality skinning). Click reset to zero out all animation parameters (i.e. revert animated mesh to its default pose).

    ![PoseParametersHidden](./assets/images/perfcap/pose_params_dont_show.png)
    ![PoseParametersShown](./assets/images/perfcap/pose_params_show.png)

4. Use the `Error selector` to select and enable computation of individual error terms. After making your selection, press `Refresh Frame` once. Evaluation of the error terms is executed after each modification of the animate template's pose parameters in the `Pose Parameters` widget.

    ![Error Selector](./assets/images/perfcap/error_selector.png)

5. Use the `Errors` widget to inspect individual error term values and small graphs, as you play with pose parameters in the `Pose Parameters widget`

    ![Errors](./assets/images/perfcap/error_vis.png)

## Logging

### Nevergrad Logging
Each performance capture benchmark (`perfcap_benchX`) is **implicitly** associated with a unique id, called `experiment_id`, an integer value in [1,11]. This identifier is associated with a group of experiments all sharing in common the same project data and the same target fitting frame. `experiment_id` is defined inside `./benchmark/perfcap/experiment_config/experimentX.json` configuration files and is aligned with the number of the benchmark. (i.e. `perfcap_bench1` has `experiment_id` 1, `perfcap_bench2` has `experiment_id` 2, etc). Additionally, each individual experiment receives an `experiment_tag_id`, uniquely identifying the optimizer and budget under which the experiment was run. `experiment_id_tag` is also appended in the nevergrad CSV logs and is an identifier which can help in associating nevergrad logs with benchmark server logs.

### Benchmark Server Logging

For each finished experiment identified by `experiment_tag_id`, the benchmark server writes 3 kinds of logs:

* `benchmark_server/logs/{experiment_tag_id}.json`: for each iteration of the optimization this file contains the objective function query point (animation poses), the individual error term values and the aggregated (weighed) error value of the objective function at the this point.
* `benchmark_server/live_meshes/experiment_{experiment_id}.ply`: the 3D mesh that corresponds to the target frame of the performance that the animated template should fit.
* `benchmark_server/animated_meshes/{experiment_tag_id}.ply}`: these files contain the animated template 3D mesh at the optimizer recommendation pose, after optimization finishes.


## Extensions

### Experiment Configuration

Each benchmark experiment can be configured via editing the files located in `benchmark/perfcap/experiment{X}.json`.

```python
"benchmark_server_args" : {
    "experiment_id": {int} # this is the experiment_id. must be unique across all experiment{X}.json files.
    "project_file_path": {string} # must be a path to a project file relative to he performance_capture.exe binary
    "frame_index": {int},  # defines the target frame where the animated template should fit to.
    "error_weights": {  # error
        "silhouette": 0.1,              # error weight for E_J
        "chamfer_total": 1.0,           # error weight for chamfer distance E_D
        "surface_gradient_error": 0.01, # error weight for surface distance E_S
        "self_penetration": 1.0,        # error weight for self-penetration E_P
        "anthropomorfic_constraints":1.0    # error weight for anthropomorphic term E_A
    }
},
"ng_function_args" : {
    "initial_pose": [  # defines the initial pose of the animated template (i.e. initial point of the optimizer)
    {
        "joint_id": -1, # joint ids are integers in range [-1,17], with -1 being the translation of the root joint in meters and the reset of the joint_ids corresponding to the respective joint rotations in degrees.
        "values":[0, 0, 0]  # list of 3 floating point numbers each one corresponding to a value for each one of the 3 Degrees of Freedom (DOFs) of this joint.
    },
    ...
    "relative_search_space" : {bool} # if True, variable bounds are with respect to the initial pose (true), or are given in absolute units (false)

    "normalize" :  {bool} # if true, pose parameters are normalized from range [bounds_min,bounds_max] to [-1,1]

    "variables": [      # this defines the variables to solve for.
    {
        "joint_id":-1,      # defining variables corresponding this joint
        "DOFs" : [true, true, true],    # if DOFs[i] is true it means that the i-th Degree of Freedom of the joint participates in optimization
        "mutation_variances":[0.1,0.1,0.1], # mutation_variances for each Degree of freedom marked True (in the same order as they appear in DOFs). len(mutation_variances) = sum(DOFs == true)
        "mutable_mutation_variances":[true,true,true],  # defines whether mutation variances are mutable of DOFs marked as true (in the same order as they appear in DOFs) - len(mutable_mutation_variances) = sum(DOFs == true)
        "bounds_min" : [-0.4,-0.4,-0.4], # min,max bounds for each DOF marked as True (order is same as they appear in DOFs) - len(bounds_min) = sum(DOFs == true)
        "bounds_max" : [0.4,0.4,0.4]

    },
    ...
}
```

### Multi-Objective Optimization

For each objective function evaluation, the Performance Capture Benchmark Server will return per term error values of the objective function as well as their weighted sum. In the current implementation the `PerfcapFunction` class which implements the Performance Capture Objective Function, will only return to the caller the `total` weighted sum of the individual error terms. A future extension is possible to extend the `PerfcapFunction` class to support multi-objective optimization under each individual error term. In particular, the response from the Benchmark Server for the function evaluations has the following json format:

```python
"reply_info": {
		"transaction_id": {guid}       # transaction_id of the request that this response corresponds to. (internal implementation detail)
	}
"args": {
		"chamfer_total": {float},      # error term corresponding to E_D
		"silhouette": {float}          # error term corresponding to E_J
        "self_penetration" : {float}   # error term corresponding to E_P
        "anthropomorfic_constraints" : {float} # error term corresponding to E_A
        "surface_gradient_error" : {float} # error term corresponding to E_S
        "total" : {float}           # the weighted combination of active individual error terms
	}
}
```

## Benchmark Results

### Quantitative Results

![LossVSBudget](./assets/images/quantitative/loss_vs_budget.png)
![HausVSBudget](./assets/images/quantitative/hauss_vs_budget.png)


#### Nevergrad Experiments

| Experiment#1   |      Experiment#2      |  Experiment#3 |
|:----------:|:-------------:|:-------------:|
| ![exp1](./assets/images/nevergrad/plots/perfcap_experiment1_plots/xpresults.png) | ![exp2](./assets/images/nevergrad/plots/perfcap_experiment2_plots/xpresults.png) | ![exp3](./assets/images/nevergrad/plots/perfcap_experiment3_plots/xpresults.png) |
| Experiment#4 | Experiment#5 | Experiment#6 |
| ![exp4](./assets/images/nevergrad/plots/perfcap_experiment4_plots/xpresults.png) | ![exp5](./assets/images/nevergrad/plots/perfcap_experiment5_plots/xpresults.png) | ![exp6](./assets/images/nevergrad/plots/perfcap_experiment6_plots/xpresults.png) |
| Experiment#7 | Experiment#8 |Experiment#9 |
| ![exp7](./assets/images/nevergrad/plots/perfcap_experiment7_plots/xpresults.png) | ![exp8](./assets/images/nevergrad/plots/perfcap_experiment8_plots/xpresults.png) | ![exp9](./assets/images/nevergrad/plots/perfcap_experiment9_plots/xpresults.png) |
| Experiment#10 | Experiment#11 | |
| ![exp10](./assets/images/nevergrad/plots/perfcap_experiment10_plots/xpresults.png) | ![exp11](./assets/images/nevergrad/plots/perfcap_experiment11_plots/xpresults.png) | |


| Experiment#1   |      Experiment#2      |  Experiment#3 |
|:----------:|:-------------:|:-------------:|
| ![exp1](./assets/images/nevergrad/plots/perfcap_experiment1_plots/fight_all.png) | ![exp2](./assets/images/nevergrad/plots/perfcap_experiment2_plots/fight_all.png) | ![exp3](./assets/images/nevergrad/plots/perfcap_experiment3_plots/fight_all.png) |
| Experiment#4 | Experiment#5 | Experiment#6 |
| ![exp4](./assets/images/nevergrad/plots/perfcap_experiment4_plots/fight_all.png) | ![exp5](./assets/images/nevergrad/plots/perfcap_experiment5_plots/fight_all.png) | ![exp6](./assets/images/nevergrad/plots/perfcap_experiment6_plots/fight_all.png) |
| Experiment#7 | Experiment#8 |Experiment#9 |
| ![exp7](./assets/images/nevergrad/plots/perfcap_experiment7_plots/fight_all.png) | ![exp8](./assets/images/nevergrad/plots/perfcap_experiment8_plots/fight_all.png) | ![exp9](./assets/images/nevergrad/plots/perfcap_experiment9_plots/fight_all.png) |
| Experiment#10 | Experiment#11 | |
| ![exp10](./assets/images/nevergrad/plots/perfcap_experiment10_plots/fight_all.png) | ![exp11](./assets/images/nevergrad/plots/perfcap_experiment11_plots/fight_all.png) | |


#### Optimizer Evaluation

### Qualitative Results

#### Visualized Error on the Converged Poses
Worm colors indicate low geometric error.
#### Experiment#2
![Exp2_1](./assets/images/qualitative/experiment2_1.png)
![Exp2_2](./assets/images/qualitative/experiment2_2.png)

#### Optimizer Exploration Evolution

##### Experiment#2

| CMA   |      DE      |  Powell |
|:----------:|:-------------:|:-------------:|
| ![CMA](./assets/images/exploration/perfcap_experiment2_gifs/CMA.gif) | ![CMA](./assets/images/exploration/perfcap_experiment2_gifs/DE.gif) | ![CMA](./assets/images/exploration/perfcap_experiment2_gifs/Powell.gif) |
| CMA+Powell | DiscreteOnePlusOne | NGO |
| ![chainCMAPowell](./assets/images/exploration/perfcap_experiment2_gifs/chainCMAPowell.gif) | ![DiscreteOnePlusOne](./assets/images/exploration/perfcap_experiment2_gifs/DiscreteOnePlusOne.gif) | ![NGO](./assets/images/exploration/perfcap_experiment2_gifs/NGO.gif) |
| TBPSA | RealSpacePSO |Shiwa |
| ![TBPSA](./assets/images/exploration/perfcap_experiment2_gifs/TBPSA.gif) | ![CMA](./assets/images/exploration/perfcap_experiment2_gifs/RealSpacePso.gif) | ![Shiwa](./assets/images/exploration/perfcap_experiment2_gifs/Shiwa.gif) |

##### Experiment#8

| CMA   |      DE      |  Powell |
|:----------:|:-------------:|:-------------:|
| ![CMA](./assets/images/exploration/perfcap_experiment8_gifs/CMA.gif) | ![CMA](./assets/images/exploration/perfcap_experiment8_gifs/DE.gif) | ![CMA](./assets/images/exploration/perfcap_experiment8_gifs/Powell.gif) |
| CMA+Powell | DiscreteOnePlusOne | NGO |
| ![chainCMAPowell](./assets/images/exploration/perfcap_experiment8_gifs/chainCMAPowell.gif) | ![DiscreteOnePlusOne](./assets/images/exploration/perfcap_experiment8_gifs/DiscreteOnePlusOne.gif) | ![NGO](./assets/images/exploration/perfcap_experiment8_gifs/NGO.gif) |
| TBPSA | RealSpacePSO |Shiwa |
| ![TBPSA](./assets/images/exploration/perfcap_experiment8_gifs/TBPSA.gif) | ![CMA](./assets/images/exploration/perfcap_experiment8_gifs/RealSpacePso.gif) | ![Shiwa](./assets/images/exploration/perfcap_experiment8_gifs/Shiwa.gif) |

##### Experiment#10

| CMA   |      DE      |  Powell |
|:----------:|:-------------:|:-------------:|
| ![CMA](./assets/images/exploration/perfcap_experiment10_gifs/CMA.gif) | ![CMA](./assets/images/exploration/perfcap_experiment10_gifs/DE.gif) | ![CMA](./assets/images/exploration/perfcap_experiment10_gifs/Powell.gif) |
| CMA+Powell | DiscreteOnePlusOne | NGO |
| ![chainCMAPowell](./assets/images/exploration/perfcap_experiment10_gifs/chainCMAPowell.gif) | ![DiscreteOnePlusOne](./assets/images/exploration/perfcap_experiment10_gifs/DiscreteOnePlusOne.gif) | ![NGO](./assets/images/exploration/perfcap_experiment10_gifs/NGO.gif) |
| TBPSA | RealSpacePSO |Shiwa |
| ![TBPSA](./assets/images/exploration/perfcap_experiment10_gifs/TBPSA.gif) | ![CMA](./assets/images/exploration/perfcap_experiment10_gifs/RealSpacePso.gif) | ![Shiwa](./assets/images/exploration/perfcap_experiment10_gifs/Shiwa.gif) |


`
[1] Volumetric Capture
`

`
[2] Performance Capture
`

`
[3] Optimizer Benchmarking
`