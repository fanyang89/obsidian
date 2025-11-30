#libuv

## 参考链接

[libuv documentation](https://docs.libuv.org/en/v1.x/)
[libuv 避坑指南](https://blog.coderzh.com/2022/04/03/libuv/)

## 基础 app

- `uv_loop_init(loop);`
- `uv_run(loop, UV_RUN_DEFAULT);`
- `uv_loop_close(loop);`

## 如何停止和释放

- 正确的做法是调用 `uv_stop`，设置 loop 的状态为 stop，然后 `uv_run` 内的循环能退出出来
- `uv_handle` 需要使用 `uv_close` 释放，在 `uv_close_cb` 中释放资源
