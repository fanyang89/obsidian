#pin

```bash
cmake .. -G Ninja -D CMAKE_C_COMPILER_LAUNCHER=$(which sccache) -D CMAKE_CXX_COMPILER_LAUNCHER=$(which sccache) -D BUILD_GOLD_LINKER=on -D USE_SANITIZE=address
cmake .. -G Ninja -D CMAKE_C_COMPILER_LAUNCHER=$(which ccache) -D CMAKE_CXX_COMPILER_LAUNCHER=$(which ccache) -D BUILD_GOLD_LINKER=on -D USE_SANITIZE=address
```

```bash
. /opt/rh/devtoolset-7/enable

cmake .. -G Ninja \
-D CMAKE_C_COMPILER_LAUNCHER=ccache \
-D CMAKE_CXX_COMPILER_LAUNCHER=ccache \
-D BUILD_GOLD_LINKER=on \
-D MOCK_GIT_DESCRIBE=v6.0.0-rc42-21-gf3d5d25bfb \
-D MOCK_GIT_LOG_PATH=../git.log \
-D SPDK_VERSION=687e88b0e \
-D DPDK_VERSION=1c7eb5ef1a

cmake .. -G Ninja \
-D CMAKE_C_COMPILER_LAUNCHER=ccache \
-D CMAKE_CXX_COMPILER_LAUNCHER=ccache \
-D BUILD_GOLD_LINKER=on \
-D USE_SANITIZE=address \
-D MOCK_GIT_DESCRIBE=v6.0.0-rc42-21-gf3d5d25bfb \
-D MOCK_GIT_LOG_PATH=/tmp/zbs/mock-git-log.txt \
-D SPDK_VERSION=687e88b0e \
-D DPDK_VERSION=1c7eb5ef1a

cmake .. -G Ninja \
-D CMAKE_C_COMPILER_LAUNCHER=ccache \
-D CMAKE_CXX_COMPILER_LAUNCHER=ccache \
-D BUILD_GOLD_LINKER=on \
-D USE_SANITIZE="" \
-D MOCK_GIT_DESCRIBE=v6.0.0-rc42-21-gf3d5d25bfb \
-D MOCK_GIT_LOG_PATH=/tmp/mock-git-log.txt \
-D SPDK_VERSION=687e88b0e \
-D DPDK_VERSION=1c7eb5ef1a
```

```bash
-G Ninja
-D USE_SANITIZE=address
-D BUILD_ENABLE_SANITIZE_OPTIMIZATION=off
-D BUILD_GOLD_LINKER=on
-D CMAKE_C_COMPILER_LAUNCHER=ccache
-D CMAKE_CXX_COMPILER_LAUNCHER=ccache
-D MOCK_GIT_DESCRIBE=v6.0.0-rc42-21-gf3d5d25bfb
-D MOCK_GIT_LOG_PATH=/home/fanmi/mock-git-log.txt
-D SPDK_VERSION=687e88b0e
-D DPDK_VERSION=1c7eb5ef1a
```

# Rebuild

```bash
rm -rf ../spdk/build
rm -rf ../dpdk/build
ninja
```

```bash
# build debug + asan
cmake .. -G Ninja -D CMAKE_BUILD_TYPE=Debug \
-D USE_SANITIZE=address \
-D BUILD_ENABLE_SANITIZE_OPTIMIZATION=off \
-D BUILD_GOLD_LINKER=on \
-D CMAKE_C_COMPILER_LAUNCHER=/home/fanmi/.cargo/bin/sccache \
-D CMAKE_CXX_COMPILER_LAUNCHER=/home/fanmi/.cargo/bin/sccache \
-D MOCK_GIT_DESCRIBE=v6.0.0-rc42-21-gf3d5d25bfb \
-D MOCK_GIT_LOG_PATH=/home/fanmi/mock-git-log.txt \
-D SPDK_VERSION=687e88b0e \
-D DPDK_VERSION=1c7eb5ef1a
```

```bash
# build release
cmake .. -G Ninja -D CMAKE_BUILD_TYPE=Release \
-D CMAKE_C_COMPILER_LAUNCHER=/home/fanmi/.cargo/bin/sccache \
-D CMAKE_CXX_COMPILER_LAUNCHER=/home/fanmi/.cargo/bin/sccache \
-D BUILD_GOLD_LINKER=on \
-D MOCK_GIT_DESCRIBE=v6.0.0-rc42-21-gf3d5d25bfb \
-D MOCK_GIT_LOG_PATH=/home/fanmi/mock-git-log.txt \
-D SPDK_VERSION=687e88b0e \
-D DPDK_VERSION=1c7eb5ef1a
```

```
(gdb) p this->watchdog_->dogs_
```
