---
layout: default
title: Benchmark Logs
nav_order: 6
description: "Logging Details"
---


# Nevergrad Logging
Each performance capture benchmark (`perfcap_benchX`) is **implicitly** associated with a unique id, called `experiment_id`, an integer value in [1,11]. This identifier is associated with a group of experiments all sharing in common the same project data and the same target fitting frame. `experiment_id` is defined inside `./benchmark/perfcap/experiment_config/experimentX.json` configuration files and is aligned with the number of the benchmark. (i.e. `perfcap_bench1` has `experiment_id` 1, `perfcap_bench2` has `experiment_id` 2, etc). Additionally, each individual experiment receives an `experiment_tag_id`, uniquely identifying the optimizer and budget under which the experiment was run. `experiment_id_tag` is also appended in the nevergrad CSV logs and is an identifier which can help in associating nevergrad logs with benchmark server logs.

# Benchmark Server Logging

For each finished experiment identified by `experiment_tag_id`, the benchmark server writes 3 kinds of logs:

* `benchmark_server/logs/{experiment_tag_id}.json`: for each iteration of the optimization this file contains the objective function query point (animation poses), the individual error term values and the aggregated (weighed) error value of the objective function at the this point.
* `benchmark_server/live_meshes/experiment_{experiment_id}.ply`: the 3D mesh that corresponds to the target frame of the performance that the animated template should fit.
* `benchmark_server/animated_meshes/{experiment_tag_id}.ply}`: these files contain the animated template 3D mesh at the optimizer recommendation pose, after optimization finishes.
