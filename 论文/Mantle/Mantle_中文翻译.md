# Mantle：云对象存储服务的高效分层元数据管理

贾浩李†§_ 曹彪§_ 简捷龙§ 李成†¶ 韩森† 王一铎† 吴宇飞†  
陈康‡ 尹志辉§ 陈秋实§ 熊继伟§ 赵杰§ 刘丰源§ 邢燕§  
段立国§ 余淼§ 郑然§ 吴峰†¶ 孟显军§  
†中国科学技术大学 §百度（中国）有限公司 ‡清华大学  
¶合肥综合性国家科学中心人工智能研究院

# 摘要

云对象存储服务（COSS）是云中的主要存储后端，支持大规模分析和机器学习工作负载，这些工作负载频繁访问深度对象路径并并发更新元数据。然而，当前的COSS架构会产生代价高昂的多轮查找和高目录争用，延迟作业执行。先前的优化主要针对分布式文件系统（在云中采用率最低），由于COSS特定的约束（如无状态代理和有限的API）而不适用。

Mantle是为现代云工作负载设计的新型COSS元数据服务。它采用两层架构：一个可扩展的、分片的数据库（TafDB）在命名空间之间共享，以及每个命名空间单服务器的IndexNode，整合轻量级目录元数据。通过元数据和责任的精细划分，Mantle支持单个命名空间中多达100亿个对象或目录，并通过在IndexNode上可扩展执行单RPC查找实现每秒180万次查找。它还通过在TafDB中集成异地增量更新并将跨目录重命名的循环检测卸载到IndexNode，在高争用情况下实现每秒高达58K的目录更新，两者都有效消除了协调瓶颈。与Tectonic、InfiniFS和LocoFS的元数据服务相比，Mantle将元数据延迟降低了6.6-99.1%，吞吐量提高了0.07-115.00倍。启用数据访问后，它将交互式Spark分析作业完成时间缩短了63.3-93.3%，将AI驱动的音频预处理任务缩短了38.5-47.7%。Mantle已在百度对象存储（BOS）上部署超过2年，这是百度沧海存储提供的一项服务。

CCS概念：·信息系统→云存储；目录结构。

# 1 引言

云对象存储服务（COSS）如Amazon S3 [5]、Azure Blob Storage [28]和Google Cloud Storage [14]已成为现代云环境中的主导存储基础设施[41]。它们作为广泛关键工作负载的基础层，特别是在数据密集型领域，如数据分析和机器学习（ML）训练和推理。这些应用程序严重依赖COSS来存储海量数据集并频繁并发访问它们。因此，它们要求解析对象路径和修改对象元数据都具有超低延迟和高吞吐量，以保持作业响应性和成本效率，特别是考虑到涉及GPU等昂贵计算资源。

然而，在COSS中满足这些性能要求并非易事。现代工作负载通常在深度嵌套的目录层次结构上操作，具有长访问路径，并涉及并发下的实质性目录变更。我们对真实世界命名空间的分析（§3）显示，这些命名空间达到十亿规模，平均目录深度约为11，最大深度为95。总体而言，许多并发应用程序同时访问这些命名空间，将单个命名空间的峰值吞吐量提高到每秒数十万次操作或更多。在所有元数据请求中，路径解析（也称为查找）主导执行时间，在某些情况下占元数据操作延迟的89%以上。此外，共享目录中的争用显著降低了mkdir和dirname等目录修改操作的性能，限制了交互式分析查询等应用程序的性能。

大量研究集中在分布式文件系统（DFS）中的元数据优化上，但很少关注COSS，尽管它们在云环境中的采用率要高得多。COSS架构施加了基本约束，如无状态代理层、有限的API和缺乏协作客户端逻辑，使得传统的DFS技术无效。诸如元数据缓存[26, 42, 50]和推测性并行路径解析[26]等技术依赖于COSS本质上缺乏的紧密耦合设计。类似地，通过单分片更新[8]放松隔离可以减少目录更新争用，但未能完全解决COSS工作负载中的复杂协调模式，并且路径解析性能基本未优化。

为了解决这些限制，我们提出了Mantle，一种新型COSS元数据服务，旨在提供超低延迟、高吞吐量和抗争用性能。Mantle采用包容性的两层架构，包括可扩展的分片TafDB和针对快速目录查找优化的轻量级每命名空间IndexNode。与先前的分层设计[21]不同，Mantle引入了元数据和服务责任的精细划分：TafDB大规模存储所有元数据并在命名空间之间共享，而IndexNode仅缓存单个命名空间的基本目录元数据，实现高效的单RPC路径查找，而不是跨节点多轮路径遍历。

除了架构创新之外，Mantle还引入了两个关键优化来实现其性能目标。首先，它重新设计了IndexNode内的数据组织和工作流，以防止其成为单节点瓶颈。为了减少CPU开销，Mantle采用TopDirPathCache，这是一个存储高稳定性路径前缀解析结果的静态缓存。TopDirPathCache避免运行时提升或降级；相反，过期条目由Invalidator（一种轻量级无锁机制）移除。这种设计有效平衡了快速查找性能和高效的缓存失效，由目录更新触发。此外，Mantle通过将查找任务卸载到跟随者和学习者副本，利用IndexNode的基于Raft的复制组，分配工作负载并增强超出领导节点的查找可扩展性。

其次，为了解决并发目录更新中的争用，Mantle向TafDB引入了一种新颖的增量记录机制，用无冲突追加替代原地更新，显著减少高并发下的事务中止和重试。对于跨目录重命名等复杂操作，Mantle将循环检测逻辑移至IndexNode，并使用其本地元数据索引在单个RPC中执行锁定和验证，避免昂贵的分布式协调。最后，Mantle应用Raft日志批处理和跟随者写入卸载来维持IndexNode的写入吞吐量，随着目录更新率的增加。

Mantle已在百度对象存储中部署超过1.5年。我们在§7中报告了丰富的部署经验，其中管理了19个内部命名空间和数十亿对象。在我们的评估中，它支持单个命名空间中100亿条目，实现每秒189万次查找。在高争用下，它实现每秒高达58.8K次mkdir和38.0K次dirname操作。我们将Mantle与三种现代分层元数据服务进行比较，

![](https://cdn-mineru.openxlab.org.cn/result/2025-09-28/a1cd3fdd-045f-44e0-9bdb-7cd0c339f424/20fadb5b61dca72a066bbeca76eb7b9b1235dc2b49fef21dbb0c0d2af570d97b.jpg)  
图1. 具有受限应用接口的典型COSS架构。请注意，单个COSS通常托管许多共享内部元数据和数据服务的应用程序。

包括Tectonic [33]、LocoFS [21]和InfiniFS [26]，使用两个真实世界工作负载（交互式Spark分析和基于AI的音频预处理）和mdtest基准测试。Mantle分别将元数据延迟降低了17.5-95.2%、9.5-99.1%和6.6-98.8%，同时引入了1.20-20.90倍、1.16-116.00倍和1.07-80.78倍的吞吐量加速。这些改进转化为Spark分析作业完成时间缩短63.3-93.3%，音频预处理任务缩短38.5-47.7%。Mantle及其分析数据将在https://mantle-opensource.github.io/上提供。

总之，本文的贡献是：

- 对真实世界COSS命名空间的研究，识别了由低效查找和高更新争用引起的关键元数据瓶颈。
- Mantle的两层设计，将单节点目录索引与可扩展数据库相结合，用于批量元数据存储和处理，结合了元数据划分和协调消除等优化，以避免单节点瓶颈。
- Mantle的实现，整合了这些范式，支持十亿规模的单个命名空间。
- 使用真实世界应用程序基准测试和元数据基准测试对Mantle进行深入评估，并与最先进的系统进行比较。
- Mantle的生产部署，具有详细的部署经验教训，为社区的未来研究提供了见解。

# 2 背景

# 2.1 云对象存储服务（COSS）

预计云计算基础设施投资将占2023年全球IT基础设施支出的60%以上[17]。云对象存储服务（COSS）已成为构建基于云应用程序的主要存储解决方案。最近的一项研究[41]检查了396个基于Amazon Web Services（AWS）构建的应用程序和系统，发现其中约68%使用Amazon S3对象存储，而分布式文件系统并不特别重要且采用率最低，使用率仅为4%。此外，Enterprise Storage Forum报告称，全球云对象存储市场在2020年价值48.3亿美元，预计到2028年将达到136.5亿美元，复合年增长率为13.6%[40]。这种主导性采用和不断扩大的市场凸显了COSS作为关键且及时的研究重点。

同一研究还强调，两个关键工作负载，分析和机器学习（ML）任务，主导着云应用程序格局。具体来说，超过60%的应用程序使用分析或ML服务，其中分析占42%，ML占18%。鉴于AI日益突出，ML工作负载预计将继续增加。COSS在支持这两种工作负载方面发挥着至关重要的作用：分析引擎通常将预处理和后处理数据存储在S3等COSS中，而许多ML服务（如语言和视频处理服务）依赖COSS访问输入数据并存储输出结果。这些发现与我们在百度内部云环境中的观察一致（详见§3）。

# 2.2 COSS的接口和编程约束

图1显示，典型的COSS由三个关键组件组成：代理层、元数据服务和数据服务。数据服务维护服务器池来存储对象数据，而元数据服务跨越几台服务器来管理对象命名空间，将对象路径映射到其物理数据位置。

通常，多个应用程序共享单个COSS，它们的命名空间和对象分别存储在内部的元数据服务和数据服务中。COSS对其与应用程序的交互施加了约束。应用程序通过受限的RESTful API集[5, 28]与COSS交互，通常通过云SDK访问。例如，要检索对象，应用程序向随机选择的代理服务器发出带有指定路径的HTTP GET请求。代理通过元数据服务解析路径，从数据服务获取相应对象，并将其返回给应用程序。值得注意的是，这个代理层是无状态的，这有助于负载均衡。

COSS的松散耦合架构严格将其内部组件与外部用户隔离，为扩展其元数据和数据服务带来了新的挑战。

# 2.3 COSS中的元数据管理

元数据性能对于使用COSS的应用程序至关重要，因为根据图1中的元数据-数据分离架构，必须在对象操作之前访问元数据服务。此外，应用程序对象大小通常很小[3, 44]，与对象访问相比，元数据开销已变得不可忽视。稍后，我们将在§6.2中展示元数据增强带来了显著的应用级性能提升。

最近，许多COSS，包括Amazon S3、Azure Data Lake Storage和Google Cloud Storage，已逐渐通过从管理平面命名空间过渡到分层命名空间来增强其元数据服务[5, 14, 28]。此外，它们开始使用数据库将分层命名空间处理为数据库表，这些表被分片并分布在多个服务器上以实现扩展，并将元数据操作编排为数据库事务[7, 22, 30, 33]。上述研究指出，16个应用程序在DynamoDB [9]中存储指向S3上数据的指针。

图2显示了百度的基于DBtable的分层元数据服务架构，类似于Meta的Tectonic [33]存储中使用的架构。这种基于DBtable的元数据服务将每个分层命名空间映射到一个大型数据库表，并使用父目录的唯一ID（pid）和对象/目录名称作为主键。为了在保持目录局部性的同时实现负载均衡，它进一步按pid对元数据表进行分区，尽可能将同一目录下的条目放置在同一分片中。

多RPC路径解析。使用这种架构，解析到对象或目录的路径涉及从根开始的逐级遍历，需要通过多轮RPC在代理和元数据服务（即底层数据库服务器）之间进行交互。这是由于元数据表的分区性质，其中每个目录存储在特定的数据库服务器上。在每个级别，代理必须检索当前目录的inode ID以计算联系哪个服务器进行下一级查找。此外，在每个步骤执行权限检查以确保访问控制。图2的右上部分显示，当应用程序尝试创建新目录'/A/C/E/G'时，它首先向代理层发出HTTP PUT请求。然后随机选择的代理服务器通过从根递归遍历路径来处理此请求，验证每个组件的存在并沿途检查权限(①,②,and③)。

分布式元数据事务处理。目录修改，如mkdir和dirname，需要更新多个目录的元数据条目。当这些更新跨越多个元数据分片时，需要分布式事务以确保涉及的数据库服务器之间的强一致性[26, 49]。如图2所示，步骤④a和④b共同构成由代理发起的分布式事务，以完成上述mkdir请求的后半部分。此事务在node3和node4之间协调，以插入新创建目录'G'的元数据条目并相应更新其父目录'E'的元数据。

# 3 COSS的命名空间行为

为了了解COSS上应用程序的元数据瓶颈，我们对百度云环境中与数据密集型应用程序对应的命名空间的数量、访问特征和性能要求进行了详细分析，其中基于DBtable的元数据服务已部署多年。我们分析了五个代表性命名空间，主要托管大数据分析或ML任务等工作负载，结果总结在图3和图4中。

图3a显示，所有五个命名空间都有超过20亿条目，其中对象占82.0%到91.7%，目录仅占8.3%到18.0%。此外，它们的工作负载特征是对大量小对象的频繁操作，必须维持每秒150K到300K操作的高吞吐量。对小对象的数据访问通过SSD加速，将延迟减少到单个RPC加上设备访问的几十微秒[1, 4]。因此，元数据访问成为端到端性能的关键因素。

例如，在自动驾驶模型数据预处理中，多个任务处理超过10亿个小对象（大多低于512KB）的大型数据集，每个对象封装车辆运行期间收集的多模态传感器数据的短时间段和相关元数据。预处理工作流涉及扫描现有对象和创建新对象，产生繁重的查找和创建负载。在这种情况下，元数据访问主导总执行时间（超过70%）。

然而，由于以下两个低效问题，实现高元数据性能具有挑战性。

# 3.1 查找低效问题

令人惊讶的是，这些命名空间拥有深度嵌套的目录层次结构，平均路径深度超过10，最大深度达到95。来自

![](https://cdn-mineru.openxlab.org.cn/result/2025-09-28/a1cd3fdd-045f-44e0-9bdb-7cd0c339f424/33fc0ebe3c2b69a8589d007cfc5ad74eaf74e222c402c93babea8d6c627a82f0.jpg)  
(a) 对象/目录计数

![](https://cdn-mineru.openxlab.org.cn/result/2025-09-28/a1cd3fdd-045f-44e0-9bdb-7cd0c339f424/cd801df0ab91dc1be022bb9d1cf6ff33312d9ed12c47e242a0e435b489d5b217.jpg)  
(b) 请求路径深度

![](https://cdn-mineru.openxlab.org.cn/result/2025-09-28/a1cd3fdd-045f-44e0-9bdb-7cd0c339f424/48c4ba8b990b7d74150ac7c2ad1abf68ec7f708baf26de9c018dd5c906a4ad16.jpg)  
图3. 五个真实世界命名空间的特征  
(a) 延迟分解

![](https://cdn-mineru.openxlab.org.cn/result/2025-09-28/a1cd3fdd-045f-44e0-9bdb-7cd0c339f424/5093007b7cff4ea78bb8d66bdcd1cecb56d72c524417cc1c82b8a0abeb19d76b.jpg)  
图4. 基于DBtable的COSS元数据服务的性能分析  
(b) 吞吐量

应用程序的访问模式严重偏向层次结构的较深层次。图3b显示，五个命名空间的平均访问深度分别为11.6、11.5、10.8、10.6和11.9。对于最大的命名空间ns4，一半的请求访问深度超过10的路径。

深度目录层次结构和长访问路径使现代元数据管理方法面临路径解析瓶颈。为了说明这一点，我们通过使用mdtest基准测试[39]、基于DBtable的元数据服务和512个应用程序线程（§6.3中的详细配置）进行实验，分析了三种元数据操作（如objstat、dirstat和delete\*）的延迟分解结果。图4a强调，大部分执行时间被查找步骤消耗，即三种执行操作分别为89.9%、91.2%和63.1%。这种长查找时间是由代理层和元数据服务之间的几轮交互造成的，如§2.3所述。请注意，此结论对所有其他元数据操作都有效（由于空间限制而省略），因为查找是所有元数据操作的第一步。

# 3.2 目录更新争用

目录修改操作，如mkdir、rmdir和dirname，占对命名空间总体访问的相对较小部分。然而，对于某些应用程序，由于对同一目录属性的并发更新，它们可能导致共享目录上的实质性争用。

在我们的生产环境中，Spark即席查询每天发出超过10,000次运行，具有严格的延迟要求。每个查询生成数百个子任务，其临时目录被原子性地重命名到共享输出目录中，导致集中的元数据更新。在现有的基于DBtable的机制下，争用导致

表1. 元数据服务比较：查找延迟优化与目录修改争用处理。pathlen表示要解析的路径长度。

<table><tr><td></td><td>类型</td><td>查找的RTT次数</td><td>减少目录争用</td></tr><tr><td>元数据缓存[26, 42, 50]</td><td rowspan="4">DFS</td><td>pathlen</td><td>不适用</td></tr><tr><td>并行解析[26]</td><td>[1, pathlen]</td><td>不适用</td></tr><tr><td>隔离放松[49]</td><td>pathlen</td><td>受限</td></tr><tr><td>分层[21]</td><td>单次</td><td>不适用</td></tr><tr><td>Mantle（我们的）</td><td>COSS</td><td>单次</td><td>全部</td></tr></table>

频繁的事务重试，使这个提交阶段成为主要瓶颈。在极端情况下，它占总任务执行时间的99%以上。

为了展示共享目录争用的影响，我们还从512个线程运行mdtest mkdir和dirname工作负载，有两种情况，其中"无冲突"表示所有线程写入不同的目录，而"全冲突"模拟上述Spark查询场景，即写入单个目录。图4b显示，与无争用场景相比，基于DBtable的元数据服务在完全争用下表现显著更差，即两种元数据操作的吞吐量分别减少99.7%和99.4%。这种退化发生是因为mkdir和dirname操作触发跨数据库分片的分布式两阶段提交事务，导致高争用下的高中止率或长锁等待时间。

# 3.3 动机

在本文中，我们旨在为现代COSS构建新的元数据服务，提供超低查找延迟，减少目录更新争用，并保持高整体元数据吞吐量。然而，如表1总结的那样，大多数现有的元数据优化工作针对分布式文件系统（DFS），由于基本架构差异，它们不直接适用于COSS。

首先，几个基于DFS的工作侧重于减少路径查找中的往返时间（RTT）次数。元数据缓存——在Ceph [50]、NFSv4 [42]和InfiniFS [26]等系统中使用——缓存目录遍历结果以加速重复查找。虽然在紧密耦合的DFS架构中有效，但由于COSS的无状态代理设计、有限的API暴露和缺乏跨层可见性，这种缓存在COSS中难以实现。另一种技术，并行解析[26]，推测性地预测并并发查询所有路径级别的元数据分片以减少平均延迟。然而，在高并发下，这种方法变得适得其反，因为查找延迟由最慢的请求决定，并因线程过度订阅而加剧。此外，并行解析不

![](https://cdn-mineru.openxlab.org.cn/result/2025-09-28/a1cd3fdd-045f-44e0-9bdb-7cd0c339f424/7bd917ba8090cdf3955378d39604fd37db699881f8596418070cbea5dbf0f35d.jpg)  
图5. Mantle元数据服务架构以及IndexNode和TafDB之间的元数据请求处理划分。标有(⋆)的操作是目录操作，其他是对象操作。

减少RPC的数量，也不能提高元数据服务的吞吐量能力。因此，在InfiniFS中用512个线程解析10级路径导致平均延迟为7.4个RTT——接近基于DBtable的架构中朴素的顺序解析。

LocoFS [21]通过解耦目录和对象元数据提出了分层元数据服务，将前者放在专用目录节点上，后者放在可扩展数据库集群中。虽然这种分离理论上实现了独立扩展，但它也带来了新的挑战。集中式目录节点必须处理所有目录相关操作，造成性能瓶颈。此外，对于对象创建等操作，仍然需要跨组件协调，这涉及重复名称检查和父目录更新，两者都必须通过目录节点，施加额外开销。

关键的是，大多数现有工作忽略了目录更新中的争用，通常假设此类操作不频繁且对整体系统性能影响可忽略。这个假设在支持动态和高并发云工作负载的现代COSS中不再成立。最近的努力如CFS [8]尝试通过放松隔离级别并使用原子原语将分布式事务转换为单分片事务来减少协调开销。虽然在避免简单目录更新的协调方面有效，但此策略对于dirname等复杂操作会失效，这些操作跨越多个分片，仍然会产生昂贵的分布式事务。

总之，现有方法要么优化查找延迟，要么略微减少更新开销，但没有一个在COSS环境中同时解决两者。这些局限性促使设计新的元数据服务架构，全面增强COSS在多样化和大规模工作负载下的性能。

# 4 Mantle概述

图5展示了Mantle的高级架构，这是一种新型COSS元数据服务，旨在支持包含

![](https://cdn-mineru.openxlab.org.cn/result/2025-09-28/a1cd3fdd-045f-44e0-9bdb-7cd0c339f424/f431ea96651e760ededad87501d7fbafc38e20ac72b7e3de53fa038d9afbf07d.jpg)  
图6. Mantle的IndexNode上的主要数据结构

<table><tr><td colspan="2">完整路径</td><td>id</td><td colspan="2">路径权限</td></tr><tr><td colspan="2">/A/C</td><td>3</td><td colspan="2">PA∩PC</td></tr><tr><td colspan="2">/A/C/E</td><td>5</td><td colspan="2">PA∩PC∩PE</td></tr><tr><td colspan="2">/B</td><td>2</td><td colspan="2">PB</td></tr><tr><td colspan="5">TopDirPathCache</td></tr><tr><td>pid</td><td>名称</td><td>id</td><td>权限</td><td>锁</td></tr><tr><td>0</td><td>A</td><td>1</td><td>PA</td><td>false</td></tr><tr><td>1</td><td>B</td><td>2</td><td>PB</td><td>false</td></tr><tr><td>1</td><td>C</td><td>3</td><td>PC</td><td>true</td></tr><tr><td>3</td><td>E</td><td>5</td><td>PE</td><td>false</td></tr><tr><td colspan="5">IndexTable</td></tr></table>

多达100亿个目录和对象的单个命名空间。Mantle实现每秒180万次路径查找和3万次目录层次结构修改，同时显著减少在该命名空间上操作的云工作负载的延迟。此外，通过加速路径查找，Mantle还改善了涉及目录和对象的所有其他元数据操作的性能。

为了实现这些能力，Mantle由三个核心组件组成：代理层、TafDB和IndexNode。与基于DBtable的元数据服务（图1）一样，Mantle的代理层接收应用程序的HTTP请求，并通过与内部组件交互来协调它们的执行，以处理元数据和数据请求。TafDB是一个可扩展的分片数据库，负责存储任何规模的元数据。IndexNode是一个专用的单节点索引，整合给定命名空间的所有/部分目录元数据。每个命名空间分配自己的IndexNode，而共享的TafDB仍然是所有命名空间的可扩展后端。本文专注于优化单个命名空间内的元数据性能，尽管该架构通过部署多个IndexNode实例自然支持多命名空间部署。

与上述分层方法相反，Mantle在IndexNode和TafDB之间采用包容性的两层架构，具有精细的元数据划分。虽然TafDB保留完整元数据，但IndexNode仅维护关键子集的目录元数据（每个目录约80字节）用于高效查找和跨目录协调。我们将目录元数据划分为访问元数据（图5中红色顶部框）和属性元数据（蓝色底部框），IndexNode仅存储访问元数据。如图6所示，这是通过IndexNode中的IndexTable实现的，其中包括：父目录ID（pid）、目录名称（dirname）、目录ID（id）、访问权限（permission）和用于并发更新协调的锁位。

为了补充我们的数据划分，我们设计了精细的责任划分，最小化IndexNode工作负载，同时最大化TafDB用于目录操作的扩展能力。如图5所示，IndexNode将查找和权限检查处理为单RPC操作，而TafDB使用其存储的属性元数据处理对象元数据和目录操作（readdir和stat）。对于复杂变更（如mkdir和rename），协调发生在两个组件之间：TafDB更新所有元数据（访问和属性），而IndexNode刷新访问数据以供将来查找，保持强同步。此外，IndexNode管理重命名循环检测，这对于当目录跨越多个数据库分片时的TafDB来说是一个特别具有挑战性的任务。

IndexNode的引入不会改变MetaTable的可用性保证，这是由底层数据库确保的。因此，我们的重点在于确保IndexNode本身的高可用性。这是通过采用Raft共识协议[31]将更新复制到多个节点来实现的。在Raft组内，所有节点维护相同的内存数据结构，这些结构由每个节点根据§5.1中描述的过程独立构建。

虽然这些设计选择实现了可扩展的元数据管理，但它们也引入了必须解决的特定挑战，以确保系统的整体性能和一致性。我们将在下一节详细阐述这些挑战及其解决方案。

# 5 Mantle的设计

# 5.1 扩展单RPC查找

虽然IndexNode将分布式查找集中到单个节点，但CPU密集型的路径解析过程在IndexNode中造成性能瓶颈。这是因为IndexNode上的单RPC查找仍然分解为对IndexTable的几次本地访问。请注意，随着路径深度加深，这个瓶颈变得更加明显。为此，我们引入了新颖的TopDirPathCache设计，有效减少IndexNode中的CPU消耗（§5.1.1）。接下来，为了以低开销维护缓存一致性，我们提出了轻量级缓存失效机制，最小化对查找请求的干扰（§5.1.2）。最后，在IndexNode的Raft组中，跟随者节点通常空闲，仅执行复制任务，浪费CPU资源。因此，我们能否使用跟随者节点来分担领导者的路径解析压力成为查找优化的另一个关键。详细设计在§5.1.3中提供。

5.1.1 TopDirPathCache。我们在IndexNode内设计了一个名为TopDirPathCache的查找缓存，通过存储路径解析结果来减轻IndexTable的负载。缓存效率在内存消耗、性能改进和缓存维护开销之间做出基本权衡。缓存每条路径是不可行的，因为需要过多的内存。例如，10亿目录条目需要超过1TB内存使用。此外，维护缓存一致性会产生不可忽视的

开销：对祖先目录的任何修改都需要使受影响的条目失效，给IndexNode带来额外的CPU负担。因此，我们更喜欢缓存的路径解析结果高度稳定。

对生产跟踪的经验分析表明，大多数目录重命名操作发生在命名空间层次结构的叶节点附近。因此，我们决定不缓存完整路径的解析结果。相反，我们仅缓存路径的截断前缀，与叶节点保持一定距离。这样做最小化了内存占用和频繁维护操作（如提升和降级）的开销。如图6所示，TopDirPathCache实现为内存哈希表，其中每个条目将完整路径前缀映射到相应目录的pid及其聚合权限掩码。遵循Lazy-Hybrid方法[6]，沿路径的权限相交以计算统一的路径权限。

工作流程如下。具体来说，当处理深度为N的查找请求时，IndexNode截断最后k级并使用生成的前缀查询TopDirPathCache。经验常数k表示超出叶节点的距离，超过该距离则禁用缓存。因此，叶节点k级内的所有路径都从TopDirPathCache中省略。举例来说，当解析路径'/A/C/E/G/H'且k=3时，前缀'/A/C/'在TopDirPathCache中检查。如果未找到相应条目，将在通过IndexTable进行路径解析后为此前缀创建新条目。§6.5中的评估表明，k=3在性能增益和内存消耗之间取得了平衡。具体来说，此配置产生显著加速，同时仅消耗缓存所有结果所需内存的12%。

5.1.2 缓解查找-修改紧张关系。目录修改，如dirname和setAttr，可能使相关缓存条目过期。具体来说，所有以受影响路径为前缀的条目必须失效并从TopDirPathCache中移除。这需要范围扫描来识别受影响目录的所有后代。然而，作为TopDirPathCache基础的哈希表本身不支持范围扫描。此外，需要协调机制来防止传入查找访问受影响范围，确保一致性。

为了协调查找和目录修改，Mantle引入了Invalidator，一种用于缓存失效的轻量级机制。它依赖于两个辅助数据结构，即PrefixTree和RemovalList。PrefixTree是一个无锁的基数树，重建TopDirPathCache中所有缓存路径的目录树，实现高效的范围查询以进行失效。RemovalList是一个无锁的跳表，记录正在修改的目录的完整路径。这两个数据结构的填充方式如下。当新的查找结果缓存在TopDirPathCache中时，PrefixTree同步更新以反映新路径。当

![](https://cdn-mineru.openxlab.org.cn/result/2025-09-28/a1cd3fdd-045f-44e0-9bdb-7cd0c339f424/c713e95584561ed03a6bac0437966ca7e6525e1dcdf7897361b32b1150a13b21.jpg)  
图7. 处理查找操作的工作流程

发生目录修改（如dirname和setattr）时，目标目录的完整路径插入到RemovalList中。Invalidator中的后台执行线程定期轮询RemovalList，查询PrefixTree以获取受影响路径的范围，并从TopDirPathCache中移除相应条目。对于rmdir，目标目录可以安全移除而无需更新RemovalList，因为空目录不能是现有目录的前缀，消除了插入带来的潜在冲突。Invalidator的无锁设计减少了维护一致性相关的CPU开销，而其非阻塞架构最小化了查找操作期间的争用。这些优化使IndexNode即使在繁重的目录修改工作负载下也能维持路径解析的高吞吐量。

IndexNode上的新查找工作流涉及与几个数据结构的交互。如图7所示，查找请求首先扫描RemovalList以检查是否有任何正在修改的路径是请求路径的前缀。(1)。此扫描是轻量级的，产生的开销可忽略不计，因为RemovalList大多数时间为空。如果找到此类路径，搜索跳过TopDirPathCache并直接查询IndexTable以避免访问过期的缓存结果(3)；否则，请求继续在TopDirPathCache中搜索截断的前缀路径(2)。如果前缀被缓存，请求通过IndexTable中的逐级路径遍历解析剩余的后代目录(3)。完成查找后，如果满足以下两个条件，TopDirPathCache缓存截断前缀的解析结果：(a)截断前缀未被缓存，以及(b)在查找请求执行期间RemovalList未被修改。为了检测并发修改和查找之间的冲突，使用传统的时间戳机制。最后，请求路径的pid返回给Proxy(4)。

5.1.3 跟随者/学习者上的路径解析。为了克服单个节点的资源限制，当领导节点负载沉重时，我们将路径解析请求卸载到空闲的IndexNode跟随者。跟随者首先查询领导节点以获取最新的commitIndex以确保一致性并避免陈旧读取。之后，它等待其本地applyIndex超过commitIndex，然后执行本地路径解析例程。为了最小化对领导者的开销，commitIndex查询被批处理。此外，学习者（只读副本）[20]被添加到Raft组以进一步增强吞吐量。跟随者和学习者在内部维护并独立使用其本地TopDirPathCache以加速路径解析。

然而，跟随者读取也有风险读取其本地TopDirPathCache中的陈旧条目。为避免这种情况，缓存失效通过Raft日志在Raft组内同步，通过Raft日志复制失效信息。需要缓存失效的操作将受影响目录的完整路径附加到Raft日志。当应用这些日志时，使用前面描述的Invalidator机制使相应的缓存条目失效。这种方法确保跟随者和学习者与目录的更改保持一致更新，而不会产生高开销。

# 5.2 扩展目录修改

有三种关键情况需要解决以提高目录修改性能。首先，如§3.2中观察到的，对同一目录的并发修改——特别是那些更新其属性元数据的修改——可能导致重复的事务中止和重试，导致高尾延迟。为了缓解这个问题，我们引入了异地更新机制，消除属性争用（§5.2.1）。其次，我们优化跨目录dirname，这是最复杂的元数据操作。特别是，我们利用IndexNode将循环检测从分布式协调任务转换为本地操作（§5.2.2）。最后，随着目录修改变得显著更高效，我们必须确保IndexNode能够承受增加的写入负载。为此，我们引入针对性机制，防止写入吞吐量受到单个Raft组容量的瓶颈限制（§5.2.3）。

5.2.1 增量记录和压缩。当并发目录操作（如mkdir和rmdir）作用于同一目录时，它们经常竞争目录属性元数据，触发事务中止。这导致频繁重试和增加的尾延迟。在CFS [49]中，通过将每个操作分成两个单分片事务来缓解mkdir和rmdir的争用：一个插入或删除属性元数据，另一个更新访问元数据和父目录的属性元数据。然而，在我们的系统中，这种特定的两事务策略与IndexNode和TafDB之间的同步要求不兼容。

![](https://cdn-mineru.openxlab.org.cn/result/2025-09-28/a1cd3fdd-045f-44e0-9bdb-7cd0c339f424/c48a831d26b1d774498532177bd8cfc7a41f966ecb9aa369ae9e6b3da04d8ed0.jpg)  
图8. 使用增量记录避免了目录内不同目录修改之间的争用。

相反，Mantle引入增量记录以避免原地更新，从而减轻争用。如图8所示，每个增量记录由复合键标识，该键由父目录ID（pid）、特殊名称字段（'/\_ATTR'）和事务时间戳(TS_txn)组成。每个操作不是直接更新主属性记录（timestep=0的记录），而是简单地插入一个不同的增量记录。例如，在图8中，mkdir(/A/B)和并发的mkdir(/A/C)都需要修改/A的属性元数据。没有增量记录，这些操作会在同一行上冲突。然而，使用增量记录，mkdir插入一个增量（使用TS_1并将链接计数+1），mkdir插入另一个（使用TS_2和-1），消除了冲突。这些增量记录稍后被异步压缩到主目录属性元数据中，共享锁存器确保目录元数据在压缩过程中保持完整且不能被删除。这种延迟更新策略消除了争用，显著减少了高并发环境中的事务中止、重试和尾延迟。

尽管有其优势，这种设计引入了权衡：dirstat操作必须扫描增量记录以计算准确结果。为了解决这个问题，增量记录被选择性启用，仅在目录内持续争用时激活。这种方法有效减少了争用并改善了整体延迟。§6.4中展示了这两种优化的性能优势。

5.2.2 改进跨目录的Dirname。Mantle指定IndexNode作为跨目录dirname的协调器。IndexTable中的每个目录元数据条目包含一个锁位，使Proxy能够通过单个RPC锁定dirname请求中涉及的目录（见图6）。这种方法消除了获取分布式锁的多个RPC的需要。

跨目录dirname工作流，如图9所示，从路径解析(1,2,3)开始。IndexNode将源目录路径添加到RemovalList(4)，并通过在IndexTable中设置源目录路径的锁位来执行循环检测(5)。然后检查从源目录和目标目录的最小公共祖先到目标目录路径上的目录锁状态(6)。路径解析和循环检测的结果返回给Proxy(7)。如果

检测到现有锁，表示与另一个正在进行的重命名冲突，操作中止并重试。如果未发现冲突，Proxy通过分布式事务完成目录重命名，更新MetaTable和IndexTable中的所有相关元数据（8a和8b）。当源目录的访问元数据在IndexTable中删除时，重命名锁自动释放。

5.2.3 改进IndexNode的Raft写入。在繁重的目录修改工作负载下，IndexNode的写入吞吐量通常受到单个Raft组容量的限制。这种限制主要源于频繁fsync操作引入的开销，这些操作需要将数据持久化到磁盘，在密集工作负载期间构成重大瓶颈。为了缓解这个瓶颈，Mantle采用批处理Raft提交机制，将多个操作聚合到单个提交中。通过批处理操作，Mantle有效减少了昂贵的fsync调用频率，降低了磁盘I/O开销，显著提高了高强度工作负载下的吞吐量。

# 5.3 容错

Mantle的设计结合了强大的机制，以确保在组件故障情况下的元数据一致性和高可用性。

对于元数据服务器故障，受影响的Raft组自动发起领导者重新选举。完整性和可用性由Raft协议内在保护。

当代理变得无响应时，客户端重新连接到另一个代理并重新提交待处理请求。为了确保幂等性，每个元数据请求携带唯一的客户端生成的UUID。如图9所示，如果代理在步骤①-⑦期间故障，IndexNode重试这些步骤，同时利用UUID识别原始尝试是否已持有锁，从而避免死锁。如果循环检测成功，新代理继续步骤8a/(8b)作为正常执行以提交2PC事务并释放锁。如果在步骤8a/(8b)期间发生崩溃，标准2PC恢复解析最终提交决策，确保系统达到一致状态。

# 6 评估

# 6.1 实验设置

硬件配置。我们在由53台物理服务器组成的本地集群中进行实验，每台服务器具有128GB DRAM并运行CentOS 6.3（内核4.14.0）。其中，21台服务器用于元数据服务，32台作为客户端生成测试工作负载。三台元数据服务器配备双插槽Intel(R) Xeon(R) Platinum 8350C处理器，用于Mantle的IndexNode、LocoFS的目录元数据服务器和InfiniFS的重命名协调器，运行频率为2.60GHz，64核，每台具有1TB NVMe SSD。其余服务器使用双插槽Intel(R) Xeon(R) Silver 4314 CPU，运行频率为2.40GHz，32核，每台配备八个4TB

表2. 部署配置

<table><tr><td>系统</td><td>元数据</td><td>客户端</td></tr><tr><td>Tectonic</td><td>21×服务器</td><td rowspan="4">32×服务器</td></tr><tr><td>InfiniFS</td><td>3×服务器用于重命名协调器 + 18×服务器用于元数据</td></tr><tr><td>LocoFS</td><td>3×服务器用于目录元数据 + 18×服务器用于对象元数据</td></tr><tr><td>Mantle</td><td>3×服务器用于IndexNode + 18×服务器用于TafDB</td></tr></table>

NVMe SSD。所有服务器通过Mellanox ConnectX-5 25Gbps互连。表2展示了这四种元数据系统的评估部署。所有部署共享相同的数据存储（使用额外的数据服务器）。

基线。我们将Mantle与三个代表性基线进行比较：Tectonic、LocoFS和InfiniFS，它们分别代表了基于DBtable的方法、分层和并行解析。由于它们不是公开的，我们忠实地重新实现了它们。对于Tectonic，我们放松一致性并避免使用分布式事务。在InfiniFS中，我们采用CFS的两事务策略进行目录修改操作。此外，我们实现了InfiniFS的AM-Cache用于元数据缓存，并评估其在真实世界工作负载下的有效性。我们的评估表明，重新实现的基线系统的性能与其原始论文中报告的结果一致。

工作负载。我们使用真实世界工作负载评估元数据服务性能，并调整mdtest基准测试以生成重负载操作，包括对象创建（create）、对象删除（delete）、元数据检索（objstat、dirstat）、目录创建（mkdir）和重命名（dir-remote）。这些工作负载使用平均路径深度10。通过mdtest测量吞吐量，记录内部和客户端延迟。对于并行处理，我们使用OpenMPI 5.0.2在客户端节点上启动mdtest进程。注意，数据存储仅在应用程序评估中使用（§6.2）。所有其他评估仅涉及元数据，这是本文的重点。在运行实验之前，我们使用mdtest用数据填充每个系统，将命名空间规模扩展到10亿条目，对象与目录比例为10:1。

# 6.2 应用程序评估

我们使用两个应用程序来测试Mantle为真实世界应用程序带来的性能改进。面向AI的音频预处理（Audio）通过将长音频输入分割为几秒长的片段来处理，涉及总共200GB的数据。Spark分析（Analytics）执行交互式数据分析，涉及10GB数据并包含大量元数据操作。首先，我们剔除数据访问并记录完成时间。然后，我们重新启用数据访问并记录端到端

![](https://cdn-mineru.openxlab.org.cn/result/2025-09-28/a1cd3fdd-045f-44e0-9bdb-7cd0c339f424/31697f0c2c769434317a3cd490e8089278c183fa33611651864ca6f5cd6642ff.jpg)

![](https://cdn-mineru.openxlab.org.cn/result/2025-09-28/a1cd3fdd-045f-44e0-9bdb-7cd0c339f424/3fafdfad771db0cd17a58ed7d4707b831f473ebd483c867ce1d1b4f379cb67e8.jpg)  
(a) 无数据访问  
图10. 真实世界工作负载的完成时间  
(b) 有数据访问

完成时间。同时，我们在每个应用程序中选择代表性元数据操作，并在图11中显示其延迟分布。

图10a说明了禁用数据访问时两个工作负载的完成时间。在Analytics工作负载中，所有目录修改操作都针对临时目录的同一属性，导致严重冲突。Tectonic的完成时间比InfiniFS长75.0%，因为Tectonic的目录修改操作实现缺乏一致性保证。相比之下，InfiniFS使用分布式事务确保一致性，但其dirname性能因争用导致的大量事务重试而受损。如图11b所示，10.6%的dirname操作经历超过5秒的延迟，峰值延迟为52秒。LocoFS进一步将完成时间减少27.0%，但其完成时间仍比Mantle长225%。尽管LocoFS避免了分布式事务中的冲突，但子目录列表中相同键的修改通过锁存器序列化，限制了性能。LocoFS和Tectonic中mkdir和dirname的CDF曲线非常相似，如图11a和图11b所示。LocoFS通过在集中节点上完成这些操作显示出轻微优势。

与Analytics工作负载不同，Audio工作负载仅包含非冲突元数据操作，突出了路径解析性能。Tectonic因其低效的逐级路径解析方法表现最差。InfiniFS表现稍好，完成时间减少23.9%，尽管未实现显著改进。这是因为推测性路径解析产生的大量子线程导致相当大的延迟变异性。

![](https://cdn-mineru.openxlab.org.cn/result/2025-09-28/a1cd3fdd-045f-44e0-9bdb-7cd0c339f424/810d98d42cd323adfc8b21b479c82892355bc1c6830a39c58bcaac7ac4f6224.jpg)

![](https://cdn-mineru.openxlab.org.cn/result/2025-09-28/a1cd3fdd-045f-44e0-9bdb-7cd0c339f424/ffe04b084f6d06f4cd6ea5f5b00a4f5271272ea026ee262cd11ab2ba3f9c1a12.jpg)

![](https://cdn-mineru.openxlab.org.cn/result/2025-09-28/a1cd3fdd-045f-44e0-9bdb-7cd0c339f424/8b940ab8aa7f51e594278b95db5b93103ec31df8f3135a35e15bd59bad65a079.jpg)  
(c) Audio - create

![](https://cdn-mineru.openxlab.org.cn/result/2025-09-28/a1cd3fdd-045f-44e0-9bdb-7cd0c339f424/8f2cd48c5796fcaf19ddd5ad878a09b51013163adc77e8bc7cc71c1b490bd7bb.jpg)  
图11. 仅执行元数据操作时应用程序工作负载中元数据操作的延迟CDF  
(d) Audio - objstat

如图11c和图11d所示，InfiniFS元数据操作的延迟分布广泛。虽然一小部分objstat操作实现接近Mantle的延迟，但其尾延迟显著较高。LocoFS的单节点路径解析设计缺乏扩展中心节点的优化，导致路径解析延迟增加，完成时间与InfiniFS相当。相比之下，Mantle与LocoFS相比将完成时间减少了40.8%。CDF曲线进一步证明Mantle在这些操作中实现了高效率。

最后，图10b显示了启用数据访问时两个工作负载的完成时间。对于Analytics工作负载，元数据操作主导完成时间，导致与禁用数据访问时相比差异很小。这是因为启用数据访问通过将数据访问与先前必要的阻塞期重叠来减少阻塞时间。与Tectonic、InfiniFS、LocoFS相比，Mantle分别将完成时间减少了73.2%、93.3%和63.3%。然而，Audio工作负载经历完成时间的显著增加，减少了Mantle的相对改进。尽管如此，与Tectonic、InfiniFS、LocoFS相比，Mantle分别将完成时间减少了47.7%、40.1%和38.5%。

# 6.3 单个命名空间操作

我们使用512个客户端的mdtest评估七个关键操作的性能：create、delete、objstat、dirstat、mkdir、rmdir和dirname。这些操作分为：(1)对象和目录读取操作（create、delete、objstat、dirstat）和(2)目录修改操作（mkdir、rmdir、dirname）。我们测量每个操作的吞吐量和延迟，并将延迟分解

![](https://cdn-mineru.openxlab.org.cn/result/2025-09-28/a1cd3fdd-045f-44e0-9bdb-7cd0c339f424/27c9a8747368f33d466cc666e08442d57e3b6b145712a95b711f4330595be19.jpg)

![](https://cdn-mineru.openxlab.org.cn/result/2025-09-28/a1cd3fdd-045f-44e0-9bdb-7cd0c339f424/15727d81641c3a6bc9dabd16740681ad204a04ce79e455aa31ce610e3bd6d798.jpg)  
图12. 对象操作和目录读取操作的吞吐量  
图13. 对象操作和目录读取操作的延迟分解（T代表Tectonic，I代表InfiniFS，L代表LocoFS，M代表Mantle）

为三个阶段：(1)查找（路径解析），(2)循环检测（可选），和(3)执行。查找阶段检索目录ID（pid）。循环检测仅适用于dirname操作期间，识别并解析潜在的目录循环。执行阶段允许元数据服务使用pid读取或更新元数据。InfiniFS为objstat绕过执行阶段，在查找阶段处理它，而LocoFS在目录操作的执行阶段解析路径。Mantle在dirname中记录零查找时间，因为它与循环检测合并。

对象和目录读取操作。图12和13说明了对象和目录读取操作的性能，这主要由路径解析的效率决定。因此，所有操作的性能趋势相似，从最差到最佳排序为Tectonic、InfiniFS、LocoFS和Mantle。Tectonic由于其分层路径遍历的高开销而表现最低性能。InfiniFS虽然优于Tectonic，但仅实现了0.19-0.37倍的边际改进，归因于并行解析中的线程过度配置。LocoFS与InfiniFS相比进一步将吞吐量提高了0.32-0.83倍，其集中式路径解析设计有效减少了路径遍历开销。然而，其可扩展性受到限制，因为本地记录检索耗尽了目录元数据服务器的CPU资源。Mantle借助TopDirPathCache和跟随者读取实现最高性能，在所有四种操作中保持最低的路径解析延迟。

Mantle在dirstat、objstat和delete操作中引入了相对于其他基线系统的显著性能改进。对于create，LocoFS实现与Mantle相当的吞吐量。这种相似性是由于mdtest基准测试中的数据层修改，这导致元数据的额外

属性更新。这些更新减少了路径解析工作负载，从而缩小了LocoFS和Mantle之间的性能差距。

总之，Mantle分别将查找延迟降低了83.9-89.0%、80.0-84.2%和16.4-74.5%，相比Tectonic、InfiniFS和LocoFS，实现了2.49-4.30倍、1.96-3.44倍和1.07-2.50倍的加速。

目录修改操作。我们使用mdtest在冲突和非冲突工作负载下评估目录修改操作。在冲突工作负载中（后缀'-s'），线程在共享目录上操作，而在非冲突工作负载中（后缀'-e'），每个线程使用自己的专用目录。省略rmdir的结果，因为它们与mkdir相似。

如图14和15所示，在mkdir-e中，Tectonic和InfiniFS的吞吐量非常接近。Tectonic在执行阶段表现稍好，而InfiniFS在查找阶段显示轻微优势。因此，它们的整体性能相似。两个系统都受到元数据服务器瓶颈的限制，在饱和读取（用于查找）和写入（用于重命名）请求下。LocoFS的吞吐量受到Raft吞吐量的限制，导致这些系统中性能最差。Mantle实现最高吞吐量，较短的查找阶段补偿了其较长的执行阶段。Mantle的吞吐量受限于单个Raft组，需要进一步优化以扩展。尽管如此，根据我们的生产经验（见§7），其当前吞吐量能力足以满足我们的部署需求。

在mkdir-s中，对父目录属性的修改在Tectonic和LoCoFS中都通过锁存器序列化。虽然InfiniFS应用单分片原子原语以避免重试，但其性能仍与非冲突场景相比有所不足。Mantle使用增量记录将吞吐量比InfiniFS提高了1.96倍，这实现了对目录属性的完全并发更新。

对于dirname-e，趋势与mkdir-e中观察到的相似，但出现两个关键差异：(1)dirname需要两轮路径解析，扩大了性能差距；(2)循环检测开销在InfiniFS、LocoFS和Mantle中产生额外开销。因此，Mantle实现了比Tectonic高26.2%的吞吐量。循环检测增加了LocoFS和Mantle的中心节点压力，LocoFS显示最低性能。Mantle通过其优化的dirname程序减轻额外开销，尽管循环检测成本较高，仍实现最高吞吐量。在dirname-s中，Tectonic、InfiniFS和LocoFS的性能因冲突而显著下降。Mantle使用增量记录消除冲突，实现最高性能。

总之，Mantle在目录修改操作中展示了显著的性能改进，相比Tectonic、InfiniFS和LocoFS分别实现了1.20-20.9倍、1.16-116.00倍和2.87-80.78倍的加速。

# 6.4 单个优化的效果

我们通过逐步启用各个优化来评估每个优化的影响。我们从Mantle的基础配置（Mantle-base）开始，其中TopDirPathCache被禁用。'+pathcache'配置激活TopDirPathCache，增强Mantle的效率。后续增强包括'+raftlogbatch'和'+deltarecord'，分别实现Raft日志批处理和增量记录。最后，'+followerread'在Raft中启用跟随者读取以加速查找。我们使用三个mdtest工作负载测试这些配置：dirstat、mkdir-e和dirname-s。

图16报告了相对于Mantle-base的标准化吞吐量。'+pathcache'优化有效将dirstat的吞吐量翻倍，'+followerread'优化进一步改进。Mantle-base中mkdir-e的吞吐量受到IndexNode中Raft日志提交速度的限制。'+raftlogbatch'优化通过摊销提交开销生效。在Mantle-base中，dirname-s产生大量冲突，导致数据库事务失败和重试。'+deltarecord'优化消除了这些冲突并提高了吞吐量。

![](https://cdn-mineru.openxlab.org.cn/result/2025-09-28/a1cd3fdd-045f-44e0-9bdb-7cd0c339f424/0ca293f76e0e3da6ff05ac8764b31954c0982e000ea7963d081ea6d9faec0dc0.jpg)  
图17. 深度对路径解析的影响

![](https://cdn-mineru.openxlab.org.cn/result/2025-09-28/a1cd3fdd-045f-44e0-9bdb-7cd0c339f424/cb4dc6467894701acbbd7baf7b21522dd00fc5e0d2a7cfe15272e77482a9de58.jpg)  
图18. IndexNode中k的影响

# 6.5 其他因素研究

深度对路径解析的影响。我们评估各种目录深度下的路径解析延迟，以评估对系统性能的影响。我们在32台服务器中启动512个线程发送查找请求。如图17所示，Tectonic和InfiniFS的路径解析延迟随目录深度增加而增加。值得注意的是，对于十级路径，Tectonic和InfiniFS的延迟分别比单级路径高6.82倍和6.4倍。Tectonic的查找延迟相对于路径深度呈线性增加，主要由于顺序调度等效数量RPC的相关延迟。相比之下，InfiniFS在较大深度下遇到瓶颈，由于线程过度配置。LocoFS在路径深度达到6之前显示出与Mantle相当的查找延迟，此时CPU限制成为瓶颈。相比之下，Mantle表现出显著的稳定性，其十级路径的查找延迟仅比单级路径高1.09倍，展示了其卓越的可扩展性和效率。

TopDirPathCache中k的影响。我们禁用跟随者读取，并通过运行512个线程发出查找请求并测量不同k值（范围从1到5）的延迟来评估TopDirPathCache中k的影响。我们还分析了命名空间ns4（见§3）并计算在不同k设置下可以缓存的目录部分。如图18所示，查找延迟随k值增大而增加。在k=3时（我们系统中选择的值），标准化（相对于Mantle-base）延迟为0.32，比k=1的延迟慢31.1%。然而，与k=1相比，内存消耗减少了88%。在生产中，我们设置k=3以在性能和内存使用之间取得平衡。

IndexNode可扩展性。我们通过运行mdtest的objstat和create工作负载来评估Mantle的可扩展性，同时改变命名空间大小和线程数量。首先，我们使用512个线程进行实验，将命名空间大小从10亿条目变化到100亿条目，结果如图19a所示。无论命名空间大小如何增加，吞吐量保持一致，证明Mantle能够有效处理高达100亿规模的命名空间。接下来，我们通过在100亿命名空间上将客户端数量从64变化到2048来评估Mantle相对于线程数量的可扩展性。为了分析跟随者读取的影响，我们在3种配置下评估objstat工作负载：无跟随者读取（objstat）、具有2个跟随者的跟随者读取（+followers）和具有额外2个学习者副本的跟随者读取（+learners）。

如图19b所示，create工作负载的吞吐量线性扩展到512个线程，在64个线程时达到18.8 Kop/s，在512个线程时达到133.5 Kop/s。然而，随着线程数增加到1024，吞吐量仅增加28.3%，受到TafDB达到其最大容量的限制。对于objstat工作负载，吞吐量在64和256个线程时分别达到101.6 Kop/s和376.5 Kop/s，但在512个线程时趋于平稳，由于单个节点的容量限制。超过1024个线程，未观察到进一步的吞吐量增加。相比之下，启用具有2个跟随者的跟随者读取允许objstat扩展超过512个线程，在2048个线程时实现1280.0 Kop/s的吞吐量。添加两个学习者副本进一步增强可扩展性，使objstat在2048个线程时达到1894.5 Kop/s的吞吐量。

添加元数据缓存。虽然元数据缓存未在Mantle的设计中采用，但我们评估其在Mantle主要关注范围之外的潜在场景中的有效性，在这些场景中它可能仍然适用。我们为InfiniFS和Mantle配备元数据缓存，并在两个真实世界应用程序上测量其性能。图20说明了元数据缓存对InfiniFS和Mantle在Analytics和Audio工作负载上性能的影响。对于Analytics工作负载，元数据缓存带来适度的改进。这种有限的改进是由于Analytics工作负载的性质，它由目录修改主导。相比之下，对于Audio工作负载，

表3. Cluster-C中5个命名空间的特征。小对象指大小≤512KB的对象。

<table><tr><td rowspan="2">名称</td><td rowspan="2">#对象</td><td rowspan="2">#目录</td><td rowspan="2">小对象比例</td><td colspan="2">峰值吞吐量(Kop/s)</td></tr><tr><td>查找</td><td>mkdir</td></tr><tr><td>C1</td><td>3.2B</td><td>27M</td><td>62.0%</td><td>400</td><td>24</td></tr><tr><td>C2</td><td>2.1B</td><td>194M</td><td>29.2%</td><td>300</td><td>12</td></tr><tr><td>C3</td><td>1.2B</td><td>145M</td><td>33.7%</td><td>350</td><td>18</td></tr><tr><td>C4</td><td>0.8B</td><td>88M</td><td>28.8%</td><td>175</td><td>11</td></tr><tr><td>C5</td><td>75M</td><td>9M</td><td>28.1%</td><td>215</td><td>9</td></tr></table>

元数据缓存带来显著的性能改进。InfiniFS的执行时间从115.1秒大幅减少到63.0秒。Mantle也从缓存中受益，其执行时间从68.9秒减少到63.0秒。然而，Mantle的相对改进较小，因为它已经实现了高效的单RPC路径解析机制，通过元数据缓存进行优化的空间较小。

# 7 Mantle在生产环境中

# 7.1 部署概述

Mantle已与遗留COSS系统集成，取代了其基于DBtable的元数据管理。新的COSS系统同时支持平面和分层命名空间，形成统一的存储基础。

Mantle和COSS系统已部署在三个集群（A、B和C）中，托管19个内部命名空间和众多外部命名空间。这些内部命名空间分别分布在集群A、B和C中7、7和5个。在每个集群内，所有命名空间共享一个共同的TafDB部署。这些命名空间服务于不同的应用程序领域，包括元数据密集型任务（如AI数据预处理、即席SQL查询）和I/O密集型任务（如模型检查点、批量分析）。这些工作负载产生平均每天数十亿次的元数据请求量。

# 7.2 经验和教训

小对象的普遍性。为了提供生产工作负载的详细分析，我们专注于托管在集群C中的五个代表性内部命名空间，它们对应我们的AI训练（C1-C3、C5）、数据仓库（C2、C3）、广告（C2-C3）、金融处理（C3）、日志分析（C3、C5）和搜索引擎（C2-C4）工作负载。表3总结了它们的特征。这些命名空间代表了一系列用例，从成熟的十亿规模工作负载（C1-C3）到快速增长的服务（C4、C5）。这些命名空间中的一个关键工作负载特征是小对象（≤512 KB）的普遍性，范围从大约30%到C1的超过60%。高比例的小对象表明许多工作负载本质上是元数据密集型的，可以

受益于Mantle的低延迟路径解析。在这些真实世界条件下，系统为这些命名空间提供175-400 Kop/s的峰值查找吞吐量和9-24 Kop/s的峰值mkdir吞吐量。这些生产峰值仅代表Mantle全部吞吐量能力的一小部分，为未来工作负载增长留下了显著空间。

共置IndexNode以提高资源利用率。IndexNode的高单节点吞吐量实现了高效的运营策略：在共享硬件上共置多个命名空间的IndexNode以最大化资源利用率。具体来说，我们维护一个共享的物理服务器池来托管所有命名空间的IndexNode副本。在池内，较小命名空间（如C4、C5）的领导者可以共享一个节点，而大型高流量命名空间的领导者可以分配专用节点。为避免热点，我们采用动态机制重新平衡领导者分布并根据需求添加新的物理节点。

跨工作负载配置文件的影响。Mantle的性能影响高度依赖于工作负载配置文件，元数据密集型应用程序受益最大。例如，我们在§3.1中描述的数据预处理任务的端到端完成时间在迁移到Mantle后下降了超过40%。相比之下，I/O密集型工作负载没有经历性能退化，同时仍从加速的查找中受益。除了应用级加速之外，Mantle的高效率通过减少所需的元数据服务器数量提供了运营效益。

优化潜力。我们的分析显示，Mantle的可扩展性目前受到IndexNode的CPU资源限制。进一步减少CPU使用的优化可以在不进行重大架构更改的情况下扩展Mantle处理未来工作负载增长的能力。例如，概念验证实现表明，在RPC框架中采用RDMA可以将每节点路径解析吞吐量从500K ops/s提升到1M ops/s。

# 8 相关工作

元数据管理。早期分布式文件系统将元数据与数据分离，并将元数据存储在集中式元数据服务器中[13, 16, 24, 29, 32]。为了提高可扩展性，许多架构将元数据分布在多个服务器上[11, 25, 45, 50, 51, 55]。最近，除了上述系统之外，CalvinFS [47]、ADLS [37]、HopsFS [30]和λFS [7]利用分布式数据库进行元数据管理。

使用不同的元数据分区机制在元数据服务器之间分布元数据。子树分区[27, 48, 50]容易导致负载不平衡[46]。完整路径索引实现细粒度负载平衡[25, 35]，但产生多个RPC查找，因为权限检查必须逐步遍历目录层次结构。此外，缺乏局部性导致高协调开销。为了解决这些挑战，范围分区[30, 43]（也被Mantle采用）用于实现负载平衡同时减轻分布式协调。但我们的评估显示，这种策略在目录修改操作中仍然引入了显著的协调开销。

路径解析。客户端缓存[21, 26, 38, 42]用于最小化或消除查找RPC。然而，这种方法对于COSS不可行。InfiniFS [26]推测性地使用并行RPC进行快速查找，但在高并发下扩展性差。FalconFS [52]惰性地复制所有元数据以在任何元数据服务器中实现本地查找，但由于命名空间规模巨大，这种方法在COSS中不切实际。CephFS [50]和FileScale [22]在子树分区之上实现服务器端缓存以提高查找延迟，但它们未能实现Mantle启用的单RPC查找。此外，它们的方法容易导致负载不平衡并产生高迁移开销[46]。Duplex [10]在中心节点上缓存路径权限，产生高一致性开销，并在故障后回退到并行解析。相比之下，Mantle使用高效的数据结构和轻量级索引来确保快速查找、低维护开销和故障恢复能力。

其他工作。几项研究提出了具有高写入吞吐量的复制协议[12, 18, 34, 36, 53]，为增强Mantle中的目录修改性能提供了机会。其他工作利用新兴硬件，如PM、RDMA和SmartNIC，用于高性能元数据服务[2, 15, 19, 23, 54]，这些与Mantle正交，并可能补充其设计。

# 9 结论

Mantle为云对象存储服务提供了分层元数据管理的可扩展解决方案，解决了高路径解析延迟和目录修改争用等挑战。通过利用IndexNode进行单RPC查找、增量记录、基于Raft的复制和平衡的工作负载分配，Mantle优于现有系统。评估显示其能够处理十亿规模的命名空间，具有高吞吐量和低延迟，使其成为数据密集型云应用程序和未来元数据服务的理想选择。

# 10 致谢

我们感谢所有匿名评审员的深刻反馈，以及我们的指导者George Amvrosiadis在准备我们的相机就绪提交期间的指导。我们还要感谢王萌、王尧和王新星对Mantle开发的贡献。我们也感谢金泽文和朱佳安在图表方面的帮助。这项工作部分得到了中国国家重点研发计划（批准号：2024YFB4505701）和安徽省高校协同创新计划（批准号：GXXT-2022-045）的支持。李成和曹彪是通讯作者。

# 参考文献

[1] Marco Abela. 云存储存储AI概述，Lustre、GCSFuse和Anywhere cache与Google Cloud。https://techfieldday.com/video/overview-of-cloud-storage-storage-for-ai-lustre-gcsfuse-and-anywhere-cache-with-google-cloud/。访问于2025年8月10日。  
[2] Thomas E Anderson, Marco Canini, Jongyul Kim, Dejan Kostic, Youngjin Kwon, Simon Peter, Waleed Reda, Henry N Schuh, and Emmett Witchel. Assise: 通过分布式文件系统中的客户端本地NVM实现性能和可用性。在第14届USENIX操作系统设计与实现研讨会（OSDI 20）中，第1011-1027页，2020年。  
[3] Srinivasan Archana and Kumar SR Arun. 识别冷对象以归档到Amazon S3 Glacier存储类别。https://aws.amazon.com/cn/blogs/storage/identify-cold-objects-for-archiving-to-amazon-s3-glacier-storage-classes/。访问于2025年4月10日。  
[4] AWS. Amazon S3 Express One Zone存储类别。https://aws.amazon.com/cn/s3/storage-classes/express-one-zone/。访问于2024年4月8日。  
[5] AWS. AWS S3 API参考。https://docsAWS.amazon.com/AmazonS3/latest/API/Type_API_Reference.html。访问于2024年11月21日。  
[6] Scott A Brandt, Ethan L Miller, Darrell DE Long, and Lan Xue. 大型分布式存储系统中的高效元数据管理。在第20届IEEE/第11届NASA戈达德大规模存储系统与技术研讨会（MSST 03）中，第290-298页。IEEE，2003年。  
[7] Benjamin Carver, Runzhou Han, Jingyuan Zhang, Mai Zheng, and Yue Cheng. λfs: 使用无服务器函数的可扩展弹性分布式文件系统元数据服务。在第28届ACM国际编程语言与操作系统支持会议论文集，第4卷，ASPLOS '23，第394-411页，美国纽约州纽约市，2023年。美国计算机协会。  
[8] CFS. cfs的Posix兼容性。https://github.com/cfs-for-review/CFS-for-review。访问于2024年4月8日。  
[9] Giuseppe DeCandia, Deniz Hastorun, Madan Jampani, Gunavardhan Kakulapati, Avinash Lakshman, Alex Pilchin, Swaminathan Sivasubramanian, Peter Vosshall, and Werner Vogels. Dynamo: Amazon的高可用键值存储。在第21届ACM SIGOPS操作系统原理研讨会论文集（SOSP 07），第205–220页，美国纽约州纽约市，2007年。美国计算机协会。  
[10] Chao Dong, Fang Wang, Yuxin Yang, Mengya Lei, Jianshun Zhang, and Dan Feng. 分布式文件系统的低延迟可扩展全路径索引元数据服务。在2023年IEEE第41届国际计算机设计会议（ICCD 23）中，第283-290页，2023年。  
[11] John JD Douceur and Jon Howell. Farsite文件系统中的分布式目录服务。在第7届操作系统设计与实现研讨会（OSDI 06）论文集中，2006年。  
[12] Aishwarya Ganesan, Ramnatthan Alagappan, Andrea C. Arpaci-Dusseau, and Remzi H. Arpaci-Dusseau. 利用零外部性实现快速复制存储。在ACM SIGOPS第28届操作系统原理研讨会论文集，SOSP '21，第440-456页，美国纽约州纽约市，2021年。美国计算机协会。  
[13] Sanjay Ghemawat, Howard Gobioff, and Shun-Tak Leung. Google文件系统。在第19届ACM操作系统原理研讨会（SOSP '03）论文集中，2003年。  
[14] Google. Google Cloud Storage APIs。https://cloud.google.com/storage/docs apis。访问于2024年11月21日。  
[15] Hao Guo, Youyou Lu, Wenhao Lv, Xiaojian Liao, Shaoxun Zeng, and Jiwu Shu. SingularFS: 使用单个元数据服务器的十亿规模分布式文件系统。在2023年USENIX年度技术会议（USENIX ATC 23）中，第915-928页，2023年。  
[16] Apache Hadoop. Hadoop分布式文件系统。http://hadoop.apache.org。访问于2024年4月8日。

[17] MIT Technology Review Insights. 2023年全球云生态系统。https://www.technologyreview.com/2023/11/16/1078645/2023-global-cloud-ecosystem/。访问于2025年4月10日。  
[18] Manos Kapritsos, Yang Wang, Vivien Quema, Allen Clement, Lorenzo Alvisi, and Mike Dahlin. All about eve: 多核服务器的执行-验证复制。在第10届USENIX操作系统设计与实现研讨会（OSDI 12）中，第237–250页，美国加利福尼亚州好莱坞，2012年10月。USENIX协会。  
[19] Jongyul Kim, Insu Jang, Waleed Reda, Jaeseong Im, Marco Canini, Dejan Kostic, Youngjin Kwon, Simon Peter, and Emmett Witchel. LineFS: 具有流水线并行性的分布式文件系统的SmartNIC高效卸载。在ACM SIGOPS第28届操作系统原理研讨会论文集（SOSP 21）中，第756-771页，2021年。  
[20] Leslie Lamport. Paxos简单化。ACM SIGACT新闻（分布式计算专栏）32, 4（总第121期，2002年12月），第51-58页，2001年。  
[21] Siyang Li, Youyou Lu, Jiwu Shu, Yang Hu, and Tao Li. LocoFS: 分布式文件系统的松耦合元数据服务。在国际高性能计算、网络、存储与分析会议（SC 17）论文集中，2017年。  
[22] Gang Liao and Daniel J Abadi. Filescale: 分布式文件系统的快速弹性元数据管理。在2023年ACM云计算研讨会（SoCC 23）论文集中，第459-474页，2023年。  
[23] Youyou Lu, Jiwu Shu, Youmin Chen, and Tao Li. Octopus: 启用RDMA的分布式持久内存文件系统。在2017年USENIX年度技术会议（ATC 17）中，第773-785页，2017年。  
[24] Lustre. Lustre文件系统。http://www.lustre.org。访问于2024年4月8日。  
[25] Lustre. Lustre元数据服务。https://wiki.lustre.org/Lustre_Metadata_Service_(MDS)。访问于2024年4月8日。  
[26] Wenhao Lv, Youyou Lu, Yiming Zhang, Peile Duan, and Jiwu Shu. InfiniFS: 大规模分布式文件系统的高效元数据服务。在第20届USENIX文件与存储技术会议（FAST 22）中，第313-328页，2022年。  
[27] Stathis Maneas and Bianca Schroeder. Hadoop分布式文件系统的演变。在2018年第32届高级信息网络与应用国际会议研讨会（WAINA）中，第67-74页，2018年。  
[28] Microsoft. Azure Data Lake Storage Gen2 REST APIs。https://learn.microsoft.com/en-us/rest/api/storageservices/data-lake-storage-gen2。访问于2024年11月21日。  
[29] MooseFS. MooseFS - PB级分布式文件系统。https://moosefs.com。访问于2024年4月8日。  
[30] Salman Niazi, Mahmoud Ismail, Seif Haridi, Jim Dowling, Steffen Grohsschmiedt, and Mikael Ronström. HopsFS: 使用NewSQL数据库扩展分层文件系统元数据。在第15届USENIX文件与存储技术会议（FAST 17）中，第89-104页，2017年。  
[31] Diego Ongaro and John K. Ousterhout. 寻找可理解的共识算法。在2014年USENIX年度技术会议（ATC 12）中，美国宾夕法尼亚州费城，2012年6月19-20日，第305-319页。USENIX协会，2014年。  
[32] Michael Ovsiannikov, Silvius Rus, Damian Reeves, Paul Sutter, Sriram Rao, and Jim Kelly. Quantcast文件系统。VLDB杂志（VLDB 13），6(11):1092-1101，2013年。  
[33] Satadru Pan, Theano Stavrinos, Yunqiao Zhang, Atul Sikaria, Pavel Zakharov, Abhinav Sharma, Mike Shuey, Richard Wareing, Monika Gangapuram, Guanglei Cao, et al. Facebook的Tectonic文件系统：来自艾字节规模的效率。在第19届USENIX文件与存储技术会议（FAST 21）中，第217-231页，2021年。  
[34] Seo Jin Park and John Ousterhout. 利用交换性实现实用快速复制。在第16届USENIX网络系统设计与实现研讨会（NSDI 19）中，第47-64页，美国马萨诸塞州波士顿，2019年2月。USENIX协会。

[35] Swapnil Patil and Garth A Gibson. GIGA+的规模和并发性：具有数百万文件的文件系统目录。在第9届USENIX文件与存储技术会议（FAST 11）中，2011年。  
[36] Dan R. K. Ports, Jialin Li, Vincent Liu, Naveen Kr. Sharma, and Arvind Krishnamurthy. 使用数据中心网络中的近似同步设计分布式系统。在第12届USENIX网络系统设计与实现研讨会（NSDI 15）中，第43-57页，美国加利福尼亚州奥克兰，2015年5月。USENIX协会。  
[37] Raghu Ramakrishnan, Baskar Sridharan, John R Douceur, Pavan Kasturi, Balaji Krishnamachari-Sampath, Karthick Krishnamoorthy, Peng Li, Mitica Manu, Spiro Michaylov, Rogério Ramos, et al. Azure Data Lake Store: 大数据分析的超大规模分布式文件服务。在2017年ACM国际数据管理会议（SIGMOD 17）论文集中，第51-63页，2017年。  
[38] Kai Ren, Qing Zheng, Swapnil Patil, and Garth Gibson. IndexFS: 通过无状态缓存和批量插入扩展文件系统元数据性能。在国际高性能计算、网络、存储与分析会议（SC 14）论文集中，2014年。  
[39] HPC IO基准测试存储库。IOR和mdtest。https://github.com/hpc/ior。访问于2024年4月8日。  
[40] Emergen Research. 全球云对象存储市场。https://www.emergenresearch.com/press-release/global-cloud-object-storage-market。访问于2025年4月10日。  
[41] Sambhav Satija, Chenhao Ye, Ranjitha Kosgi, Aditya Jain, Romit Kankaria, Yiwei Chen, Andrea C. Arpaci-Dusseau, Remzi H. Arpaci-Dusseau, and Kiran Srinivasan. Cloudscape: 现代云架构中存储服务的研究。在第23届USENIX文件与存储技术会议论文集，FAST '25，美国，2025年。USENIX协会。  
[42] Spencer Shepler. Nfsv4。在2005年USENIX年度技术会议（USENIX ATC 05）中，2005年。  
[43] Konstantin V Shvachko and Yuxiang Chen. 使用giraffa文件系统扩展命名空间操作。USENIX; login, 2017年。  
[44] Sacheendra Talluri, Alicia Luszczak, Cristina L Abad, and Alexandru Iosup. 云中大数据存储工作负载的特征。在2019年ACM/SPEC国际性能工程会议论文集中，第33-44页，2019年。  
[45] Houjun Tang, Suren Byna, Bin Dong, Jialin Liu, and Quincey Koziol. SoMeta: 高性能计算的可扩展对象中心元数据管理。在2017年IEEE国际集群计算会议（CLUSTER 17）中，第359-369页。IEEE，2017年。  
[46] Mouratidis Theofilos. Ceph MDS平衡问题。https://www.spinics.net/lists/ceph-devel msg44650.html。访问于2025年4月10日。  
[47] Alexander Thomson and Daniel J Abadi. CalvinFS: 分布式文件系统的一致WAN复制和可扩展元数据管理。在第13届USENIX文件与存储技术会议（FAST 15）中，第1-14页，2015年。  
[48] Yiduo Wang, Cheng Li, Xinyang Shao, Youxu Chen, Feng Yan, and Yinlong Xu. Lunule: CephFS的敏捷明智元数据负载均衡器。在国际高性能计算、网络、存储与分析会议（SC 21）论文集中，第1-16页，2021年。  
[49] Yiduo Wang, Yufei Wu, Cheng Li, Pengfei Zheng, Biao Cao, Yan Sun, Fei Zhou, Yinlong Xu, Yao Wang, and Guangjun Xie. CFS: 通过修剪关键部分范围扩展分布式文件系统的元数据服务。在第18届欧洲计算机系统会议论文集（EuroSys 23），第331-346页，美国纽约州纽约市，2023年。美国计算机协会。  
[50] Sage A. Weil, Scott A. Brandt, Ethan L. Miller, Darrell D. E. Long, and Carlos Maltzahn. Ceph: 可扩展高性能分布式文件系统。在第7届USENIX操作系统设计与实现研讨会（OSDI '06）中，美国华盛顿州西雅图，2006年11月。USENIX协会。

[51] Jing Xing, Jin Xiong, Ninghui Sun, and Jie Ma. 自适应可扩展元数据管理以支持万亿文件。在高性能计算网络、存储与分析会议（SC 09）论文集中，第1-11页。IEEE，2009年。  
[52] Jingwei Xu, Junbin Kang, Mingkai Dong, Mingyu Liu, Lu Zhang, Shaohong Guo, Ziyan Qiu, Mingzhen You, Ziyi Tian, Anqi Yu, Tianhong Ding, Xinwei Hu, and Haibo Chen. FalconFS: 大规模深度学习流水线的分布式文件系统。在第23届USENIX网络系统设计与实现研讨会（NSDI 26）中，美国华盛顿州伦顿，2026年5月。USENIX协会。  
[53] Yi Xu, Henry Zhu, Prashant Pandey, Alex Conway, Rob Johnson, Aishwarya Ganesan, and Ramnatthan Alagappan. IONIA:现代基于磁盘的KV存储的高性能复制。在第22届USENIX文件与存储技术会议（FAST 24）中，第225-241页，2024年。  
[54] Jian Yang, Joseph Izraelevitz, and Steven Swanson. Orion: 非易失性主内存和RDMA capable网络的分布式文件系统。在第17届USENIX文件与存储技术会议（FAST 19）中，第221-234页，2019年。  
[55] Yifeng Zhu, Hong Jiang, Jun Wang, and Feng Xian. HBA: 大规模基于集群的存储系统的分布式元数据管理。IEEE并行与分布式系统杂志（TPDS），19(6):750-763，2008年。
