# jetbrains-speed-boost
本仓库收录了 ​CLion、WebStorm、DataGrip、GoLand、PyCharm、IntelliJ IDEA​ 等主流 JetBrains IDE 的性能调优配置，专治卡顿、启动慢、编译久等问题 ->This repository collects performance optimization configurations for mainstream JetBrains IDEs, including ​CLion, WebStorm, DataGrip, GoLand, PyCharm, and IntelliJ IDEA​ — designed to fix slow startup, laggy UI, and long compilation times

# JetBrains 全家桶性能调优指南 🚀  
这里收录了 **CLion / WebStorm / DataGrip / GoLand / PyCharm / IDEA** 等 JetBrains IDE 的终极性能优化配置，包括：  
✅ JVM 虚拟机参数（堆内存/垃圾回收/渲染优化）  
✅ IDE 专属配置（文件编码/插件兼容/缓存策略）  
✅ 通用技巧（启动加速/卡顿排查/多项目管理）  

📌 每个工具的参数表均经过实测，附带详细注释和适用场景说明。目标是让你的 JetBrains 工具 **快如闪电，稳如老狗！**  

👉 当前已支持：PyCharm / IDEA / CLion（持续更新中...）

```diff
重要提醒
路径说明 → 正常版本在 PyCharm 2024.1.4\bin 目录下，魔法型路径自行查找（别覆盖原路径！）。

根据电脑配置调整 → 内存大小、处理器核数、并发线程数需适配你的硬件。

备份原文件 → 修改前务必复制 pycharm.vmoptions 以防翻车！
```

```diff
Important Reminder
Path Explanation → The normal version is located in the PyCharm 2024.1.4\bin directory. For the magic type path, please search for it yourself (do not overwrite the original path!) 。

Adjust according to computer configuration → The size of memory, the number of processor cores, and the number of concurrent threads need to be compatible with your hardware. 
Backup the original file → Before making any modifications, make sure to copy the pycharm.vmoptions file to prevent any errors!
```
>为了更直观,我把我的电脑淘汰硬件配置传上来给大家参考

>To make it more clear, I have uploaded the configuration of my old computer hardware here for everyone to refer to.

### **🖥️ My Computer Configuration (中英双语)**

| **配置项 (Category)**       | **中文详情 (Chinese Details)**                     | **English Details**                                |
|----------------------------|----------------------------------------------------|----------------------------------------------------|
| **电脑型号 (Model)**       | X64 兼容台式电脑 (X64-Compatible Desktop)           | X64-Compatible Desktop PC                          |
| **操作系统 (OS)**          | Windows 10 教育版 64位（Version 2004 / DirectX 12） | Windows 10 Education 64-bit (Version 2004 / DirectX 12) |
| **处理器 (CPU)**           | 英特尔 Xeon(至强) E5-2689 0 @ 2.60GHz 八核 (X2)     | Intel Xeon (Xeon) E5-2689 0 @ 2.60GHz, 8 cores (Dual CPUs) |
| **主板 (Motherboard)**     | Intel X79（X79 PCH -）                              | Intel X79 Chipset (X79 PCH)                        |
| **显卡 (GPU)**             | AMD Radeon RX590 GME (8 GB / AMD)                   | AMD Radeon RX590 GME, 8GB VRAM (AMD)               |
| **内存 (RAM)**             | 64 GB (镁光 DDR3L 1600MHz 16GB x 2)                 | 64GB (Micron DDR3L 1600MHz, 16GB × 2)              |
| **主硬盘 (Primary Storage)** | 七彩虹 CF600 512GB TLC (512 GB / 固态硬盘)          | Colorful CF600 512GB TLC SSD (512GB, NVMe/SSD)     |
| **显示器 (Monitor)**       | FFL0963 TCL Smart TV (52 英寸)                      | TCL Smart TV FFL0963, 52-inch                      |
| **声卡 (Sound Card)**      | 瑞昱 High Definition Audio                          | Realtek High Definition Audio                      |
| **网卡 (Network Card)**    | 瑞昱 RTL8168/8111/8112 Gigabit Ethernet Controller  | Realtek RTL8168/8111/8112 Gigabit Ethernet Controller |

>你的每一次反馈，都是让这份调优表更完美的动力～ 哪里有误的欢迎指出，我们一起努力解决问题！

>Every piece of your feedback is the driving force that makes this optimization table more perfect! If you find any mistakes, please point them out. Let's work together to solve the problems!

### **🔧 PyCharm 性能调优核心参数表（附简易注释）**

| **参数**                              | **值**                  | **作用说明**                                                                 |
|---------------------------------------|-------------------------|------------------------------------------------------------------------------|
| `-Xms2048m`                           | 初始堆内存 2GB          | PyCharm 启动时预分配 2GB 内存，避免运行时频繁申请内存卡顿  (数值太大容易芭比q)  |
| `-Xmx9216m`                           | 最大堆内存 9GB          | PyCharm 最多使用 9GB 内存，适合大型 Python 项目（如深度学习/多库依赖）        |
| `-XX:ReservedCodeCacheSize=2048m`     | 代码缓存 2GB            | 存储 JIT 编译后的热点代码，提升代码执行效率（项目越大建议值越高）(数值太大容易芭比q)   |
| `-XX:MaxDirectMemorySize=6G`          | 直接内存上限 6GB        | 控制堆外内存（如科学计算库用的 Native 内存）最大值，避免系统资源耗尽         |
| `-XX:+UseG2GC`                        | 使用 G2 垃圾回收器      | 低延迟垃圾回收（JDK 9+ 默认），平衡吞吐量与卡顿，适合 PyCharm 长时间运行     |
| `-XX:ParallelGCThreads=12`            | 并行 GC 线程数 12       | 垃圾回收时使用的并行线程数（建议为 CPU 核心数的 1~1.5 倍，如 8 核设 8~12）    |
| `-XX:ConcGCThreads=6`                 | 并发 GC 线程数 6        | G2 垃圾回收的并发阶段线程数（通常为 ParallelGCThreads 的一半）               |
| `-XX:+HeapDumpOnOutOfMemoryError`     | OOM 时生成堆转储文件    | 内存爆炸时自动保存快照，方便排查崩溃原因                                     |
| `-XX:HeapDumpPath=$USER_HOME/pycharm_error.hprof` | 堆转储文件路径       | OOM 时生成的内存快照保存位置（用户目录下，避免覆盖其他 IDE 的 dump 文件）    |
| `-XX:ErrorFile=$USER_HOME/java_error_in_pycharm_%p.log` | JVM 错误日志路径   | PyCharm 崩溃时的错误日志保存到用户目录（文件名含进程 ID，如 pycharm_1234.log）|
| `-Dfile.encoding=UTF-8`               | 文件编码 UTF-8          | 确保读写代码文件时用 UTF-8，避免中文/特殊字符乱码（必开！）                  |
| `-Dsun.jnu.encoding=UTF-8`            | 系统路径编码 UTF-8      | 解决 Windows 下中文目录（如 D:\项目\数据集）显示/操作乱码问题                |
| `-Dsun.java2d.renderer=sun.java2d.marlin.MarlinRenderingEngine` | 指定 Marlin 渲染引擎 | 优化高分辨率屏幕（如 4K 显示器）的图形绘制性能，替代默认渲染器               |
| `-Dsun.java2d.marlin.doChecks=false`  | 关闭渲染额外检查        | 提升界面绘制速度（稳定版 PyCharm 可关，开发版若遇渲染问题可设为 true）       |
| `-Dpython.console.encoding=UTF-8`     | Python 控制台编码 UTF-8 | 确保 PyCharm 内置 Python 控制台（如运行 Jupyter Notebook）支持中文输出       |
| **模块访问权限（解决插件兼容问题）** |                         |                                                                              |
| `--add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED` | 开放 ASM 字节码包访问 | 允许插件（如 Lombok、科学计算工具链）反射访问 JDK 内部的 ASM 库（解决“非法访问”报错） |
| `--add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED` | 开放 ASM 树结构包访问 | 支持插件对字节码树结构的反射操作（部分高级工具依赖此权限）                   |

### **🔧 PyCharm Performance Tuning Core Parameters (With Brief Notes)**

| **Parameter**                              | **Value**               | **Description**                                                                 |
|--------------------------------------------|-------------------------|---------------------------------------------------------------------------------|
| `-Xms2048m`                                | Initial heap memory 2GB | Pre-allocates 2GB memory on PyCharm startup to avoid runtime lag from frequent memory requests. (The numbers are too large and may cause a crash.)|
| `-Xmx9216m`                                | Max heap memory 9GB     | Sets the maximum memory PyCharm can use (suitable for large Python projects like deep learning/multi-library dependencies). |
| `-XX:ReservedCodeCacheSize=2048m`          | Code cache 2GB          | Stores JIT-compiled hotspot code to improve execution efficiency (higher values recommended for larger projects). (The numbers are too large and may cause a crash.)|
| `-XX:MaxDirectMemorySize=6G`               | Direct memory limit 6GB | Caps off-heap memory (e.g., Native memory used by scientific libraries) to prevent system resource exhaustion. |
| `-XX:+UseG2GC`                             | Use G2 Garbage Collector| Low-latency GC (default in JDK 9+), balancing throughput and pause time for long-running PyCharm sessions. |
| `-XX:ParallelGCThreads=12`                 | Parallel GC threads 12  | Number of threads for parallel garbage collection (recommended: 1~1.5x CPU cores, e.g., 8~12 for an 8-core CPU). |
| `-XX:ConcGCThreads=6`                      | Concurrent GC threads 6 | Threads for concurrent phase of G2 GC (typically half of ParallelGCThreads). |
| `-XX:+HeapDumpOnOutOfMemoryError`          | Generate heap dump on OOM | Automatically saves a memory snapshot when crashing to diagnose OOM issues. |
| `-XX:HeapDumpPath=$USER_HOME/pycharm_error.hprof` | Heap dump file path    | Saves OOM memory snapshots in the user directory (avoids overwriting dumps from other IDEs). |
| `-XX:ErrorFile=$USER_HOME/java_error_in_pycharm_%p.log` | JVM error log path     | Logs PyCharm crash errors to the user directory (filename includes process ID, e.g., pycharm_1234.log). |
| `-Dfile.encoding=UTF-8`                    | File encoding UTF-8     | Ensures UTF-8 encoding for reading/writing code files to avoid Chinese/special character corruption (must-enable!). |
| `-Dsun.jnu.encoding=UTF-8`                 | System path encoding UTF-8 | Resolves Chinese directory (e.g., D:\Projects\Datasets) display/operation issues on Windows. |
| `-Dsun.java2d.renderer=sun.java2d.marlin.MarlinRenderingEngine` | Marlin rendering engine | Optimizes graphics rendering for high-DPI screens (e.g., 4K monitors), replacing the default renderer. |
| `-Dsun.java2d.marlin.doChecks=false`       | Disable extra rendering checks | Boosts drawing speed (safe for stable PyCharm; set to `true` if rendering issues occur in dev builds). |
| `-Dpython.console.encoding=UTF-8`          | Python console encoding UTF-8 | Ensures Chinese output support in PyCharm's built-in Python console (e.g., Jupyter Notebook). |
| **Module Access (For Plugin Compatibility)** |                         |                                                                                 |
| `--add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED` | Open ASM bytecode package | Allows plugins (e.g., Lombok, scientific tools) to reflectively access JDK's internal ASM library (fixes "illegal access" errors). |
| `--add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED` | Open ASM tree structure package | Enables reflective operations on bytecode tree structures (required by advanced plugins). |
