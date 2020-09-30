# Nevegrad Optimizer Benchmarking for 3D Performance Capture

## Description

This pull request regards the addition of a new benchmark to the nevergrad platform for the real-world noisy problem of 3D Human Performance Capture via fitting an animatable template 3D mesh to post-processed sensed 3D data.

The dataset consists of 3D captures of 11 human performances, recorded via a Volumetric Capture System of [1]. For the purposes of this benchmark and for each performance, a 3D animatable template mesh has been created along with the selection of one individual target frame. The benchmark aims to assess the performance of nevergrad optimizers to the task of fitting the animatable template mesh (via its animation parameters) to the sensed 3D data of the selected performance's target frame.

To realize the concept, along with this pull request, we release Windows binaries of a Performance Capture Benchmark Server (Perfcap benchmark server) which is able to serve Objective Function evaluations for arbitrary template mesh animation parameter values for each one of the designed experiments. To the nevergrad side, we implemented a python client to this benchmark server, which acts as an ObjectiveFunction proxy for every benchmarked optimizer.

## Definitions

### Project

A Project consists of sensed 3D data captured during the performance recording of a single human and a constructed animatable template of the performer.

### Experiment

As an experiment we define the problem of fitting the subject's template 3D mesh to a specific target frame of the captured performance. Each experiment instance is tightly bound to a project. Further, an experiment instance is bound to a specific objective function parameterization and to a specific optimizer under a pre-defined budget. Objective function parameterization consists of defining a subset of template's animation variables to optimize against (i.e. locking/excluding some degrees of freedom for some joints) and specifying weights of individual error terms.

# Objective Function

The objective function of each experiment is a weighted linear combination of five individual error terms:
<!--
$$
E(\mathbf{p})= \lambda_J E_J(\mathbf{p}) + \lambda_D E_D(\mathbf{p}) + \lambda_S E_S(\mathbf{p}) + \lambda_P E_P(\mathbf{p}) +\lambda_A E_A(\mathbf{p}),
$$
-->

<p align="center">
<img src="https://render.githubusercontent.com/render/math?math=E(\mathbf{p})=%20\lambda_J%20E_J(\mathbf{p})%20%2B%20\lambda_D%20E_D(\mathbf{p})%20%2B%20\lambda_S%20E_S(\mathbf{p})%20%2B%20\lambda_P%20E_P(\mathbf{p})%20%2B\lambda_A%20E_A(\mathbf{p}),">
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

![Animation](./assets/images/animationc.png)


![Errors](./assets/images/errors.png)

## Requirements

This pull request has been tested with Python 3.8.2. As of 30th September 2020, the code is compatible with the latest commit in nevegrad-master. A pre-requisite is to have RabbitMQ installed to a reachable network location

- [RabbitMQ](https://www.rabbitmq.com/)

Additional requirements for the Nevergrad Performance Capture Benchmark Client:
- aio-pika 6.6.1

Requirements for Performance Capture Benchmark Server:

- Microsoft Windows OS (Tested under Windows 10 Professional)
- NVIDIA CUDA 9.2
- OpenGL 4.6

The benchmark server and the nevergrad benchmark python client may be running at different workstations without problem. Messaging between Nevergrad Benchmark and the Performance Capture Benchmark Server is realized through a RabbitMQ server which must be reachable from the machine hosting the nevergrad benchmark as well as the machine hosting the Performance Capure Benchmark Server.

## Run

To run the nevergrad performance capture benchmark follow the steps bellow:

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

3. Run the benchmark by executing the standard nevergrad interface for benchmark execution, by defining the Performance Capture benchmark name:

    `python -m nevergrad.benchmark perfcap_bench1 --repetitions=10 --plot`

The experiment names can be anything between `perfcap_bench1` and `perfcap_bench11`. Each benchmark executes a pre-defined configuration of an experiment for a pre-defined range of optimizers and budgets.

## Benchmark Server Documentation

## Logging

### Nevergrad Logging
### Benchmark Server

## Extensions

### Experiment Configuration

### Multi-Objective Optimization

## Benchmark Results

### Quantitative Results

#### Nevergrad Experiments

#### Optimizer Evaluation

### Qualitative Results

#### Convergence Pose & Visualized Error

#### Optimizer Exploration Evolution



`
[1] Volumetric Capture
`

`
[2] Performance Capture
`

`
[3] Optimizer Benchmarking
`