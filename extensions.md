---
layout: default
title: Extensions
nav_order: 4
description: "Extensibility Documentation"
---

# Experiment Configuration

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

# Multi-Objective Optimization

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