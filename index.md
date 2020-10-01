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

The dataset consists of 3D captures of 11 human performances, recorded via the Multi-View Volumetric Capture System of __\[[1](#VolCap)\]__. 
For the purposes of this benchmark and for each performance, a 3D animatable template mesh has been created along with the selection of one individual target frame. 
The benchmark aims to assess the performance of nevergrad optimizers to the task of fitting the animatable template mesh (via its animation parameters) to the sensed 3D data of the selected performance's target frame.

To realize the concept, along with this pull request, we release Windows binaries of a Performance Capture Benchmark Server (Perfcap benchmark server) which is able to serve Objective Function evaluations for arbitrary template mesh animation parameter values for each one of the designed experiments. 
On the nevergrad side, we implemented a python client to this benchmark server, which acts as an ObjectiveFunction proxy for every benchmarked optimizer.

## Definitions

### Project

A **project** consists of sensed 3D data captured during the performance recording of a single human and a constructed animatable template of the performer.

### Experiment

As an **experiment** we define the problem of fitting the subject's template 3D mesh to a specific target frame of the captured performance. Each experiment instance is tightly bound to a project. 
Further, an experiment instance is bound to a specific objective function parameterization and to a specific optimizer under a pre-defined budget. 
Objective function parameterization consists of defining a subset of template's animation variables to optimize against (i.e. locking/excluding some degrees of freedom for some joints) and specifying weights of individual error terms.


<a name="VolCap"/>__[1]__ Sterzentsenko, V., Karakottas, A., Papachristou, A., Zioulis, N., Doumanoglou, A., Zarpalas, D., & Daras, P. (2018, November). [A low-cost, flexible and portable volumetric capturing system](https://arxiv.org/pdf/1909.01207.pdf). In 2018 14th International Conference on Signal-Image Technology & Internet-Based Systems (SITIS) (pp. 200-207). IEEE.