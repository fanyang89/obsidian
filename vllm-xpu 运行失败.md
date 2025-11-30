```
(my-vllm) ➜  my-vllm git:(main) ✗ python -m vllm.entrypoints.openai.api_server --model "Qwen/Qwen3-0.6B"
[W814 14:34:26.877210303 OperatorEntry.cpp:218] Warning: Warning only once for all operators,  other operators may also be overridden.
  Overriding a previously registered kernel for the same operator and the same dispatch key
  operator: aten::geometric_(Tensor(a!) self, float p, *, Generator? generator=None) -> Tensor(a!)
    registered at /pytorch/build/aten/src/ATen/RegisterSchema.cpp:6
  dispatch key: XPU
  previous kernel: registered at /pytorch/aten/src/ATen/VmapModeRegistrations.cpp:37
       new kernel: registered at /build/intel-pytorch-extension/build/Release/csrc/gpu/csrc/gpu/xpu/ATen/RegisterXPU_0.cpp:172 (function operator())
INFO 08-14 14:34:26 [__init__.py:241] Automatically detected platform xpu.
(APIServer pid=19130) INFO 08-14 14:34:27 [api_server.py:1805] vLLM API server version 0.10.1.dev636+g92ff41abe.d20250814
(APIServer pid=19130) INFO 08-14 14:34:27 [utils.py:326] non-default args: {}
(APIServer pid=19130) Downloading Model from https://www.modelscope.cn to directory: /home/fanmi/.cache/modelscope/hub/models/Qwen/Qwen3-0.6B
(APIServer pid=19130) 2025-08-14 14:34:28,239 - modelscope - INFO - Got 9 files, start to download ...
Downloading [LICENSE]: 100%|█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 11.1k/11.1k [00:01<00:00, 8.05kB/s]
Downloading [config.json]: 100%|███████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 726/726 [00:01<00:00, 515B/s]
Downloading [README.md]: 100%|███████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 13.6k/13.6k [00:01<00:00, 9.90kB/s]
Downloading [tokenizer_config.json]: 100%|███████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 9.50k/9.50k [00:01<00:00, 5.13kB/s]
Downloading [tokenizer.json]: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 10.9M/10.9M [00:02<00:00, 4.63MB/s]
Downloading [generation_config.json]: 100%|███████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 239/239 [00:02<00:00, 93.5B/s]
Downloading [configuration.json]: 100%|█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 73.0/73.0 [00:02<00:00, 28.5B/s]
Downloading [vocab.json]: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 2.65M/2.65M [00:01<00:00, 2.41MB/s]
Downloading [merges.txt]: 100%|███████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 1.59M/1.59M [00:02<00:00, 587kB/s]
Processing 9 items: 100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 9.00/9.00 [00:02<00:00, 3.16it/s]
(APIServer pid=19130) 2025-08-14 14:34:31,094 - modelscope - INFO - Download model 'Qwen/Qwen3-0.6B' successfully.█▉                                             | 7.00M/10.9M [00:01<00:00, 5.23MB/s]
(APIServer pid=19130) 2025-08-14 14:34:31,094 - modelscope - INFO - Creating symbolic link [/home/fanmi/.cache/modelscope/hub/models/Qwen/Qwen3-0.6B].███████████| 10.9M/10.9M [00:02<00:00, 5.60MB/s]
(APIServer pid=19130) Downloading Model from https://www.modelscope.cn to directory: /home/fanmi/.cache/modelscope/hub/models/Qwen/Qwen3-0.6B
(APIServer pid=19130) 2025-08-14 14:34:32,337 - modelscope - INFO - Target directory already exists, skipping creation.                                           | 1.00M/1.59M [00:02<00:01, 377kB/s]
(APIServer pid=19130) Downloading Model from https://www.modelscope.cn to directory: /home/fanmi/.cache/modelscope/hub/models/Qwen/Qwen3-0.6B
(APIServer pid=19130) 2025-08-14 14:34:33,544 - modelscope - INFO - Target directory already exists, skipping creation.
(APIServer pid=19130) INFO 08-14 14:34:37 [__init__.py:702] Resolved architecture: Qwen3ForCausalLM
(APIServer pid=19130) INFO 08-14 14:34:37 [__init__.py:1740] Using max model len 40960
(APIServer pid=19130) Downloading Model from https://www.modelscope.cn to directory: /home/fanmi/.cache/modelscope/hub/models/Qwen/Qwen3-0.6B
(APIServer pid=19130) 2025-08-14 14:34:39,031 - modelscope - INFO - Target directory already exists, skipping creation.
(APIServer pid=19130) Downloading Model from https://www.modelscope.cn to directory: /home/fanmi/.cache/modelscope/hub/models/Qwen/Qwen3-0.6B
(APIServer pid=19130) 2025-08-14 14:34:40,217 - modelscope - INFO - Target directory already exists, skipping creation.
(APIServer pid=19130) INFO 08-14 14:34:40 [scheduler.py:222] Chunked prefill is enabled with max_num_batched_tokens=2048.
(APIServer pid=19130) INFO 08-14 14:34:40 [xpu.py:170] Device name intel(r) graphics [0xe211] supports bfloat16. Please file an issue if you encounter any accuracy problems with bfloat16.
(APIServer pid=19130) WARNING 08-14 14:34:40 [_logger.py:68] CUDA graph is not supported on XPU, fallback to the eager mode.
(APIServer pid=19130) Downloading Model from https://www.modelscope.cn to directory: /home/fanmi/.cache/modelscope/hub/models/Qwen/Qwen3-0.6B
(APIServer pid=19130) 2025-08-14 14:34:42,466 - modelscope - INFO - Target directory already exists, skipping creation.
(APIServer pid=19130) Downloading Model from https://www.modelscope.cn to directory: /home/fanmi/.cache/modelscope/hub/models/Qwen/Qwen3-0.6B
(APIServer pid=19130) 2025-08-14 14:34:44,268 - modelscope - INFO - Target directory already exists, skipping creation.
[W814 14:34:46.010654202 OperatorEntry.cpp:218] Warning: Warning only once for all operators,  other operators may also be overridden.
  Overriding a previously registered kernel for the same operator and the same dispatch key
  operator: aten::geometric_(Tensor(a!) self, float p, *, Generator? generator=None) -> Tensor(a!)
    registered at /pytorch/build/aten/src/ATen/RegisterSchema.cpp:6
  dispatch key: XPU
  previous kernel: registered at /pytorch/aten/src/ATen/VmapModeRegistrations.cpp:37
       new kernel: registered at /build/intel-pytorch-extension/build/Release/csrc/gpu/csrc/gpu/xpu/ATen/RegisterXPU_0.cpp:172 (function operator())
INFO 08-14 14:34:46 [__init__.py:241] Automatically detected platform xpu.
(EngineCore_0 pid=19220) INFO 08-14 14:34:47 [core.py:620] Waiting for init message from front-end.
(EngineCore_0 pid=19220) INFO 08-14 14:34:47 [core.py:72] Initializing a V1 LLM engine (v0.10.1.dev636+g92ff41abe.d20250814) with config: model='Qwen/Qwen3-0.6B', speculative_config=None, tokenizer='Qwen/Qwen3-0.6B', skip_tokenizer_init=False, tokenizer_mode=auto, revision=None, override_neuron_config={}, tokenizer_revision=None, trust_remote_code=False, dtype=torch.bfloat16, max_seq_len=40960, download_dir=None, load_format=auto, tensor_parallel_size=1, pipeline_parallel_size=1, disable_custom_all_reduce=True, quantization=None, enforce_eager=True, kv_cache_dtype=auto, device_config=xpu, decoding_config=DecodingConfig(backend='auto', disable_fallback=False, disable_any_whitespace=False, disable_additional_properties=False, reasoning_backend=''), observability_config=ObservabilityConfig(show_hidden_metrics_for_version=None, otlp_traces_endpoint=None, collect_detailed_traces=None), seed=0, served_model_name=Qwen/Qwen3-0.6B, enable_prefix_caching=True, chunked_prefill_enabled=True, use_async_output_proc=True, pooler_config=None, compilation_config={"level":0,"debug_dump_path":"","cache_dir":"","backend":"","custom_ops":[],"splitting_ops":["vllm.unified_attention","vllm.unified_attention_with_output","vllm.mamba_mixer2"],"use_inductor":true,"compile_sizes":[],"inductor_compile_config":{"enable_auto_functionalized_v2":false},"inductor_passes":{},"use_cudagraph":true,"cudagraph_num_of_warmups":1,"cudagraph_capture_sizes":[512,504,496,488,480,472,464,456,448,440,432,424,416,408,400,392,384,376,368,360,352,344,336,328,320,312,304,296,288,280,272,264,256,248,240,232,224,216,208,200,192,184,176,168,160,152,144,136,128,120,112,104,96,88,80,72,64,56,48,40,32,24,16,8,4,2,1],"cudagraph_copy_inputs":false,"full_cuda_graph":false,"pass_config":{},"max_capture_size":512,"local_cache_dir":null}
[Gloo] Rank 0 is connected to 0 peer ranks. Expected number of connected peer ranks is : 0
[Gloo] Rank 0 is connected to 0 peer ranks. Expected number of connected peer ranks is : 0
[Gloo] Rank 0 is connected to 0 peer ranks. Expected number of connected peer ranks is : 0
[Gloo] Rank 0 is connected to 0 peer ranks. Expected number of connected peer ranks is : 0
[Gloo] Rank 0 is connected to 0 peer ranks. Expected number of connected peer ranks is : 0
(EngineCore_0 pid=19220) INFO 08-14 14:34:47 [parallel_state.py:1134] rank 0 in world size 1 is assigned as DP rank 0, PP rank 0, TP rank 0, EP rank 0
2025:08:14-14:34:47:(19220) |CCL_WARN| value of CCL_ATL_TRANSPORT changed to be ofi (default:mpi)
2025:08:14-14:34:47:(19220) |CCL_WARN| could not get local_idx/count from environment variables, trying to get them from ATL
2025:08:14-14:34:47:(19220) |CCL_ERROR| comm_selector.cpp:57 create_comm_impl: condition ccl::global_data::get().ze_data failed
ze_data was not initialized
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684] EngineCore failed to start.
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684] Traceback (most recent call last):
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]   File "/home/fanmi/workspace/my-vllm/vllm/v1/engine/core.py", line 675, in run_engine_core
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]     engine_core = EngineCoreProc(*args, **kwargs)
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]                   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]   File "/home/fanmi/workspace/my-vllm/vllm/v1/engine/core.py", line 476, in __init__
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]     super().__init__(vllm_config, executor_class, log_stats,
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]   File "/home/fanmi/workspace/my-vllm/vllm/v1/engine/core.py", line 78, in __init__
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]     self.model_executor = executor_class(vllm_config)
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]                           ^^^^^^^^^^^^^^^^^^^^^^^^^^^
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]   File "/home/fanmi/workspace/my-vllm/vllm/executor/executor_base.py", line 54, in __init__
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]     self._init_executor()
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]   File "/home/fanmi/workspace/my-vllm/vllm/executor/uniproc_executor.py", line 48, in _init_executor
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]     self.collective_rpc("init_device")
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]   File "/home/fanmi/workspace/my-vllm/vllm/executor/uniproc_executor.py", line 58, in collective_rpc
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]     answer = run_method(self.driver_worker, method, args, kwargs)
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]   File "/home/fanmi/workspace/my-vllm/vllm/utils/__init__.py", line 2997, in run_method
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]     return func(*args, **kwargs)
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]            ^^^^^^^^^^^^^^^^^^^^^
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]   File "/home/fanmi/workspace/my-vllm/vllm/worker/worker_base.py", line 603, in init_device
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]     self.worker.init_device()  # type: ignore
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]     ^^^^^^^^^^^^^^^^^^^^^^^^^
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]   File "/home/fanmi/workspace/my-vllm/vllm/v1/worker/xpu_worker.py", line 170, in init_device
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]     torch.distributed.all_reduce(torch.zeros(1).xpu(),
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]   File "/home/fanmi/workspace/my-vllm/.venv/lib/python3.12/site-packages/torch/distributed/c10d_logger.py", line 81, in wrapper
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]     return func(*args, **kwargs)
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]            ^^^^^^^^^^^^^^^^^^^^^
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]   File "/home/fanmi/workspace/my-vllm/.venv/lib/python3.12/site-packages/torch/distributed/distributed_c10d.py", line 2924, in all_reduce
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]     work = group.allreduce([tensor], opts)
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684]            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
(EngineCore_0 pid=19220) ERROR 08-14 14:34:47 [core.py:684] RuntimeError: oneCCL: comm_selector.cpp:57 create_comm_impl: EXCEPTION: ze_data was not initialized
(EngineCore_0 pid=19220) Process EngineCore_0:
(EngineCore_0 pid=19220) Traceback (most recent call last):
(EngineCore_0 pid=19220)   File "/usr/lib/python3.12/multiprocessing/process.py", line 314, in _bootstrap
(EngineCore_0 pid=19220)     self.run()
(EngineCore_0 pid=19220)   File "/usr/lib/python3.12/multiprocessing/process.py", line 108, in run
(EngineCore_0 pid=19220)     self._target(*self._args, **self._kwargs)
(EngineCore_0 pid=19220)   File "/home/fanmi/workspace/my-vllm/vllm/v1/engine/core.py", line 688, in run_engine_core
(EngineCore_0 pid=19220)     raise e
(EngineCore_0 pid=19220)   File "/home/fanmi/workspace/my-vllm/vllm/v1/engine/core.py", line 675, in run_engine_core
(EngineCore_0 pid=19220)     engine_core = EngineCoreProc(*args, **kwargs)
(EngineCore_0 pid=19220)                   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
(EngineCore_0 pid=19220)   File "/home/fanmi/workspace/my-vllm/vllm/v1/engine/core.py", line 476, in __init__
(EngineCore_0 pid=19220)     super().__init__(vllm_config, executor_class, log_stats,
(EngineCore_0 pid=19220)   File "/home/fanmi/workspace/my-vllm/vllm/v1/engine/core.py", line 78, in __init__
(EngineCore_0 pid=19220)     self.model_executor = executor_class(vllm_config)
(EngineCore_0 pid=19220)                           ^^^^^^^^^^^^^^^^^^^^^^^^^^^
(EngineCore_0 pid=19220)   File "/home/fanmi/workspace/my-vllm/vllm/executor/executor_base.py", line 54, in __init__
(EngineCore_0 pid=19220)     self._init_executor()
(EngineCore_0 pid=19220)   File "/home/fanmi/workspace/my-vllm/vllm/executor/uniproc_executor.py", line 48, in _init_executor
(EngineCore_0 pid=19220)     self.collective_rpc("init_device")
(EngineCore_0 pid=19220)   File "/home/fanmi/workspace/my-vllm/vllm/executor/uniproc_executor.py", line 58, in collective_rpc
(EngineCore_0 pid=19220)     answer = run_method(self.driver_worker, method, args, kwargs)
(EngineCore_0 pid=19220)              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
(EngineCore_0 pid=19220)   File "/home/fanmi/workspace/my-vllm/vllm/utils/__init__.py", line 2997, in run_method
(EngineCore_0 pid=19220)     return func(*args, **kwargs)
(EngineCore_0 pid=19220)            ^^^^^^^^^^^^^^^^^^^^^
(EngineCore_0 pid=19220)   File "/home/fanmi/workspace/my-vllm/vllm/worker/worker_base.py", line 603, in init_device
(EngineCore_0 pid=19220)     self.worker.init_device()  # type: ignore
(EngineCore_0 pid=19220)     ^^^^^^^^^^^^^^^^^^^^^^^^^
(EngineCore_0 pid=19220)   File "/home/fanmi/workspace/my-vllm/vllm/v1/worker/xpu_worker.py", line 170, in init_device
(EngineCore_0 pid=19220)     torch.distributed.all_reduce(torch.zeros(1).xpu(),
(EngineCore_0 pid=19220)   File "/home/fanmi/workspace/my-vllm/.venv/lib/python3.12/site-packages/torch/distributed/c10d_logger.py", line 81, in wrapper
(EngineCore_0 pid=19220)     return func(*args, **kwargs)
(EngineCore_0 pid=19220)            ^^^^^^^^^^^^^^^^^^^^^
(EngineCore_0 pid=19220)   File "/home/fanmi/workspace/my-vllm/.venv/lib/python3.12/site-packages/torch/distributed/distributed_c10d.py", line 2924, in all_reduce
(EngineCore_0 pid=19220)     work = group.allreduce([tensor], opts)
(EngineCore_0 pid=19220)            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
(EngineCore_0 pid=19220) RuntimeError: oneCCL: comm_selector.cpp:57 create_comm_impl: EXCEPTION: ze_data was not initialized
(APIServer pid=19130) Traceback (most recent call last):
(APIServer pid=19130)   File "<frozen runpy>", line 198, in _run_module_as_main
(APIServer pid=19130)   File "<frozen runpy>", line 88, in _run_code
(APIServer pid=19130)   File "/home/fanmi/workspace/my-vllm/vllm/entrypoints/openai/api_server.py", line 1918, in <module>
(APIServer pid=19130)     uvloop.run(run_server(args))
(APIServer pid=19130)   File "/home/fanmi/workspace/my-vllm/.venv/lib/python3.12/site-packages/uvloop/__init__.py", line 109, in run
(APIServer pid=19130)     return __asyncio.run(
(APIServer pid=19130)            ^^^^^^^^^^^^^^
(APIServer pid=19130)   File "/usr/lib/python3.12/asyncio/runners.py", line 194, in run
(APIServer pid=19130)     return runner.run(main)
(APIServer pid=19130)            ^^^^^^^^^^^^^^^^
(APIServer pid=19130)   File "/usr/lib/python3.12/asyncio/runners.py", line 118, in run
(APIServer pid=19130)     return self._loop.run_until_complete(task)
(APIServer pid=19130)            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
(APIServer pid=19130)   File "uvloop/loop.pyx", line 1518, in uvloop.loop.Loop.run_until_complete
(APIServer pid=19130)   File "/home/fanmi/workspace/my-vllm/.venv/lib/python3.12/site-packages/uvloop/__init__.py", line 61, in wrapper
(APIServer pid=19130)     return await main
(APIServer pid=19130)            ^^^^^^^^^^
(APIServer pid=19130)   File "/home/fanmi/workspace/my-vllm/vllm/entrypoints/openai/api_server.py", line 1850, in run_server
(APIServer pid=19130)     await run_server_worker(listen_address, sock, args, **uvicorn_kwargs)
(APIServer pid=19130)   File "/home/fanmi/workspace/my-vllm/vllm/entrypoints/openai/api_server.py", line 1870, in run_server_worker
(APIServer pid=19130)     async with build_async_engine_client(
(APIServer pid=19130)                ^^^^^^^^^^^^^^^^^^^^^^^^^^
(APIServer pid=19130)   File "/usr/lib/python3.12/contextlib.py", line 210, in __aenter__
(APIServer pid=19130)     return await anext(self.gen)
(APIServer pid=19130)            ^^^^^^^^^^^^^^^^^^^^^
(APIServer pid=19130)   File "/home/fanmi/workspace/my-vllm/vllm/entrypoints/openai/api_server.py", line 178, in build_async_engine_client
(APIServer pid=19130)     async with build_async_engine_client_from_engine_args(
(APIServer pid=19130)                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
(APIServer pid=19130)   File "/usr/lib/python3.12/contextlib.py", line 210, in __aenter__
(APIServer pid=19130)     return await anext(self.gen)
(APIServer pid=19130)            ^^^^^^^^^^^^^^^^^^^^^
(APIServer pid=19130)   File "/home/fanmi/workspace/my-vllm/vllm/entrypoints/openai/api_server.py", line 220, in build_async_engine_client_from_engine_args
(APIServer pid=19130)     async_llm = AsyncLLM.from_vllm_config(
(APIServer pid=19130)                 ^^^^^^^^^^^^^^^^^^^^^^^^^^
(APIServer pid=19130)   File "/home/fanmi/workspace/my-vllm/vllm/utils/__init__.py", line 1551, in inner
(APIServer pid=19130)     return fn(*args, **kwargs)
(APIServer pid=19130)            ^^^^^^^^^^^^^^^^^^^
(APIServer pid=19130)   File "/home/fanmi/workspace/my-vllm/vllm/v1/engine/async_llm.py", line 173, in from_vllm_config
(APIServer pid=19130)     return cls(
(APIServer pid=19130)            ^^^^
(APIServer pid=19130)   File "/home/fanmi/workspace/my-vllm/vllm/v1/engine/async_llm.py", line 119, in __init__
(APIServer pid=19130)     self.engine_core = EngineCoreClient.make_async_mp_client(
(APIServer pid=19130)                        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
(APIServer pid=19130)   File "/home/fanmi/workspace/my-vllm/vllm/v1/engine/core_client.py", line 102, in make_async_mp_client
(APIServer pid=19130)     return AsyncMPClient(*client_args)
(APIServer pid=19130)            ^^^^^^^^^^^^^^^^^^^^^^^^^^^
(APIServer pid=19130)   File "/home/fanmi/workspace/my-vllm/vllm/v1/engine/core_client.py", line 758, in __init__
(APIServer pid=19130)     super().__init__(
(APIServer pid=19130)   File "/home/fanmi/workspace/my-vllm/vllm/v1/engine/core_client.py", line 446, in __init__
(APIServer pid=19130)     with launch_core_engines(vllm_config, executor_class,
(APIServer pid=19130)          ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
(APIServer pid=19130)   File "/usr/lib/python3.12/contextlib.py", line 144, in __exit__
(APIServer pid=19130)     next(self.gen)
(APIServer pid=19130)   File "/home/fanmi/workspace/my-vllm/vllm/v1/engine/utils.py", line 706, in launch_core_engines
(APIServer pid=19130)     wait_for_engine_startup(
(APIServer pid=19130)   File "/home/fanmi/workspace/my-vllm/vllm/v1/engine/utils.py", line 759, in wait_for_engine_startup
(APIServer pid=19130)     raise RuntimeError("Engine core initialization failed. "
(APIServer pid=19130) RuntimeError: Engine core initialization failed. See root cause above. Failed core proc(s): {}
```
