# Lec 1 - Introduction

**分布式系统** 核心是通过网络使一群计算机相互通信来完成一些任务。主要用于存储与计算（许多目标是能够从分布式存储和计算基础结构中发现一些可抽象的东西并设计为接口以简化使用）。

使用分布式系统的 **原因**：

- 通过并行增加容量、提高性能（大量CPU、大量内存、大量硬盘等）
- 可通过冗余（复制）来容错（两台计算机做同样的事，若一台故障，可切换到另一台执行）

- 物理空间上的扩展
- 通过将计算拆分至多机（隔离）来实现安全性

使用分布式系统面临的 **挑战**：

- 有许多并发执行的部分，需要进行复杂的交互
- 需要解决部分故障

- 想要获得好的性能需要精心的设计

构建分布式系统的 **工具**：

- **RPC** ：将底层通过不可靠网络进行通信这个事实隐藏
- **线程**：需要通过并发编程技术驾驭多核计算机，线程可以提供结构化并发操作
- **并发控制**：锁 

关于 **性能** ( **performance** )：

- 构建分布式系统的高级目标是通过扩展进行加速（如使用两倍计算机或资源可以获得两倍的性能或吞吐量）
- 不能通过无限制地增加计算机数量来使性能全面提升（计算机很多的情况下，数据库的访问会称为瓶颈）

关于 **容错** ( **fault tolerance** )：

- 大量计算机组成的系统中几乎无时不刻有计算机故障（运行错误、运行缓慢、网络崩溃等）
- 分布式系统要尽可能多地将故障隐藏起来，这样应用开发人员就无须为各种故障提供完整而复杂的应对方案
- 常用思想：
  - 可用性：当遇到故障时，系统可继续执行，提供稳定服务（复制服务器，如果一个服务器崩溃了，可使用另外的服务器）
  - 可恢复性：修复问题后，系统可以恢复并正确运行（可将新数据存于硬盘，恢复运行后系统将数据取回）

关于 **一致性** ( **consistency** )：

- 强一致性：（如对于主副KV存储查询，每次查询都读到该数据最近一次的写入）执行时间开销大，通信开销大
- 弱一致性：（可能查询到该数据在不同副本中不同的值）执行开销小

**some tips of MapReduce**：

- 对于 map 函数的结果，让MapReduce框架以key（每个word）为单位将所有这些Map发出的键值对收集到一起
- 整个执行流程称为 **job** ，一次 **map** 或者 **reduce** 称为 **task**

- 输入文件存在 **GFS** ，会在空闲时期由 **master** 服务器分发给不同的 **workers** 服务器
- 当 **map** 调用 **emit** 时，会在调用此 **map** 的 **worker** 本地磁盘上生成文件，这些文件会不断追加该 **worker** 上运行 **map** 时生成的键和值。所以在 **map** 的最后阶段，我们会有所有 **worker** 机器上 **map** 函数所生成的输出
- 然后这些 **MapReduce workers** 会安排将数据移动到需要进行 **reduce** 操作的位置上。因此，在一个典型的大型计算中，**一个 reduce** 需要 **所有 map** 输出的结果（如key为a的键值对）

- 获取所有需要的数据后，调用 **reduce** ，最后将输出结果写入 **GFS** 的一个文件中
- **GFS** 实际上与运行 **MapReduce** 的 **worker** 是在同一服务器集上。如在一千台机器上运行着 **GFS** ，并且将 **MapReduce** 也运行在这一千台机器上，**master** 会将 **map** 任务拆分，然后分配给不同的 **worker** ，当需要对文件1执行 **map** 操作时，**master** 会从 **GFS** 上找出实际保存该输入文件1所在的服务器，然后在该服务器上执行 **map** 。
- **GFS** 会自动地将存储在它之上的任何大文件分割为很多 **64MB** 大小的数据块，并将它们分散存储在许多服务器上
- 所有 **map** 的计算结果最初是 **按行存储** （一行为一个map的计算结果）的，并且与运行 **map** 同在一个机器上，需要将这些 **按列存储**（一列为一个key）在一台机器上以供 **reduce** 使用。这个过程称为 **shuffle** 。













