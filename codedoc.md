---
layout: default
title: Implementation documentation
nav_order: 9
description: "Code architecture documentation"
---

# Performance Benchmark Server API

The API of the benchmark server is based on JSON messages communicated over RabbitMQ. The API is based on a request-response pattern, i.e. for each request received by the nevergrad benchmark client, there is a corresponding response message from the benchmark server. Each request message is given a unique (UUID) `transaction_id` identifying the request. The benchmark server embeds `transaction_id` to its replies, to allow for the client to match the response to its outgoing request (this scheme makes easier support for batched requests and multi-threading/multi-processing in the client side). Further, request messages contain a `reply_id` field which is used by the benchmark server as a RabbitMQ `routing_key` in order to route the reply to a specific nevergrad benchmark client instance (this is again to support for multi-processing in client-side (i.e starting the benchmark with `--num_workers` greater than 1)).

## Request - Response Message Pairs

The Benchmark Server can serve four types of requests:

1. `set_experiment`
2. `start_experiment`
3. `stop_experiment`
4. `function_evaluation`

Their request-response API message definitions are given below.

### Set Experiment

#### Request
```json
{
	"request_info": {
		"reply_id": "ccdf242c-9efa-4023-9ecc-5096d7e21321",
		"transaction_id":"0abaa9d6-363f-4b8b-a46d-baf2b7217b74"
	},
	"args": {
		"action" : "set_experiment",
		"experiment_id": 1,
		"project_file_path": "./data/Ballet/Ballet.perfproj",
		"frame_index": 25,
		"error_weights": {
			"silhouette": 0.1,
            "chamfer_total": 1.0,
            "chamfer_interpenetration": 1.0,
            "surface_gradient_error": 0.01,
            "self_penetration": 1.0,
            "anthropomorfic_constraints":1.0,
            "point2plane" : 0.1
		}
	}
}
```
- `experiment_id`: must be the integer designated in the `experiment{X}.json` included in the folder `nevergrad/benchmark/perfcap/experiment_configuration`. This is to facilitate logging. The target mesh of the benchmark instance is always saved in `benchmark_server/live_meshes/experiment_{experiment_id}.ply`.
- `frame_index`: sets the target mesh's frame number inside the performance recording.
- `project_file_path`: defines which project file to load. must be a relative path with respect to the `performance_capture.exe`.
- `error_weights` : a dictionary defining error term weights. All possible names for `error_weights` are given in the above example. Ommiting a name for `error_weights` implicitly sets its weight to 0.

The `set_experiment` message is effective only the first time it is received by the benchmark server. Subsequent messages are ignored. This implies that benchmark_server cannot switch projects on the-fly. If you need to switch project for your experiment you need to restart benchmark server.

#### Response

```json
{
	"reply_info": {
		"transaction_id":"0abaa9d6-363f-4b8b-a46d-baf2b7217b74"
	},
	"args": {
		"status" : 0,
		"message" : "Experiment set successfully"
	}
}
```
`status`: can be either 0 (Success) or 1 (Failure)
`message`: A string representation of the status returned by the benchmark serer

### Start Experiment


#### Request
```json
{
	"request_info": {
		"reply_id": "ccdf242c-9efa-4023-9ecc-5096d7e21321",
		"transaction_id":"0cc239f1-363f-4b8b-a46d-ddf212345555"
	},
	"args": {
		"action" : "start_experiment",
		"experiment_tag_id" : "0f65dabf-3381-4b2e-9f9a-2b65f8e5e850"
	}
}
```

`experiment_tag_id`: a unique identifier generated client-side that uniquely identifies the performance capture experiment. An experiment_tag_id is unique across project, optimizer and budget. It is used to facilitate logging. All objective function evaluation requests (i.e. optimizer's exploration history) and the final converged animated pose of the template is saved under the `experiment_tag_id`. See `./benchmark_server/logs/{experiment_tag_id}.json` and `./benchmark_server./animated_meshes/{experiment_tag_id.ply}`.

In the benchmark client code-base this is also reffered as "experiment registration".

#### Response


```json
{
	"reply_info": {
		"transaction_id":"0cc239f1-363f-4b8b-a46d-ddf212345555"
	},
	"args": {
		"status" : 0,
		"message" : "Experiment successfully started"
	}
}
```
`status`: can be either 0 (Success) or 1 (Failure)
`message`: A string representation of the status returned by the benchmark serer

### Stop Experiment

#### Request
```json
{
	"request_info": {
		"reply_id": "ccdf242c-9efa-4023-9ecc-5096d7e21321",
		"transaction_id":"0cd239d-363f-4b8b-a46d-ddf2d23455dd"
	},
	"args": {
		"action" : "stop_experiment",
		"experiment_tag_id" : "0f65dabf-3381-4b2e-9f9a-2b65f8e5e850",
		"pose" : [
			{
				"joint_id" : -1,
				"values" : [0.0, 0.0, 0.0]
			},
			...
		]
	}
}
```

`experiment_tag_id`: the `experiment_tag_id` of the experiment that has finished.
`pose`: an array of [`joint_id`,`values`] pairs identifying the converged pose, i.e the optimizer's recommendation. The benchmark server will save the animated template in this pose in its logs.

This messages flushes benchmark server logs for the experiment specified by `experiment_tag_id`. In benchmark clinet code-base this is also refered as "experiment unregisteration".

#### Response

```json
{
	"reply_info": {
		"transaction_id":"0cd239d-363f-4b8b-a46d-ddf2d23455dd"
	},
	"args": {
		"status" : 0,
		"message" : "Experiment successfully stoped"
	}
}
```

`status`: can be either 0 (Success) or 1 (Failure)
`message`: A string representation of the status returned by the benchmark serer

### Function Evaluation

#### Request
```json
{
	"request_info": {
		"reply_id": "ccdf242c-9efa-4023-9ecc-5096d7e21321",
		"transaction_id":"0cd2ffd-363f-4b8b-f46d-ddf2d2f45fdd"
	},
	"args": {
		"action" : "function_evaluation",
		"experiment_id": 1,
		"experiment_tag_id" : "0f65dabf-3381-4b2e-9f9a-2b65f8e5e850",
		"pose" :
			[
				{
					"joint_id" : -1,
					"values" : [1.0,2.0,3.0]
				},
				{
					"joint_id" : 0,
					"values" : [3.0,4.0,5.0]
				}
			]

	}
}
```

`experiment_id`: The `experiment_id` of the current benchmark as defined in the `experiment{X}.json` included in the folder `nevergrad/benchmark/perfcap/experiment_configuration`. This is ignored for now, but included for future reference.
`experiment_tag_id`: The `experiment_tag_id` corresponding to this objective function evaluation. The server uses this info to append the requested objective function evaluation query point to its logs.
`pose`: an array of [`joint_id`,`values`] pairs identifying the pose (objective function evaluation query point) that the optimizer asked for.


#### Response

```json
{
	"reply_info": {
		"transaction_id":"0cd2ffd-363f-4b8b-f46d-ddf2d2f45fdd"
	},
	"args": {
			"silhouette": 0.7312,
            "chamfer_total": 0.031847,
            "chamfer_interpenetration": 0.01321,
            "surface_gradient_error": 0.01,
            "self_penetration": 0.0,
            "anthropomorfic_constraints": 0.0,
            "point2plane" : 0.3781,
            "total": 0.15977
		}
	}
}
```

In the objective function evaluation response individual (unweighted) error values for each one of the non-zero weighted error terms as defined by `set_experiment` are returned. Additionally, a `total` weighted sum of the errors is also returned, calculated based on the weights also defined by `set_experiment`.