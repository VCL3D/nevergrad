---
layout: default
title: Benchmark Execution 
nav_order: 2
description: "Requirements and Installation"
---

# Requirements

This pull request has been tested with Python 3.8.2. As of 30th September 2020, the code is compatible with the latest commit in nevegrad-master. A pre-requisite is to have RabbitMQ installed to a reachable network location.

- [RabbitMQ](https://www.rabbitmq.com/)

Additional requirements for the Nevergrad Performance Capture Benchmark Client:
- aio-pika 6.6.1 (`pip install aio-pika==6.6.1`)

Requirements for Performance Capture Benchmark Server:

- Microsoft Windows OS (Tested under Windows 10 Professional)
- NVIDIA CUDA 9.2
- OpenGL 4.6

The benchmark server and the nevergrad benchmark python client may be running at different workstations without problem. Messaging between Nevergrad Benchmark and the Performance Capture Benchmark Server is realized through a RabbitMQ server which must be reachable from the machine hosting the nevergrad benchmark as well as the machine hosting the Performance Capure Benchmark Server.

<p align="center">
<img width=300 src="../assets/images/overview.gif"/>
</p>

# Installation

1. Download the Performance Capture Benchmark Server [binaries](https://github.com/VCL3D/PerformanceCapture/releases/tag/1.0) and extract it to a `root` folder.
2. Download the Performance Capture Benchmark [dataset](https://github.com/VCL3D/PerformanceCapture/releases/tag/dataset_1.0) and extract it to your `root` folder.

# Run

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