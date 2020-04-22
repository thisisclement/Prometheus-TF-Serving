# Configuring Prometheus to work with Tensorflow Serving ModelServer in Docker containers


#### Contents
- [Ideal architecture](https://github.com/thisisclement/Prometheus-TF-Serving#ideal-architecture)
- [Tensorflow Serving configuration](https://github.com/thisisclement/Prometheus-TF-Serving#configuring-of-tensorflow-serving-modelserver)
- [Prometheus configuration](https://github.com/thisisclement/Prometheus-TF-Serving#Installation-and-configuration-of-Prometheus)
- [Graphana configuration](https://github.com/thisisclement/Prometheus-TF-Serving#Installation-and-configuration-of-Graphana)
---

## Ideal architecture

<p align="center">
<img width="500" src="./docs/img/prometheus_architecture.png"  />
</p>

## Configuring of Tensorflow Serving ModelServer
You need to configure the ModelServer to expose the monitoring endpoint.

To do that you need to ensure that while running your docker image at first, you need to add a `--monitoring_config_file=<path to monitoring config file>/monitoring_config.conf` to your docker command.

Example command with other options:
```sh
docker run -d -p 8500:8500 -p 8501:8501 --mount type=bind,source=/Users/clementow/docker/monitored,target=/models/bert --mount type=bind,source=/Users/clementow/docker/modelServerConfig,target=/models/modelServerConfig -t --entrypoint=tensorflow_model_server tensorflow/serving --rest_api_port=8501 --model_config_file=models/modelServerConfig/config.conf --monitoring_config_file=/models/modelServerConfig/monitoring_config.conf --model_config_file_poll_wait_seconds=60 --allow_version_labels_for_unavailable_models=true
```
More info on the monitoring_config_file option [here|.

Let's verify that the monitoring metrics are exposed by going to `http://localhost:8501/monitoring/prometheus/metrics`. You should see an example output like so:
```
# TYPE :tensorflow:api:op:using_fake_quantization gauge
# TYPE :tensorflow:cc:saved_model:load_attempt_count counter
:tensorflow:cc:saved_model:load_attempt_count{model_path="/models/bert/16",status="success"} 1
:tensorflow:cc:saved_model:load_attempt_count{model_path="/models/bert/17",status="success"} 1
# TYPE :tensorflow:cc:saved_model:load_latency counter
:tensorflow:cc:saved_model:load_latency{model_path="/models/bert/16"} 8021486
:tensorflow:cc:saved_model:load_latency{model_path="/models/bert/17"} 7960377
# TYPE :tensorflow:cc:saved_model:load_latency_by_stage histogram
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="10"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="18"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="32.4"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="58.32"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="104.976"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="188.957"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="340.122"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="612.22"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="1102"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="1983.59"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="3570.47"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="6426.84"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="11568.3"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="20823"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="37481.3"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="67466.4"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="121440"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="218591"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="393464"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="708235"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="1.27482e+06"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="2.29468e+06"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="4.13043e+06"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="7.43477e+06"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="1.33826e+07"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="2.40887e+07"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="4.33596e+07"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="7.80473e+07"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="1.40485e+08"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="2.52873e+08"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="4.55172e+08"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="8.19309e+08"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="1.47476e+09"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="init_graph",le="+Inf"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_sum{model_path="/models/bert/16",stage="init_graph"} 340650
:tensorflow:cc:saved_model:load_latency_by_stage_count{model_path="/models/bert/16",stage="init_graph"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="10"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="18"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="32.4"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="58.32"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="104.976"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="188.957"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="340.122"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="612.22"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="1102"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="1983.59"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="3570.47"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="6426.84"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="11568.3"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="20823"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="37481.3"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="67466.4"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="121440"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="218591"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="393464"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="708235"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="1.27482e+06"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="2.29468e+06"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="4.13043e+06"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="7.43477e+06"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="1.33826e+07"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="2.40887e+07"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="4.33596e+07"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="7.80473e+07"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="1.40485e+08"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="2.52873e+08"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="4.55172e+08"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="8.19309e+08"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="1.47476e+09"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/16",stage="restore_graph",le="+Inf"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_sum{model_path="/models/bert/16",stage="restore_graph"} 7.68072e+06
:tensorflow:cc:saved_model:load_latency_by_stage_count{model_path="/models/bert/16",stage="restore_graph"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="10"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="18"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="32.4"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="58.32"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="104.976"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="188.957"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="340.122"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="612.22"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="1102"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="1983.59"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="3570.47"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="6426.84"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="11568.3"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="20823"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="37481.3"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="67466.4"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="121440"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="218591"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="393464"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="708235"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="1.27482e+06"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="2.29468e+06"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="4.13043e+06"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="7.43477e+06"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="1.33826e+07"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="2.40887e+07"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="4.33596e+07"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="7.80473e+07"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="1.40485e+08"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="2.52873e+08"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="4.55172e+08"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="8.19309e+08"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="1.47476e+09"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="init_graph",le="+Inf"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_sum{model_path="/models/bert/17",stage="init_graph"} 325202
:tensorflow:cc:saved_model:load_latency_by_stage_count{model_path="/models/bert/17",stage="init_graph"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="10"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="18"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="32.4"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="58.32"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="104.976"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="188.957"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="340.122"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="612.22"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="1102"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="1983.59"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="3570.47"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="6426.84"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="11568.3"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="20823"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="37481.3"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="67466.4"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="121440"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="218591"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="393464"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="708235"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="1.27482e+06"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="2.29468e+06"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="4.13043e+06"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="7.43477e+06"} 0
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="1.33826e+07"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="2.40887e+07"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="4.33596e+07"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="7.80473e+07"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="1.40485e+08"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="2.52873e+08"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="4.55172e+08"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="8.19309e+08"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="1.47476e+09"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_bucket{model_path="/models/bert/17",stage="restore_graph",le="+Inf"} 1
:tensorflow:cc:saved_model:load_latency_by_stage_sum{model_path="/models/bert/17",stage="restore_graph"} 7.63505e+06
:tensorflow:cc:saved_model:load_latency_by_stage_count{model_path="/models/bert/17",stage="restore_graph"} 1
# TYPE :tensorflow:core:direct_session_runs counter
:tensorflow:core:direct_session_runs{} 8
# TYPE :tensorflow:core:eager_context_created gauge
# TYPE :tensorflow:core:graph_build_calls counter
:tensorflow:core:graph_build_calls{} 5
# TYPE :tensorflow:core:graph_build_time_usecs counter
:tensorflow:core:graph_build_time_usecs{} 4921475
# TYPE :tensorflow:core:graph_optimization_usecs counter
:tensorflow:core:graph_optimization_usecs{kind="GraphOptimizationPass",name="AccumulateNV2RemovePass"} 28
:tensorflow:core:graph_optimization_usecs{kind="GraphOptimizationPass",name="IsolatePlacerInspectionRequiredOpsPass"} 45
:tensorflow:core:graph_optimization_usecs{kind="GraphOptimizationPass",name="LowerFunctionalOpsPass"} 57
:tensorflow:core:graph_optimization_usecs{kind="GraphOptimizationPass",name="ParallelConcatRemovePass"} 21
:tensorflow:core:graph_optimization_usecs{kind="Grappler",name="arithmetic_optimizer"} 124997
:tensorflow:core:graph_optimization_usecs{kind="Grappler",name="constant_folding"} 297533
:tensorflow:core:graph_optimization_usecs{kind="Grappler",name="dependency_optimizer"} 51946
:tensorflow:core:graph_optimization_usecs{kind="Grappler",name="function_optimizer"} 217473
:tensorflow:core:graph_optimization_usecs{kind="Grappler",name="implementation_selector"} 9065
:tensorflow:core:graph_optimization_usecs{kind="Grappler",name="layout"} 4622
:tensorflow:core:graph_optimization_usecs{kind="Grappler",name="loop_optimizer"} 15785
:tensorflow:core:graph_optimization_usecs{kind="Grappler",name="memory_optimizer"} 73905
:tensorflow:core:graph_optimization_usecs{kind="Grappler",name="model_pruner"} 19510
:tensorflow:core:graph_optimization_usecs{kind="Grappler",name="remapper"} 27216
:tensorflow:core:graph_optimization_usecs{kind="Grappler",name="shape_optimizer"} 19577
# TYPE :tensorflow:core:graph_run_input_tensor_bytes histogram
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="1"} 2
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="4"} 2
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="16"} 2
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="64"} 4
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="256"} 4
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="1024"} 4
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="4096"} 8
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="16384"} 8
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="65536"} 8
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="262144"} 8
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="1.04858e+06"} 8
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="4.1943e+06"} 8
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="1.67772e+07"} 8
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="6.71089e+07"} 8
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="2.68435e+08"} 8
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="1.07374e+09"} 8
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="4.29497e+09"} 8
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="1.71799e+10"} 8
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="6.87195e+10"} 8
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="2.74878e+11"} 8
:tensorflow:core:graph_run_input_tensor_bytes_bucket{le="+Inf"} 8
:tensorflow:core:graph_run_input_tensor_bytes_sum{} 8278
:tensorflow:core:graph_run_input_tensor_bytes_count{} 8
# TYPE :tensorflow:core:graph_run_output_tensor_bytes histogram
:tensorflow:core:graph_run_output_tensor_bytes_bucket{le="1"} 0
:tensorflow:core:graph_run_output_tensor_bytes_bucket{le="4"} 0
:tensorflow:core:graph_run_output_tensor_bytes_bucket{le="16"} 0
:tensorflow:core:graph_run_output_tensor_bytes_bucket{le="64"} 0
:tensorflow:core:graph_run_output_tensor_bytes_bucket{le="256"} 4
:tensorflow:core:graph_run_output_tensor_bytes_bucket{le="1024"} 4
:tensorflow:core:graph_run_output_tensor_bytes_bucket{le="4096"} 4
:tensorflow:core:graph_run_output_tensor_bytes_bucket{le="16384"} 4
:tensorflow:core:graph_run_output_tensor_bytes_bucket{le="65536"} 4
:tensorflow:core:graph_run_output_tensor_bytes_bucket{le="262144"} 4
:tensorflow:core:graph_run_output_tensor_bytes_bucket{le="1.04858e+06"} 4
:tensorflow:core:graph_run_output_tensor_bytes_bucket{le="4.1943e+06"} 4
:tensorflow:core:graph_run_output_tensor_bytes_bucket{le="1.67772e+07"} 4
:tensorflow:core:graph_run_output_tensor_bytes_bucket{le="6.71089e+07"} 4
:tensorflow:core:graph_run_output_tensor_bytes_bucket{le="+Inf"} 4
:tensorflow:core:graph_run_output_tensor_bytes_sum{} 304
:tensorflow:core:graph_run_output_tensor_bytes_count{} 4
# TYPE :tensorflow:core:graph_run_time_usecs counter
:tensorflow:core:graph_run_time_usecs{} 2683607
# TYPE :tensorflow:core:graph_run_time_usecs_histogram histogram
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="1000"} 2
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="2000"} 2
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="4000"} 2
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="8000"} 2
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="16000"} 2
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="32000"} 2
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="64000"} 2
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="128000"} 2
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="256000"} 3
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="512000"} 6
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="1.024e+06"} 8
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="2.048e+06"} 8
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="4.096e+06"} 8
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="8.192e+06"} 8
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="1.6384e+07"} 8
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="3.2768e+07"} 8
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="6.5536e+07"} 8
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="1.31072e+08"} 8
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="2.62144e+08"} 8
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="5.24288e+08"} 8
:tensorflow:core:graph_run_time_usecs_histogram_bucket{le="+Inf"} 8
:tensorflow:core:graph_run_time_usecs_histogram_sum{} 2.68361e+06
:tensorflow:core:graph_run_time_usecs_histogram_count{} 8
# TYPE :tensorflow:core:graph_runs counter
:tensorflow:core:graph_runs{} 8
# TYPE :tensorflow:core:session_created gauge
:tensorflow:core:session_created{} 0
# TYPE :tensorflow:core:xla_compilation_time_usecs counter
# TYPE :tensorflow:core:xla_compilations counter
# TYPE :tensorflow:data:autotune counter
# TYPE :tensorflow:data:bytes_read counter
# TYPE :tensorflow:data:dense_feature counter
# TYPE :tensorflow:data:elements counter
# TYPE :tensorflow:data:fingerprint counter
# TYPE :tensorflow:data:optimization counter
# TYPE :tensorflow:data:ragged_feature counter
# TYPE :tensorflow:data:sparse_feature counter
# TYPE :tensorflow:mlir:import_failure_count counter
# TYPE :tensorflow:serving:model_warmup_latency histogram
# TYPE :tensorflow:serving:request_example_count_total counter
# TYPE :tensorflow:serving:request_example_counts histogram
# TYPE :tensorflow:serving:request_log_count counter
```

Once that is done you can then setup the Prometheus docker container.

## Installation and configuration of Prometheus

### Prometheus config file
Prometheus runs the service with a config file that you specify so that it knows where to pull the logs from. Specify the correct container IP address of the TF Serving container in `- targets:  `.
```yml
# my global config
global:
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's the tf modelServer itself.
scrape_configs:
 - job_name: 'tf-modelserver'
   static_configs:
           - targets: ['172.17.0.2:8501']
   metrics_path: 'monitoring/prometheus/metrics'
```

More info on configuration [here|https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config].

### Run docker image and using config file
Run the following docker command in the folder of where `prometheus.yml` is to get the latest Prometheus image from Docker Hub.

```sh
docker run -d -p 9090:9090 -v $PWD/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
```

You should be able to access the Prometheus WebUI through `localhost:9090`.

You have now a working Tensorflow Serving and Prometheus docker container running. The next step is to install Graphana to visualise the log results from TF Serving and then to Prometheus.

## Installation and configuration of Graphana

Running of Ubuntu Graphana image:
```sh
docker run -d --name=grafana -p 3000:3000 grafana/grafana:6.7.2-ubuntu\n
```

You should be able to access the Graphana WebUI through `localhost:3000`.
