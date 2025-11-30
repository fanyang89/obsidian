```bash
docker run -it --rm --gpus all --ipc=host --net=host \
--ulimit memlock=-1 --ulimit stack=67108864 \
-e CUDA_VISIBLE_DEVICES=0 \
-w $(pwd) \
-v $(pwd):$(pwd) \
nvcr.io/nvidia/tritonserver:25.05-vllm-python-py3 \
bash -l

export TORCH_CUDA_ARCH_LIST="7.5;8.0;8.6;9.0;10.0;12.0+PTX"
python3 setup.py bdist_wheel
```
