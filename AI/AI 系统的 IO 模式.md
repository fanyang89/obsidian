---
title: AI 存储综述
---

# AI 系统的 I/O 模式

_本文涉及的所有代码可以在 [zbs/aistor](https://newgh.smartx.com/zbs/aistor) 获取。_
如果您是第一次接触深度学习/人工智能/神经网络等相关概念，请从*附录 - 快速理解深度学习*开始阅读。
目前常用的深度学习框架有 PyTorch、TensorFlow、caffe2 等。目前（2025 年）PyTorch 比 TensorFlow 流行得多，所以**测试以 PyTorch 为主**。

## 准备数据集

数据集是一组样本（sample），每个样本由一组特征（feature）组成。数据集从原始数据摄取，通常已被清洗过。数据集中通常会有大量的样本存在，直接存储在文件系统上会产生大量的小文件，这对数据集的分发和移动，以及训练时的读取效率产生了影响。在一些公司，小文件的规模可以达到**百亿**级别。

常用的数据集结构：

- CSV + 若干个压缩包，压缩包内有大量小文件（下面简称为 raw，如 ImageNet 就是这种形式）
- 使用 Arrow 打包（通常不使用 parquet，因为 arrow 无须进行数据转换，效率高）
- 使用 TFRecord 打包（TensorFlow 专用）
- 使用 FFRecord 打包（幻方 3FS，使用 PyTorch）

下表是常见数据集的速览：

| 名称                                                                        | 简介                                               | 格式    | 容量   | 样本数 | 样本平均尺寸 | 样本最小尺寸 | 样本最大尺寸 |
| --------------------------------------------------------------------------- | -------------------------------------------------- | ------- | ------ | ------ | ------------ | ------------ | ------------ |
| [Salesfsorce/wikitext](https://huggingface.co/datasets/Salesforce/wikitext) | 维基百科上经过验证的优秀和精选文章                 | parquet | 644MB  | 14     | 46MB         | 618KB        | 157MB        |
| [Skylion007/openwebtext](https://huggingface.co/datasets/openwebtext)       | OpenAI 的 WebText 数据集的开源副本，用于训练 GPT-2 | txt     | 40GB   | 801w   | 5KB          | 316B         | 237KB        |
| [ImageNet21K](https://www.image-net.org/)                                   | 最大的图像识别数据集之一                           | jpg     | 1.1TB  | 1419w  | -            | -            | -            |
| [ImageNet-1k](https://huggingface.co/datasets/ILSVRC/imagenet-1k)           | ImageNet 的其中一个子集                            | jpg     | 136.8G | 143w   | 114.7KB      | 508B         | 15.7MB       |

我们本次选取两个常见的数据集进行测试，尝试找出他们在存储上运行的模式。这两个数据集分别代表 LLM 领域和 CV 领域：

- [OpenWebText](https://huggingface.co/datasets/Skylion007/openwebtext)：约 40GB，拥有 800w 样本数，样本均为字符串，可用于复现 [GPT-2](https://huggingface.co/openai-community/gpt2)
- [ImageNet-1K](https://huggingface.co/datasets/mlx-vision/imagenet-1k)：约 194.9 GB，拥有 143w 样本数，样本均为图像，可用于复现 [ResNet-50](https://huggingface.co/microsoft/resnet-50)
  _通常数据集的原始格式是 .tar.gz 等压缩包格式，使用 [huggingface/datasets](https://huggingface.co/docs/datasets/index) 加载后，会从原始数据中解压缩并构造 arrow 格式的数据集缓存。_
  本次检查的两个数据集，样本尺寸分布如下：

![[pie-simple.png|500]]
[[OpenWebText 数据集样本尺寸分布图]]
可以看到，在 OpenWebText 中，不大于 8KB 的样本占据了大多数，没有大于 256K 的样本。

![[line-simple.png|500]]
[[ImageNET-1K 数据集样本尺寸分布图]]
可以看到，在 ImageNET-1K 中，不大于 128KB 的样本占据了大多数，大于 256K 的样本非常少。

### 打包数据集

为了训练速度，通常不会直接使用大量小文件直接进行训练（直接使用大量小文件进行训练时 I/O 效率较低。几年前还是用一堆小文件的数据集，时过境迁）。因此需要进行数据集的打包：将大量的样本打包到一个或若干个文件中，读取时就无须经历缓慢的 `open-read-close` 过程。
与训练过程不同的是：不会进行 shuffle 操作。格式转换过程涉及到小文件读和顺序写两个方面，下面是测试数据：

| 数据集      | 原格式 | 目标格式 | 耗时 | 吞吐     | 只读取不写入 | I/O 特征            | 备注 |
| ----------- | ------ | -------- | ---- | -------- | ------------ | ------------------- | ---- |
| OpenWebText | Arrow  | TFRecord | 60s  | 667MB/s  | 52s          | r=128K顺序 w=顺序写 |      |
| OpenWebText | Arrow  | FFRecord | 67s  | 597MB/s  | 49s          | r=128K顺序 w=顺序写 |      |
| OpenWebText | Raw    | TFRecord |      |          |              |                     |      |
| OpenWebText | Raw    | FFRecord |      |          |              |                     |      |
| ImageNet-1K | Arrow  | TFRecord | 145s | 1.01GB/s | 70s          | r=128K顺序 w=顺序写 |      |
| ImageNet-1K | Arrow  | FFRecord | 254s | 578MB/s  | 80s          | r=128K顺序 w=顺序写 |      |
| ImageNet-1K | Raw    | TFRecord |      |          |              |                     |      |
| ImageNet-1K | Raw    | FFRecord |      |          |              |                     |      |

## 训练模型

### 模型训练简介

数据集通常会被分为训练集，测试集和验证集三部分。训练过程通常需要从训练集中分批次读取数据，进行大量读取。
模型训练的工作方式是：

- 运行 n 次 epoch，每个 epoch 会把数据集里的所有样本都送入模型一次
- 每轮 epoch，会进行 iteration 次迭代，每次迭代送入 batch_size 个样本并更新模型权重
- 迭代 m 次 epoch 后，使用验证集输出一次 loss，评估训练效果并使用优化器调整超参数；效果不好的情况下可以直接停止训练
- 所有的 epoch 运行完毕，使用测试集检查模型效果
  训练时读取测试集，会进行 shuffle 操作 （使得模型的泛化能力更好，模型不受训练集数据顺序的影响）以打乱数据集中样本的顺序，然后以按批次重复读取数据集 $N$ 次：每次读取一批，读取完一遍数据集后从头开始读取。**每次进行随机读时，读取到的数据只使用一次，并且在一段时间后不再使用，因此存储级别实现的读缓存和预读在此场景几乎没有效果（甚至是反效果），除非数据集能全部装入缓存中。主流的深度学习框架在数据流水线上均实现了自己的预读机制。**
  而验证集和测试集则不会进行 shuffle，这是为了每次评估的结果都是确定性的。
  训练过程中，通常会定时保存一个当时的模型权重文件（顺序写）。权重文件的大小根据模型的规模有关，通常每次需要写入数 GB 到数百 GB 不等（与模型参数量有关）。写入速度过慢也会影响训练的正常进行，因为保存权重的时候不能进行训练，GPU 处于闲置状态。
  **分布式训练**。进行分布式训练时，例如 PyTorch DDP，需要在所有节点上挂载数据集的文件系统。当然，通常也会有多个节点同时写入这个文件系统。

### 数据流水线

参考资料：[TensorFlow - 使用 tf.data API 提升性能](https://www.tensorflow.org/guide/data_performance?hl=zh-cn)。
这篇文章基本上把训练中的 I/O 问题讲清楚了，笔者不再赘述，直接引用原文。

先从不使用任何技巧的朴素流水线开始，按原样迭代数据集：

```python
def benchmark(dataset, num_epochs=2):
    start_time = time.perf_counter()
    for epoch_num in range(num_epochs):
        for sample in dataset:
            # Performing a training step
            time.sleep(0.01)
    tf.print("Execution time:", time.perf_counter() - start_time)
```

此时的执行时间：
![[Pasted image 20250610140807.png]]
可以看到，执行一个训练步骤涉及以下操作：

- 打开文件（如果尚未打开）
- 从文件获取数据条目
- 使用数据进行训练
  但是，在类似这里的朴素同步实现中，当流水线在获取数据时，模型会处于空闲状态。相反，当模型在进行训练时，输入流水线会处于空闲状态。因此，训练步骤的用时是打开、读取和训练时间的总和。
  **预提取**。预提取会与训练步骤的预处理和模型执行重叠。在模型执行第 $s$ 步训练的同时，输入流水线会读取第 $s+1$ 步的数据。这样做能够最大程度减少训练的单步用时（而非总和），并减少提取数据所需的时间。`tf.data` API 提供了 `tf.data.Dataset.prefetch` 转换。它可以用来将生成数据的时间和使用数据的时间分离。特别是，该转换会使用后台线程和内部缓冲区在请求元素之前从输入数据集中预提取元素。要预提取的元素数量应等于（或可能大于）单个训练步骤使用的批次数。
  ![[Pasted image 20250610141129.png]]

### I/O 模型评估

下面是测试。我们选用 [nanoGPT](https://github.com/karpathy/nanoGPT) 和 OpenWebText 进行 GPT-2 的训练。
TBD。这个要练好几天，最后再搞。

## 模型推理

应用初次启动时，需要读取一个体积较大的模型 checkpoint 文件到显存（内存）中，此后所有过程均在显存（内存）中完成。模型文件的尺寸只跟参数量有关，相同参数量的不同模型，权重文件尺寸差异不大。
在一些系统中，模型文件会经常更新（如推荐系统，信息流系统），因此还需要考虑到类似启动风暴的问题：所有的推理节点同时启动（或是被通知要求更新权重），同时向存储获取权重数据。
下表是使用 ollama 加载模型检查点文件时的性能数据和 I/O 模型：

| 模型             | 参数量 | 大小  | 实现   | 加载耗时 | 吞吐      | I/O 模型     | 备注                     |
| ---------------- | ------ | ----- | ------ | -------- | --------- | ------------ | ------------------------ |
| deepseek-r1:7b   | 7B     | 4.7GB | ollama | 1.01s    | 3.80 GB/s | 128KB 顺序读 | 拍视频截帧计时           |
| deepseek-r1:32b  | 32B    | 20GB  | ollama | 3.10s    | 6.11 GB/s | 128KB 顺序读 | 拍视频截帧计时           |
| QwQ:32b          | 32B    | 20GB  | ollama | 3.08s    | 6.15 GB/s | 128KB 顺序读 | 拍视频截帧计时           |
| deepseek-r1:671b | 671B   | 404GB | ollama | -        | -         | -            | 测试环境显存不足，未测试 |

## RAG

RAG（检索增强生成，Retrieval-Augmented Generation）通过结合检索机制和 LLM，来提高生成式 AI 的准确性和相关性。最常见的 RAG 就是知识库应用：企业将

## 业界已有的系统

### 3FS

TBD

### Alluxio

TBD

### JuiceFS

TBD

## 附录

### 测试环境简介

测试机配置为：

- CPU：AMD Ryzen 9700X 8c16t 5.58 GHz
- 内存：24GB \* 2
- 磁盘：[Samsung SSD 9100 2TB](https://www.samsung.com/tw/memory-storage/nvme-ssd/9100-pro-2tb-nvme-pcie-gen-5-mz-vap2t0bw)
  - 4k 随机读 700K IOPS，随机写 491K IOPS
  - 256K 顺序读 14GB/s，顺序写 13GB/s
- 显卡：NVIDIA GeForce RTX 3070 8GiB

### 快速理解深度学习

_本段主打快速理解，有问题，请以其他材料为准。如果你已经很懂了，请敬请跳过。_

- 深度学习：机器学习的分支，一种以人工神经网络为架构，对资料进行表征学习的算法
  - 现在火热的大语言模型（LLM）是基于深度学习的大型神经网络模型，用于处理和生成自然语言
- 矩阵、张量。
  - 矩阵（Matrix）就是 $M \times N$ 个数，是一个二维的数字网格。
  - 张量（Tensor）就是 $T$ 个矩阵。
  - 举例：
    - 一张黑白的图片，白色像素点为 1，黑色为 0，可以用一个仅包含 0 和 1 的矩阵表示
    - 一张彩色的图片，每个像素点使用 RGB 三个通道表示，也就是一个像素点有三个数字，可以用一个三维张量表示
    - 一个彩色视频可以用一个四维张量表示
- 模型
  - 模型就是一个大型的函数 $y=f(x)$，这个函数里有很多很多参数
    - 例如一个进行图像分类的模型，他的输入 $x$ 通常是一张图片（用张量表示），输出 $y$ 是一个表示这张图片的类别的矩阵
  - 举例：尝试使用模型来预测一年内每个月的冰淇淋销量
    - 假设冰淇淋在每年夏天卖得最好，其他时间销量很低；温度越高，销量越高，温度越低，销量越低
    - 建立网络：定义 $y=f(x)=ax^2+bx+c$，$x$ 是月份数
    - 定义损失函数（loss function）：使用损失函数来评价一个模型的优劣，loss（损失）越小，跟实际越接近，模型越好
    - 为了进行预测，我们需要确定参数 $a,b,c$ 的值
      - 不妨定义损失函数为预测值与实际值差的绝对值
      - 训练：此时我们搬来历年的冰淇淋销量 $y$ 与月份 $x$ 的关系数据，挨个输入到模型里，不断调整 $a,b,c$ 三者的值，使得 loss 最小
    - 推理：现在 $a,b,c$ 已经确定，网络 $y=f(x)$ 也建好了，我们就可以开始进行实际的冰淇淋销量预测了（根据月份 $x$ 计算出 $y$ 的值）
- 参数和量化。一般来说参数在训练的时候是 float32/float16 类型。量化就是用 int 来表示这个 float32，int8 量化就是用一个 byte 表示这个参数。
  - 在实际的模型中，参数的数量非常多（例如 ResNet-50 有 25.6M（M 是百万）个参数，而 DeekSeek R1 满血版有 471B （B 是十亿）个参数）
  - 模型计算的时候，参数都是加载到显存里的，所以用上量化之后，就省了很多显存；算一堆 int 也是比浮点要快很多的。当然，推理精度降低了。
  - 你可能会问，我就是没钱买大显存的显卡，组不起集群，是不是可以：
    - 参数全放内存里（毕竟内存便宜多了），用 CPU 计算。当然可以！
      - [AMD Ryzen Al Max+ 395](https://www.amd.com/en/products/processors/laptop/ryzen/ai-300-series/amd-ryzen-ai-max-plus-395.html)
      - [Mac Studio](https://www.apple.com/sg/mac-studio)
    - 每次只加载一部分网络和参数到显存里，然后计算，算好了之后把结果存下来，再加载其他部分的参数继续算。当然也可以！
      - [Jittor/JittorLLMs](https://github.com/Jittor/JittorLLMs)：用户不需要修改任何代码，原生的动态图代码即可直接支持张量交换，_张量数据可以在显存-内存-硬盘之间自动交换_

### 快速理解 Transformer

### 推荐阅读

- [3Blue1Brown - 深度学习](https://space.bilibili.com/88461692/lists/1528929?type=series)
- [深度学习入门2 - 自制框架](https://www.ituring.com.cn/book/2863)
- [Dive into Deep Learning（d2l）](https://d2l.ai)
- [深度学习（花书）](https://book.douban.com/subject/27087503)
