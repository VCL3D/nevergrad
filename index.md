---
layout: default
title: Introduction
nav_order: 1
description: "Documentation page for the Nevergrad Performance Capture Benchmark"
permalink: /
---

# Performance Capture Benchmark

## Description

The Performance Capture Benchmark is a real-world application benchmarking of zero-th order optimizers using [nevergrad](https://facebookresearch.github.io/nevergrad/).
Performance capture aims at fitting an animatable template 3D mesh to sensor data, and in this particular realization, post-processed live meshes of sensed volumetric (multi-view depth) data.

A dataset of 11 performances and subjects has been acquired via a Volumetric Capture System __\[[1](#VolCap)\]__. 
For the purposes of this benchmark and for each performance, a 3D animatable template mesh has been created along with the selection of one individual target frame. 
The benchmark aims to assess the performance of various zero-th optimizers (as implemented in nevergrad) to the task of fitting the animatable template mesh (via its animation parameters) to the sensed 3D data of the selected performance's target frame.

To realize the benchmark, we release Windows binaries of a Performance Capture Benchmark Server (Perfcap benchmark server) which is able to serve Objective Function evaluations for arbitrary template mesh animation parameter values for each one of the designed experiments. 
On the nevergrad side, we implemented a python client to this benchmark server, which acts as an ObjectiveFunction proxy for every benchmarked optimizer.

## Definitions

### Project

A **project** consists of sensed 3D data captured during the performance recording of a single human and a constructed animatable template of the performer.

### Experiment

As an **experiment** we define the problem of fitting the subject's template 3D mesh to a specific target frame of the captured performance. Each experiment instance is tightly bound to a project. 
Further, an experiment instance is bound to a specific objective function parameterization and to a specific optimizer under a pre-defined budget. 
Objective function parameterization consists of defining a subset of template's animation variables to optimize against (i.e. locking/excluding some degrees of freedom for some joints) and specifying weights of individual error terms.

## Table of Contents
- To execute the benchmark, download the data and executables, please see [Benchmark Execution](installation.md)
- To visualize the recorded data please see [Benchmark Server](server.md)
- An analysis of the objective function used to fit the meshes can be found at [Objective Function](objective_function.md)
- Details regarding the files being logged by the `perfcap server` and the `nevergrad client` can be found at [Benchmark Logs](logging.md)
- Implementation details regarding the communication between the `nevergrad client` and the `perfcap server` can be found at [Implementation](codedoc.md)
- Extra ways of using this benchmark or details about extending it can be found at [Benchmark Extensions](extensions.md)
- The results of our analysis can be found at the [Quantitative](quantitative.md) and [Qualitative](qualitative.md) results sections.


<a name="VolCap"/>__[1]__ Sterzentsenko, V., Karakottas, A., Papachristou, A., Zioulis, N., Doumanoglou, A., Zarpalas, D., & Daras, P. (2018, November). [A low-cost, flexible and portable volumetric capturing system](https://arxiv.org/pdf/1909.01207.pdf). In 2018 14th International Conference on Signal-Image Technology & Internet-Based Systems (SITIS) (pp. 200-207). IEEE.