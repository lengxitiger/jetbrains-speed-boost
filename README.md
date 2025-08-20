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
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
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

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
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

---------------------------------------------------------------------------------------------------------------------------------------------------------
### **🔧 IDEA 性能调优核心参数（附简要说明）**
| 类别               | 参数                                                              | 值                  | 作用解析                                                                 | 适用场景提示                                                                 |
|--------------------|-------------------------------------------------------------------|---------------------|--------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| **基础内存分配**   | `-Xms2048m`                                                       | 初始堆内存 2GB      | JVM 启动时分配的初始堆内存，避免运行时频繁申请内存导致卡顿               | 轻量级项目（如小型 Java 应用）可设 1024m；大型项目建议 ≥2048m     (设置再大容易芭比Q)          |
|                    | `-Xmx9216m`                                                       | 最大堆内存 9GB      | JVM 可使用的最大堆内存，直接影响 IDE 处理大型项目的编译/运行速度         | 根据物理内存调整（建议不超过机器总内存的 1/3~1/2，如 32G 内存机器可设 8~12G） |
|                    | `-XX:MaxDirectMemorySize=6G`                                      | 直接内存上限 6GB    | 控制 JVM 堆外内存（如 Netty 等框架使用的 Native 内存）的最大值           | 使用大量堆外内存工具（如大数据插件）时可适当调高；普通项目 4G 足够            |
|                    | `-XX:ReservedCodeCacheSize=2048m`                                 | 代码缓存 2GB        | 存储 JIT 编译后的热点代码，避免重复编译开销                              | 代码量大/频繁热部署项目（如 Spring Boot）建议 ≥2G；小项目 1G 可满足   (设置再大容易芭比Q)        |
| **垃圾回收与并行** | `-XX:+UseG1GC`                                                    | 使用 G1 垃圾回收器  | 低延迟 GC，平衡吞吐量与停顿时间，适合 IDE 频繁响应的场景                 | 比传统 CMS/Parallel GC 更适合 IDE，减少卡顿感                               |
|                    | `-XX:SoftRefLRUPolicyMSPerMB=1000`                                | 软引用缓存策略      | 控制软引用对象（如缓存）的存活时间（每 MB 堆内存保留 1000ms）            | 默认值可能过低，调高可减少缓存（如代码提示历史）重复加载                      |
|                    | `-XX:ParallelGCThreads=12`                                        | 并行 GC 线程数      | 垃圾回收时使用的并行线程数（建议为 CPU 物理核心数的 1~1.5 倍）           | 4 核 CPU 可设 4~6；8 核及以上建议 8~12                                      |
|                    | `-XX:ConcGCThreads=6`                                             | 并发 GC 线程数      | G1 垃圾回收的并发阶段线程数                                              | 通常设为 ParallelGCThreads 的一半（如 12 核对应 6）                         |
|                    | `-XX:CICompilerCount=14`                                          | JIT 编译器线程数    | 负责将热点代码编译为机器码的线程数，加速代码执行效率                     | 多核机器（如 8 核以上）可适当调高（默认 2~4），提升编译速度                   |
| **错误处理与调试** | `-XX:+HeapDumpOnOutOfMemoryError`                                 | OOM 堆转储          | IDE 内存耗尽崩溃时自动保存内存快照，便于分析内存泄漏问题                 | 必开！遇到卡死/崩溃时通过日志定位问题                                       |
|                    | `-XX:-OmitStackTraceInFastThrow`                                  | 禁用快速抛异常优化  | 避免 JVM 对重复异常（如空指针）省略堆栈信息，方便排查报错根源            | 开发阶段必开！否则部分报错可能只有简单提示，难以定位                          |
|                    | `-ea`                                                             | 启用断言检查        | 运行时开启 Java 断言，帮助发现代码逻辑问题（开发调试阶段有用）           | 生产环境可关闭，开发阶段建议开启                                            |
| **图形渲染**       | `-Dsun.java2d.d3d=false`                                          | 禁用 Direct3D 渲染  | 避免 Windows 系统下因 Direct3D 驱动问题导致的界面卡顿/花屏               | 若遇到 IDE 界面闪烁/渲染异常，优先关闭此选项                                |
|                    | `-Dsun.java2d.opengl=true`                                        | 启用 OpenGL 渲染    | 使用 OpenGL 加速图形绘制，提升界面流畅度（适合支持 OpenGL 的显卡）       | 显卡支持 OpenGL 的机器建议开启                                              |
|                    | `-Dsun.java2d.renderer=sun.java2d.marlin.MarlinRenderingEngine`     | Marlin 渲染引擎     | 替换默认渲染引擎，优化高分辨率屏幕的图形绘制性能                         | 高分辨率显示器（如 4K 屏）建议开启                                          |
|                    | `-Dsun.java2d.marlin.doChecks=false`                              | 关闭 Marlin 检查    | 减少渲染时的额外校验开销，提升绘制速度（牺牲少量安全性换性能）           | 稳定版 IDE 可关闭；开发版若遇渲染问题可设为 true                            |
| **兼容与编码**     | `-Djdk.http.auth.tunneling.disabledSchemes=""`                      | 允许 HTTP 隧道认证  | 支持通过代理访问外部资源（如 Maven 仓库）时的身份验证                     | 若遇到代理连接失败，检查是否被默认禁用                                      |
|                    | `-Djdk.attach.allowAttachSelf=true`                               | 允许 JVM 自身附加   | 支持 IDE 内部工具（如热部署插件）动态附加到当前 JVM 进程                 | 某些调试/热更新插件依赖此功能                                               |
|                    | `-Djdk.module.illegalAccess.silent=true`                            | 静默模块非法访问    | 兼容旧版库（如非模块化 jar 包）的反射调用，避免因 Java 模块化限制报错    | 使用旧版依赖（如传统工具包）时建议开启                                      |
|                    | `-Dfile.encoding=UTF-8`                                           | 文件编码 UTF-8      | 确保 IDE 读写文件时使用 UTF-8 编码，避免中文/特殊字符乱码                | 必开！尤其处理多语言项目时                                                  |
|                    | `-Dsun.jnu.encoding=UTF-8`                                        | 系统路径编码 UTF-8  | 解决文件路径（如 Windows 中文目录）显示/操作乱码问题                     | 中文系统用户建议开启                                                        |
| **其他优化**       | `-Didea.groovy.console.quick=true`                                | 加速 Groovy 控制台  | 提升内置 Groovy 控制台的响应速度（适合快速测试脚本）                     | 频繁使用 Groovy 脚本调试时建议开启                                          |
|                    | `-Dgroovy.antlr4=false`                                           | 禁用 ANTLR4 解析器  | 避免因 ANTLR4 库导致的解析性能问题（旧版 Groovy 插件兼容性优化）         | 遇到 Groovy 语法解析慢时可尝试关闭                                          |
|                    | `-Dgroovy.grape.enable=false`                                     | 禁用 Grape 依赖管理 | 避免 Groovy 自动下载依赖时的网络延迟（适合离线环境或稳定依赖场景）       | 不依赖动态加载 Groovy 库时建议关闭                                          |
|                    | `-noverify`                                                       | 跳过字节码验证      | 加速类加载过程（牺牲少量安全性，适合信任的代码环境）                     | 开发阶段对已知安全的代码可开启（提升启动速度）                              |
| **模块访问**       | `--add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED`  | 开放 ASM 包访问     | 允许插件（如 Lombok）通过反射访问 JDK 内部的 ASM 字节码操作库            | 若遇到 “Illegal access” 报错（如 Lombok 不生效），需添加此参数               |
|                    | `--add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED` | 开放 ASM 树结构包访问 | 支持对字节码树结构的反射操作（部分高级插件依赖）                         | 与上一条通常配套使用                                                        |

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
### **🔧 IDEA Performance Tuning Core Parameters (With Brief Notes)**

| **Category**               | **Parameter**                              | **Value**                  | **Description**                                                                 | **Use Case Tips**                                                                 |
|----------------------------|--------------------------------------------|----------------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| **Basic Memory Allocation** | `-Xms2048m`                                | Initial heap memory 2GB    | JVM startup allocated heap memory to avoid runtime lag from frequent requests. | Lightweight projects (e.g., small Java apps): 1024m; large projects: ≥2048m.  (Setting it too large is likely to cause a crash.)    |
|                            | `-Xmx9216m`                                | Max heap memory 9GB        | Maximum JVM heap memory, directly impacts compilation/runtime speed for large projects. | Adjust based on physical RAM (≤1/3~1/2 of total memory, e.g., 8~12G for 32G machines). |
|                            | `-XX:MaxDirectMemorySize=6G`               | Direct memory limit 6GB    | Caps off-heap memory (e.g., Native memory used by Netty) to prevent resource exhaustion. | Increase for tools with heavy off-heap usage (e.g., big data plugins); 4G for general use. |
|                            | `-XX:ReservedCodeCacheSize=2048m`          | Code cache 2GB             | Stores JIT-compiled hotspot code to avoid redundant compilation overhead.       | ≥2G for large/ frequently hot-deployed projects (e.g., Spring Boot); 1G for small projects. (Setting it too large is likely to cause a crash.) |
| **Garbage Collection & Parallelism** | `-XX:+UseG1GC`                             | Use G1 Garbage Collector   | Low-latency GC balancing throughput/pause time, ideal for IDE responsiveness. | Better than CMS/Parallel GC for reducing IDE lag.                                |
|                            | `-XX:SoftRefLRUPolicyMSPerMB=1000`         | Soft reference policy      | Keeps soft-referenced objects (e.g., caches) alive longer (1000ms/MB).          | Higher values reduce repeated loading of caches (e.g., code completion history). |
|                            | `-XX:ParallelGCThreads=12`                 | Parallel GC threads        | Threads for parallel GC (recommended: 1~1.5x CPU cores, e.g., 4~6 for 4-core, 8~12 for 8+). | Adjust based on CPU cores (e.g., 4~6 for 4-core, 8~12 for 8+ cores).             |
|                            | `-XX:ConcGCThreads=6`                      | Concurrent GC threads      | Threads for G1 concurrent phase (typically half of ParallelGCThreads).          | Usually set to half of ParallelGCThreads (e.g., 6 for 12-core).                  |
|                            | `-XX:CICompilerCount=14`                   | JIT compiler threads       | Threads compiling hotspot code to machine code, accelerating execution.         | Increase for multi-core machines (≥8 cores) to boost compilation speed.          |
| **Error Handling & Debugging** | `-XX:+HeapDumpOnOutOfMemoryError`          | OOM heap dump              | Auto-saves memory snapshot on crash to diagnose memory leaks.                 | Must-enable: Locate crash causes via logs when IDE freezes/crashes.              |
|                            | `-XX:-OmitStackTraceInFastThrow`           | Disable fast-throw optimization | Preserves full stack traces for repeated exceptions (e.g., NullPointerException). | Essential for debugging in development (avoids vague error messages).            |
|                            | `-ea`                                      | Enable assertions          | Runtime Java assertions to detect logic issues (useful for debugging).          | Disable in production; enable during development.                                |
| **Graphics Rendering**     | `-Dsun.java2d.d3d=false`                   | Disable Direct3D rendering | Avoids Windows rendering issues (e.g., flickering) caused by Direct3D drivers. | Prioritize disabling if IDE UI glitches occur.                                   |
|                            | `-Dsun.java2d.opengl=true`                 | Enable OpenGL rendering    | Accelerates UI drawing via OpenGL (recommended for OpenGL-supported GPUs).      | Enable for GPUs with OpenGL support to improve smoothness.                       |
|                            | `-Dsun.java2d.renderer=sun.java2d.marlin.MarlinRenderingEngine` | Marlin rendering engine  | Replaces default renderer, optimizing high-DPI screen performance.              | Enable for 4K/High-DPI displays.                                                 |
|                            | `-Dsun.java2d.marlin.doChecks=false`       | Disable Marlin checks      | Reduces rendering overhead (minor safety trade-off for speed).                  | Safe to disable on stable IDE; enable if rendering issues arise in dev builds.   |
| **Compatibility & Encoding** | `-Djdk.http.auth.tunneling.disabledSchemes=""` | Allow HTTP tunnel auth     | Enables authentication for proxy-accessed resources (e.g., Maven repos).        | Check if disabled if proxy connections fail.                                     |
|                            | `-Djdk.attach.allowAttachSelf=true`        | Allow JVM self-attach      | Permits IDE tools (e.g., hot-deploy plugins) to attach to the current JVM.      | Required by some debugging/hot-update plugins.                                   |
|                            | `-Djdk.module.illegalAccess.silent=true`   | Silent illegal access      | Allows reflection on legacy libraries (non-modular JARs) without Java module errors. | Enable when using older dependencies (e.g., traditional toolkits).               |
|                            | `-Dfile.encoding=UTF-8`                    | File encoding UTF-8        | Ensures UTF-8 for file I/O, preventing Chinese/special character corruption.    | Must-enable for multi-language projects.                                         |
|                            | `-Dsun.jnu.encoding=UTF-8`                 | System path encoding UTF-8 | Resolves Chinese/Unicode path (e.g., Windows) display/operation issues.         | Recommended for Chinese Windows systems.                                         |
| **Other Optimizations**    | `-Didea.groovy.console.quick=true`         | Accelerate Groovy console  | Speeds up built-in Groovy console (useful for quick script testing).            | Enable for frequent Groovy script debugging.                                     |
|                            | `-Dgroovy.antlr4=false`                    | Disable ANTLR4 parser      | Avoids parsing performance issues with ANTLR4 (legacy Groovy plugin fix).       | Try disabling if Groovy syntax parsing is slow.                                  |
|                            | `-Dgroovy.grape.enable=false`              | Disable Grape dependency management | Prevents network delays from auto-downloading Groovy dependencies. | Disable for offline environments or stable dependency setups.                    |
|                            | `-noverify`                                | Skip bytecode verification | Accelerates class loading (minor security trade-off for trusted code).          | Enable during development for faster startup of trusted code.                    |
| **Module Access**          | `--add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED` | Open ASM bytecode package | Allows plugins (e.g., Lombok) to reflectively access JDK's internal ASM library. | Fix "Illegal access" errors (e.g., Lombok not working).                          |
|                            | `--add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED` | Open ASM tree package    | Enables reflection on bytecode tree structures (required by advanced plugins).  | Typically used with the above ASM bytecode access.                               |


-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
### **🔧 DataGrip 性能调优核心参数表（附简易注释）**

#### **基础内存分配**
| **参数**               | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-Xms4096m`            | 初始堆内存 4GB   | JVM 启动时预分配的初始堆内存，避免运行时频繁申请内存导致卡顿                     | 中等数据量项目（如多表关联查询）建议 4GB；大数据量项目（如百万级结果集）建议 ≥6GB  |
| `-Xmx8192m`            | 最大堆内存 8GB   | JVM 可使用的最大堆内存，直接影响 DataGrip 处理复杂查询和多连接的性能             | 物理内存 ≥16GB 的机器可设 8~12GB；8GB 内存机器建议 ≤6GB                          |
| `-XX:ReservedCodeCacheSize=2048m` | 代码缓存 2GB   | 存储 JIT 编译后的热点代码，避免重复编译开销                                      | 代码提示频繁的项目（如 SQL 脚本开发）建议 ≥2GB；小项目 1GB 可满足               |
| `-XX:MaxDirectMemorySize=2G`   | 直接内存上限 2GB | 控制 JVM 堆外内存（如数据库驱动 Native 库）的最大值，防止系统资源耗尽          | 常规数据库连接（如 MySQL/PostgreSQL）2GB 足够；超多连接可适当调高               |

#### **垃圾回收与并行**
| **参数**               | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-XX:+UseG1GC`         | 使用 G1 垃圾回收器 | 低延迟垃圾回收，平衡吞吐量与停顿时间，适合 DataGrip 长时间运行                   | 比传统 GC 更适合 IDE，减少卡顿感                                                 |
| `-XX:ParallelGCThreads=10`     | 并行 GC 线程数 10 | 垃圾回收时使用的并行线程数（建议为 CPU 物理核心数的 1~1.5 倍）                 | 8 核 CPU 可设 8~10；12 核及以上建议 10~12                                        |
| `-XX:ConcGCThreads=6`          | 并发 GC 线程数 6  | G1 垃圾回收的并发阶段线程数                                                      | 通常设为 ParallelGCThreads 的一半（如 10 核对应 6）                              |
| `-XX:MaxGCPauseMillis=200`     | 最大 GC 停顿时间 200ms | 控制垃圾回收时最大暂停时间，提升交互响应流畅度                                 | 值越低交互越流畅，但可能增加 GC 频率（200ms 是平衡点）                          |
| `-XX:SoftRefLRUPolicyMSPerMB=50` | 软引用缓存策略 50ms/MB | 控制软引用对象（如缓存）的存活时间（每 MB 堆内存保留 50ms）                    | 提高常用查询结果的缓存命中率，减少重复计算                                       |

#### **数据库专项优化**
| **参数**                     | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-Ddatagrip.query.result.set.cache.size=50000` | 查询结果集缓存 5 万行 | 缓存常用查询结果，加速重复查询响应（如调试时反复执行的 SQL）                   | 结果集较大的查询（如多表 JOIN）建议调高；内存有限可降低至 3 万行               |
| `-Ddatagrip.jdbc.max.pool.size=24` | JDBC 连接池大小 24  | 数据库连接池最大连接数，避免高并发查询时连接耗尽                               | 并发查询多（如同时开 10+ 标签页）建议 ≥20；常规开发 16~24 足够                 |
| `-Ddatagrip.schema.cache.size=1024m` | 数据库 Schema 缓存 1MB | 缓存数据库表结构信息，加速元数据加载（如字段/索引提示）                        | 多数据库/多 Schema 项目（如微服务架构）建议 ≥1GB；简单项目 512MB 可用          |

#### **编码与渲染**
| **参数**               | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-Dfile.encoding=UTF-8`            | 文件编码 UTF-8   | 确保读写 SQL 文件时用 UTF-8，避免中文/特殊字符乱码（必开！）                  | 处理多语言 SQL 脚本（如中文注释）时必须开启                                     |
| `-Dsun.jnu.encoding=UTF-8`         | 系统路径编码 UTF-8 | 解决 Windows 下中文目录（如 `D:\数据\数据库`）显示/操作乱码问题                | 中文系统用户建议开启                                                             |
| `-Dsun.java2d.renderer=sun.java2d.marlin.MarlinRenderingEngine` | Marlin 渲染引擎 | 优化高分辨率屏幕（如 4K 显示器）的图形绘制性能，替代默认渲染器                   | 高分辨率显示器（如 4K 屏）建议开启                                               |
| `-Dsun.java2d.marlin.doChecks=false` | 关闭渲染额外检查  | 提升界面绘制速度（稳定版 DataGrip 可关，开发版若遇渲染问题可设为 true）         | 稳定版 IDE 可关闭；开发版若遇界面卡顿可设为 true                                 |
| `-Dsun.io.useCanonCaches=false`    | 禁用规范路径缓存  | 避免文件路径解析缓存导致的异常（如网络映射盘符变动）                           | 使用网络存储（如 NAS/SMB）或虚拟机共享目录时建议关闭                           |

#### **调试与内存保护**
| **参数**               | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-ea`                  | 启用断言检查     | 运行时开启 Java 断言，帮助发现代码逻辑问题（开发调试阶段有用）                 | 生产环境可关闭，开发阶段建议开启                                                |
| `-XX:+HeapDumpOnOutOfMemoryError`  | OOM 时生成堆转储文件 | 内存爆炸时自动保存快照，方便排查崩溃原因                                     | 必开！遇到卡死/崩溃时通过日志定位问题                                           |
| `-XX:HeapDumpPath=$USER_HOME/datagrip_oom.hprof` | 堆转储文件路径   | OOM 快照保存位置（用户目录下，避免覆盖其他 IDE 的 dump 文件）                | 自定义路径便于管理多个 IDE 的崩溃日志                                           |
| `-XX:ErrorFile=$USER_HOME/datagrip_error_%p.log` | JVM 错误日志路径 | DataGrip 崩溃时的错误日志保存到用户目录（文件名含进程 ID，如 datagrip_1234.log） | 方便排查 IDE 崩溃的底层原因                                                     |

#### **模块访问（兼容性）**
| **参数**                                 | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `--add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED` | 开放 ASM 字节码包访问 | 允许插件反射访问 JDK 内部的 ASM 库，解决“非法访问”报错                       | 若遇到插件（如数据库工具链插件）不生效，检查是否缺少此参数                       |
| `--add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED` | 开放 ASM 树结构包访问 | 支持插件对字节码树结构的反射操作（部分高级工具依赖此权限）                   | 通常与上一条参数配套使用                                                        |

#### **JVM 附加权限**
| **参数**                                 | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-Djdk.attach.allowAttachSelf=true`      | 允许 JVM 自身附加  | 支持 IDE 内部工具（如热部署插件）动态附加到当前 JVM 进程                       | 某些调试/热更新插件依赖此功能                                                   |
| `-Djdk.module.illegalAccess.silent=true` | 静默模块非法访问   | 兼容旧版库（如非模块化 jar 包）的反射调用，避免因 Java 模块化限制报错          | 使用旧版依赖（如传统工具包）时建议开启                                          |


-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
### **🔧 DataGrip 性能调优核心参数表（附简易注释）**

#### **基础内存分配**
| **参数**               | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-Xms4096m`            | 初始堆内存 4GB   | JVM 启动时预分配的初始堆内存，避免运行时频繁申请内存导致卡顿                     | 中等数据量项目（如多表关联查询）建议 4GB；大数据量项目（如百万级结果集）建议 ≥6GB  |
| `-Xmx8192m`            | 最大堆内存 8GB   | JVM 可使用的最大堆内存，直接影响 DataGrip 处理复杂查询和多连接的性能             | 物理内存 ≥16GB 的机器可设 8~12GB；8GB 内存机器建议 ≤6GB                          |
| `-XX:ReservedCodeCacheSize=2048m` | 代码缓存 2GB   | 存储 JIT 编译后的热点代码，避免重复编译开销                                      | 代码提示频繁的项目（如 SQL 脚本开发）建议 ≥2GB；小项目 1GB 可满足               |
| `-XX:MaxDirectMemorySize=2G`   | 直接内存上限 2GB | 控制 JVM 堆外内存（如数据库驱动 Native 库）的最大值，防止系统资源耗尽          | 常规数据库连接（如 MySQL/PostgreSQL）2GB 足够；超多连接可适当调高               |

#### **垃圾回收与并行**
| **参数**               | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-XX:+UseG1GC`         | 使用 G1 垃圾回收器 | 低延迟垃圾回收，平衡吞吐量与停顿时间，适合 DataGrip 长时间运行                   | 比传统 GC 更适合 IDE，减少卡顿感                                                 |
| `-XX:ParallelGCThreads=10`     | 并行 GC 线程数 10 | 垃圾回收时使用的并行线程数（建议为 CPU 物理核心数的 1~1.5 倍）                 | 8 核 CPU 可设 8~10；12 核及以上建议 10~12                                        |
| `-XX:ConcGCThreads=6`          | 并发 GC 线程数 6  | G1 垃圾回收的并发阶段线程数                                                      | 通常设为 ParallelGCThreads 的一半（如 10 核对应 6）                              |
| `-XX:MaxGCPauseMillis=200`     | 最大 GC 停顿时间 200ms | 控制垃圾回收时最大暂停时间，提升交互响应流畅度                                 | 值越低交互越流畅，但可能增加 GC 频率（200ms 是平衡点）                          |
| `-XX:SoftRefLRUPolicyMSPerMB=50` | 软引用缓存策略 50ms/MB | 控制软引用对象（如缓存）的存活时间（每 MB 堆内存保留 50ms）                    | 提高常用查询结果的缓存命中率，减少重复计算                                       |

#### **数据库专项优化**
| **参数**                     | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-Ddatagrip.query.result.set.cache.size=50000` | 查询结果集缓存 5 万行 | 缓存常用查询结果，加速重复查询响应（如调试时反复执行的 SQL）                   | 结果集较大的查询（如多表 JOIN）建议调高；内存有限可降低至 3 万行               |
| `-Ddatagrip.jdbc.max.pool.size=24` | JDBC 连接池大小 24  | 数据库连接池最大连接数，避免高并发查询时连接耗尽                               | 并发查询多（如同时开 10+ 标签页）建议 ≥20；常规开发 16~24 足够                 |
| `-Ddatagrip.schema.cache.size=1024m` | 数据库 Schema 缓存 1MB | 缓存数据库表结构信息，加速元数据加载（如字段/索引提示）                        | 多数据库/多 Schema 项目（如微服务架构）建议 ≥1GB；简单项目 512MB 可用          |

#### **编码与渲染**
| **参数**               | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-Dfile.encoding=UTF-8`            | 文件编码 UTF-8   | 确保读写 SQL 文件时用 UTF-8，避免中文/特殊字符乱码（必开！）                  | 处理多语言 SQL 脚本（如中文注释）时必须开启                                     |
| `-Dsun.jnu.encoding=UTF-8`         | 系统路径编码 UTF-8 | 解决 Windows 下中文目录（如 `D:\数据\数据库`）显示/操作乱码问题                | 中文系统用户建议开启                                                             |
| `-Dsun.java2d.renderer=sun.java2d.marlin.MarlinRenderingEngine` | Marlin 渲染引擎 | 优化高分辨率屏幕（如 4K 显示器）的图形绘制性能，替代默认渲染器                   | 高分辨率显示器（如 4K 屏）建议开启                                               |
| `-Dsun.java2d.marlin.doChecks=false` | 关闭渲染额外检查  | 提升界面绘制速度（稳定版 DataGrip 可关，开发版若遇渲染问题可设为 true）         | 稳定版 IDE 可关闭；开发版若遇界面卡顿可设为 true                                 |
| `-Dsun.io.useCanonCaches=false`    | 禁用规范路径缓存  | 避免文件路径解析缓存导致的异常（如网络映射盘符变动）                           | 使用网络存储（如 NAS/SMB）或虚拟机共享目录时建议关闭                           |

#### **调试与内存保护**
| **参数**               | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-ea`                  | 启用断言检查     | 运行时开启 Java 断言，帮助发现代码逻辑问题（开发调试阶段有用）                 | 生产环境可关闭，开发阶段建议开启                                                |
| `-XX:+HeapDumpOnOutOfMemoryError`  | OOM 时生成堆转储文件 | 内存爆炸时自动保存快照，方便排查崩溃原因                                     | 必开！遇到卡死/崩溃时通过日志定位问题                                           |
| `-XX:HeapDumpPath=$USER_HOME/datagrip_oom.hprof` | 堆转储文件路径   | OOM 快照保存位置（用户目录下，避免覆盖其他 IDE 的 dump 文件）                | 自定义路径便于管理多个 IDE 的崩溃日志                                           |
| `-XX:ErrorFile=$USER_HOME/datagrip_error_%p.log` | JVM 错误日志路径 | DataGrip 崩溃时的错误日志保存到用户目录（文件名含进程 ID，如 datagrip_1234.log） | 方便排查 IDE 崩溃的底层原因                                                     |

#### **模块访问（兼容性）**
| **参数**                                 | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `--add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED` | 开放 ASM 字节码包访问 | 允许插件反射访问 JDK 内部的 ASM 库，解决“非法访问”报错                       | 若遇到插件（如数据库工具链插件）不生效，检查是否缺少此参数                       |
| `--add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED` | 开放 ASM 树结构包访问 | 支持插件对字节码树结构的反射操作（部分高级工具依赖此权限）                   | 通常与上一条参数配套使用                                                        |

#### **JVM 附加权限**
| **参数**                                 | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-Djdk.attach.allowAttachSelf=true`      | 允许 JVM 自身附加  | 支持 IDE 内部工具（如热部署插件）动态附加到当前 JVM 进程                       | 某些调试/热更新插件依赖此功能                                                   |
| `-Djdk.module.illegalAccess.silent=true` | 静默模块非法访问   | 兼容旧版库（如非模块化 jar 包）的反射调用，避免因 Java 模块化限制报错          | 使用旧版依赖（如传统工具包）时建议开启                                          |

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
### **🔧 GoLand 性能调优核心参数表（附简易注释）**

#### **基础内存分配**
| **参数**               | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-Xms2048m`            | 初始堆内存 2GB   | JVM 启动时预分配的初始堆内存，避免运行时频繁申请内存导致卡顿                     | 轻量级项目可设 1024m；大型项目（如微服务）建议 ≥2048m                            |
| `-Xmx9216m`            | 最大堆内存 9GB   | JVM 可使用的最大堆内存，直接影响 GoLand 处理大型项目的编译/运行速度            | 根据物理内存调整（建议不超过机器总内存的 1/3~1/2，如 32G 机器可设 8~12G）         |
| `-XX:ReservedCodeCacheSize=2048m` | 代码缓存 2GB   | 存储 JIT 编译后的热点代码，避免重复编译开销                                      | 代码量大/频繁热部署项目建议 ≥2G；小项目 1G 可满足                                 |
| `-XX:MaxDirectMemorySize=6G`   | 直接内存上限 6GB | 控制 JVM 堆外内存（如 Go 工具链 Native 库）的最大值，防止系统资源耗尽          | 使用大量堆外内存工具（如 gRPC 插件）时可适当调高；普通项目 4G 足够               |
| `-XX:+UseG1GC`         | 使用 G1 垃圾回收器 | 低延迟垃圾回收，平衡吞吐量与停顿时间，适合 GoLand 频繁响应的场景               | 比传统 GC 更适合 IDE，减少卡顿感                                                 |
| `-XX:ParallelGCThreads=10`     | 并行 GC 线程数 10 | 垃圾回收时使用的并行线程数（建议为 CPU 物理核心数的 1~1.5 倍）                 | 8 核 CPU 可设 8~10；12 核及以上建议 10~12                                        |
| `-XX:ConcGCThreads=6`          | 并发 GC 线程数 6  | G1 垃圾回收的并发阶段线程数                                                      | 通常设为 ParallelGCThreads 的一半（如 10 核对应 6）                              |

#### **Go 专属优化**
| **参数**                     | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-Dgo.gopls.heap.size=6144m`   | Go 语言服务器内存 6GB | 为 Go 语言服务器（gopls）分配的专用内存，加速代码分析/补全                   | 大型 Go 项目（如微服务/高并发）必备；普通项目可适当降低                         |
| `-Dgo.dlv.heap.size=4096m`     | 调试器内存 4GB    | 调试器（Delve）的堆内存，支持复杂断点/变量监控                               | 调试复杂 Go 应用（如多协程）时建议 ≥4G；简单项目 2G 可用                        |
| `-Dgo.modules.index.parallelism=8` | 并行索引线程数 8  | 加速 Go Modules 依赖索引（8 线程并行），提升项目加载速度                     | 依赖多的项目（如含大量第三方库）建议 ≥8 线程                                     |

#### **编码与通用设置**
| **参数**               | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-Dfile.encoding=UTF-8`            | 文件编码 UTF-8   | 确保读写代码文件时用 UTF-8，避免中文/特殊字符乱码（必开！）                  | 处理多语言项目（如中英文混合代码）时必须开启                                     |
| `-Dsun.jnu.encoding=UTF-8`         | 系统路径编码 UTF-8 | 解决 Windows 下中文目录（如 `D:\项目\Go代码`）显示/操作乱码问题              | 中文系统用户建议开启                                                             |
| `-XX:+HeapDumpOnOutOfMemoryError`  | OOM 时生成堆转储文件 | 内存爆炸时自动保存快照，方便排查崩溃原因                                     | 必开！遇到卡死/崩溃时通过日志定位问题                                           |
| `-XX:HeapDumpPath=$USER_HOME/goland_oom.hprof` | 堆转储文件路径   | OOM 快照保存位置（用户目录下，避免覆盖其他 IDE 的 dump 文件）                | 自定义路径便于管理多个 IDE 的崩溃日志                                           |

#### **模块访问（兼容性）**
| **参数**                                 | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `--add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED` | 开放 ASM 字节码包访问 | 允许插件反射访问 JDK 内部的 ASM 库，解决“非法访问”报错                       | 若遇到插件（如 Lombok）不生效，检查是否缺少此参数                               |
| `--add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED` | 开放 ASM 树结构包访问 | 支持插件对字节码树结构的反射操作（部分高级工具依赖此权限）                   | 通常与上一条参数配套使用                                                        |   

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
### **🔧 GoLand Performance Tuning Core Parameters (With Brief Notes)**

#### **Basic Memory Allocation**
| **Parameter**               | **Value**           | **Description**                                                                 | **Use Case Tips**                                                                 |
|-----------------------------|---------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| `-Xms2048m`                 | Initial heap 2GB    | JVM startup allocated heap to avoid runtime lag from frequent memory requests.  | Lightweight projects: 1024m; large projects (e.g., microservices): ≥2048m.       |
| `-Xmx9216m`                 | Max heap 9GB        | Maximum JVM heap memory, directly impacts GoLand's compilation/runtime speed.   | Adjust based on physical RAM (≤1/3~1/2 of total, e.g., 8~12G for 32G machines).   |
| `-XX:ReservedCodeCacheSize=2048m` | Code cache 2GB     | Stores JIT-compiled hotspot code to reduce redundant compilation overhead.      | ≥2G for large/TS-heavy projects; 1G for small projects.                          |
| `-XX:MaxDirectMemorySize=6G` | Direct memory 6GB   | Caps off-heap memory (e.g., Go toolchain Native libs) to prevent exhaustion.    | Increase for gRPC plugins; 4G for general use.                                   |
| `-XX:+UseG1GC`              | Use G1 GC           | Low-latency GC balancing throughput/pause time, ideal for IDE responsiveness.   | Better than CMS/Parallel GC for reducing lag.                                    |
| `-XX:ParallelGCThreads=10`  | Parallel GC threads | Threads for parallel GC (recommended: 1~1.5x CPU cores, e.g., 8~10 for 8-core). | Adjust based on CPU cores (e.g., 8~10 for 8-core CPU).                           |
| `-XX:ConcGCThreads=6`       | Concurrent GC threads | Threads for G1 concurrent phase (typically half of ParallelGCThreads).          | Usually set to half of ParallelGCThreads (e.g., 6 for 10-core).                  |

#### **Go-Specific Optimizations**
| **Parameter**                     | **Value**           | **Description**                                                                 | **Use Case Tips**                                                                 |
|-----------------------------------|---------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| `-Dgo.gopls.heap.size=6144m`      | Go server memory 6GB | Dedicated heap for Go language server (gopls) to speed up analysis/completion.  | Essential for large Go projects (e.g., microservices); adjust for smaller ones.   |
| `-Dgo.dlv.heap.size=4096m`        | Debugger memory 4GB | Heap for debugger (Delve) to handle complex breakpoints/variables.              | ≥4G for multi-threaded debugging; 2G for simple projects.                        |
| `-Dgo.modules.index.parallelism=8` | Parallel indexing 8 | 8 threads for indexing Go Modules dependencies, faster project load.            | ≥8 threads for projects with many third-party libs.                              |

#### **Encoding & General Settings**
| **Parameter**               | **Value**           | **Description**                                                                 | **Use Case Tips**                                                                 |
|-----------------------------|---------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| `-Dfile.encoding=UTF-8`         | File encoding UTF-8 | Ensures UTF-8 for file I/O, prevents Chinese/special character corruption.      | Must-enable for multi-language projects.                                         |
| `-Dsun.jnu.encoding=UTF-8`      | Path encoding UTF-8 | Resolves Unicode path (e.g., Windows) display/operation issues.                 | Recommended for non-English Windows systems.                                     |
| `-XX:+HeapDumpOnOutOfMemoryError` | OOM heap dump       | Auto-saves memory snapshot on crash to diagnose memory leaks.                   | Must-enable: Locate OOM causes via logs when GoLand freezes.                     |
| `-XX:HeapDumpPath=$USER_HOME/goland_oom.hprof` | OOM dump path    | Saves OOM snapshots to user directory (avoids overwriting other IDE dumps).     | Custom path for easy debugging.                                                  |

#### **Module Access (Compatibility)**
| **Parameter**                                 | **Value**           | **Description**                                                                 | **Use Case Tips**                                                                 |
|-----------------------------------------------|---------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| `--add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED` | Open ASM bytecode   | Allows plugins to reflectively access JDK's internal ASM library.               | Fix "illegal access" errors (e.g., plugins not working).                         |
| `--add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED` | Open ASM tree      | Enables reflection on bytecode tree structures (required by advanced plugins).  | Typically used with the above ASM bytecode access.                               |

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### **🔧 WebStorm 性能调优核心参数表（附简易注释）**

| **类别**               | **参数**                              | **值**                  | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------|---------------------------------------|-------------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| **基础内存分配**       | `-Xms2048m`                           | 初始堆内存 2GB          | JVM 启动时预分配的初始堆内存，避免运行时频繁申请内存导致卡顿                     | 轻量级项目（如小型前端应用）可设 1024m；大型项目（如 Monorepo）建议 ≥2048m       |
|                        | `-Xmx9216m`                           | 最大堆内存 9GB          | JVM 可使用的最大堆内存，直接影响 WebStorm 处理大型项目的编译/运行速度            | 根据物理内存调整（建议不超过机器总内存的 1/3~1/2，如 32G 内存机器可设 8~12G）     |
|                        | `-XX:ReservedCodeCacheSize=2048m`     | 代码缓存 2GB            | 存储 JIT 编译后的热点代码，避免重复编译开销                                      | 代码量大/频繁热部署项目（如 Vue/React 全栈项目）建议 ≥2G；小项目 1G 可满足       |
|                        | `-XX:MaxDirectMemorySize=6G`          | 直接内存上限 6GB        | 控制 JVM 堆外内存（如 Node.js 原生模块使用的 Native 内存）的最大值               | 使用大量堆外内存工具（如 Webpack 5/Babel 插件）时可适当调高；普通项目 4G 足够    |
|                        | `-XX:+UseG1GC`                        | 使用 G1 垃圾回收器      | 低延迟垃圾回收，平衡吞吐量与停顿时间，适合 WebStorm 频繁响应的场景               | 比传统 CMS/Parallel GC 更适合 IDE，减少卡顿感                                     |
|                        | `-XX:ParallelGCThreads=10`            | 并行 GC 线程数 10       | 垃圾回收时使用的并行线程数（建议为 CPU 物理核心数的 1~1.5 倍）                   | 8 核 CPU 可设 8~10；12 核及以上建议 10~12                                        |
|                        | `-XX:ConcGCThreads=6`                 | 并发 GC 线程数 6        | G1 垃圾回收的并发阶段线程数                                                      | 通常设为 ParallelGCThreads 的一半（如 10 核对应 6）                              |
| **编码与渲染**         | `-Dfile.encoding=UTF-8`               | 文件编码 UTF-8          | 确保读写代码文件时用 UTF-8，避免中文/特殊字符乱码（必开！）                      | 处理多语言项目（如中英文混合代码）时必须开启                                     |
|                        | `-Dsun.jnu.encoding=UTF-8`            | 系统路径编码 UTF-8      | 解决 Windows 下中文目录（如 D:\项目\前端代码）显示/操作乱码问题                  | 中文系统用户建议开启                                                             |
|                        | `-Dsun.java2d.renderer=sun.java2d.marlin.MarlinRenderingEngine` | Marlin 渲染引擎       | 优化高分辨率屏幕（如 4K 显示器）的图形绘制性能，替代默认渲染器                   | 高分辨率显示器（如 4K 屏）建议开启                                               |
| **前端专属优化**       | `-Dnodejs.heap.size=8192m`            | Node.js 堆内存 8GB      | 为 Node.js 进程（如 Webpack/Vite）分配的专用堆内存，解决大型项目内存不足问题     | 大型 Node.js 项目（如 Next.js/Vue 3 全栈）必备；普通项目可适当降低               |
|                        | `-Dts.ls.compiler.heap.size=4096m`    | TypeScript 语言服务堆 4GB | 为 TypeScript 编译器（LS）分配更多内存，加速代码提示和类型检查                   | 大型 TypeScript 项目（如含大量 TSX 文件）建议 ≥4G；小项目 2G 可用              |
|                        | `-Didea.filewatcher.executable=true`  | 启用文件监听器          | 确保文件监听器正常工作，支持实时编译（如 SCSS/LESS/Vue 单文件组件）              | 使用 Vue/React 等需要实时编译的前端框架时必须开启                               |
| **调试与内存保护**     | `-XX:+HeapDumpOnOutOfMemoryError`     | OOM 时生成堆转储文件    | 内存爆炸时自动保存快照，方便排查崩溃原因                                         | 必开！遇到卡死/崩溃时通过日志定位问题                                           |
|                        | `-XX:HeapDumpPath=$USER_HOME/webstorm_oom.hprof` | 堆转储文件路径       | OOM 时生成的内存快照保存位置（用户目录下，避免覆盖其他 IDE 的 dump 文件）        | 自定义路径便于管理多个 IDE 的崩溃日志                                           |
|                        | `-ea`                                 | 启用断言检查            | 运行时开启 Java 断言，帮助发现代码逻辑问题（开发调试阶段有用）                   | 生产环境可关闭，开发阶段建议开启                                                |
| **模块访问（兼容性）** | `--add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED` | 开放 ASM 字节码包访问   | 允许插件（如 Lombok）反射访问 JDK 内部的 ASM 库，解决“非法访问”报错              | 若遇到插件（如 Lombok）不生效，检查是否缺少此参数                               |
|                        | `--add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED` | 开放 ASM 树结构包访问 | 支持插件对字节码树结构的反射操作（部分高级工具依赖此权限）                       | 通常与上一条参数配套使用                                                        |

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
### **🔧 WebStorm Performance Tuning Core Parameters (With Brief Notes)**

| **Category**               | **Parameter**                              | **Value**                  | **Description**                                                                 | **Use Case Tips**                                                                 |
|----------------------------|--------------------------------------------|----------------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| **Basic Memory Allocation** | `-Xms2048m`                                | Initial heap memory 2GB    | JVM startup allocated heap memory to avoid runtime lag from frequent requests. | Lightweight projects: 1024m; large projects (e.g., monorepos): ≥2048m.           |
|                            | `-Xmx9216m`                                | Max heap memory 9GB        | Maximum JVM heap memory, directly impacts compilation/runtime speed.            | Adjust based on physical RAM (≤1/3~1/2 of total memory, e.g., 8~12G for 32G machines). |
|                            | `-XX:ReservedCodeCacheSize=2048m`          | Code cache 2GB             | Stores JIT-compiled hotspot code to avoid redundant compilation overhead.       | ≥2G for large/TS-heavy projects; 1G for small projects.                          |
|                            | `-XX:MaxDirectMemorySize=6G`               | Direct memory limit 6GB    | Caps off-heap memory (e.g., Node.js native modules) to prevent resource exhaustion. | Increase for Node.js-heavy projects; 4G for general use.                         |
|                            | `-XX:+UseG1GC`                             | Use G1 Garbage Collector   | Low-latency GC balancing throughput/pause time, ideal for IDE responsiveness.   | Better than CMS/Parallel GC for reducing IDE lag.                                |
|                            | `-XX:ParallelGCThreads=10`                 | Parallel GC threads        | Threads for parallel GC (recommended: 1~1.5x CPU cores, e.g., 8~10 for 8-core). | Adjust based on CPU cores (e.g., 8~10 for 8-core CPU).                           |
|                            | `-XX:ConcGCThreads=6`                      | Concurrent GC threads      | Threads for G1 concurrent phase (typically half of ParallelGCThreads).          | Usually set to half of ParallelGCThreads (e.g., 6 for 10-core).                  |
| **Encoding & Rendering**    | `-Dfile.encoding=UTF-8`                    | File encoding UTF-8        | Ensures UTF-8 for file I/O, preventing Chinese/special character corruption.    | Must-enable for multi-language projects.                                         |
|                            | `-Dsun.jnu.encoding=UTF-8`                 | System path encoding UTF-8 | Resolves Unicode path (e.g., Windows) display/operation issues.                 | Recommended for non-English Windows systems.                                     |
|                            | `-Dsun.java2d.renderer=sun.java2d.marlin.MarlinRenderingEngine` | Marlin rendering engine  | Optimizes high-DPI screen drawing performance, replacing default renderer.      | Enable for 4K/Retina displays.                                                   |
| **Frontend-Specific**       | `-Dnodejs.heap.size=8192m`                 | Node.js heap 8GB           | Dedicated heap for Node.js processes (large projects like Next.js/Vue 3).       | Essential for monorepos or heavy Node.js tooling (e.g., Webpack 5).              |
|                            | `-Dts.ls.compiler.heap.size=4096m`         | TypeScript LS heap 4GB     | Allocates more memory for TypeScript language server (faster intellisense).     | Improves TS/JS code completion speed in large codebases.                         |
|                            | `-Didea.filewatcher.executable=true`       | Enable file watcher        | Ensures real-time file sync (e.g., for SCSS/LESS compilation).                  | Required for frontend build tools (e.g., Vite, Webpack).                         |
| **Debugging & Protection**  | `-XX:+HeapDumpOnOutOfMemoryError`          | OOM heap dump              | Auto-saves memory snapshot on crash to diagnose memory leaks.                   | Must-enable: Locate OOM causes via logs when WebStorm freezes.                   |
|                            | `-XX:HeapDumpPath=$USER_HOME/webstorm_oom.hprof` | OOM dump path              | Saves OOM snapshots to user directory (avoids overwriting other IDE dumps).     | Custom path for easy debugging.                                                  |
|                            | `-ea`                                      | Enable assertions          | Runtime Java assertions to detect logic issues (useful for debugging).          | Disable in production; enable during development.                                |
| **Module Access**           | `--add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED` | Open ASM bytecode package | Allows plugins (e.g., Lombok) to access JDK's internal ASM library.             | Fix "Illegal access" errors (e.g., plugins not working).                         |
|                            | `--add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED` | Open ASM tree package      | Enables reflection on bytecode tree structures (required by advanced plugins).  | Typically used with the above ASM bytecode access.                               |

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
### **🔧 CLion 性能调优核心参数表（附简易注释）**

#### **基础内存分配**
| **参数**               | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-Xms1024m`            | 初始堆内存 1GB   | JVM 启动时预分配的初始堆内存，避免运行时频繁申请内存导致卡顿                     | 轻量级项目（如小型 C/C++ 程序）可设 512m；中大型项目建议 ≥1GB                    |
| `-Xmx4096m`            | 最大堆内存 4GB   | JVM 可使用的最大堆内存，直接影响 CLion 处理大型项目的编译/运行速度             | 物理内存 ≥8GB 的机器可设 4~6GB；4GB 内存机器建议 ≤3GB                           |
| `-XX:ReservedCodeCacheSize=1024m` | 代码缓存 1GB   | 存储 JIT 编译后的热点代码，避免重复编译开销                                      | 代码量大/频繁热部署项目建议 ≥1G；小项目 512m 可满足                              |
| `-XX:+UseG1GC`         | 使用 G1 垃圾回收器 | 低延迟垃圾回收，平衡吞吐量与停顿时间，适合 CLion 频繁响应的场景                | 比传统 GC 更适合 IDE，减少卡顿感                                                 |

#### **垃圾回收与并行**
| **参数**               | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-XX:MaxGCPauseMillis=200`     | 最大 GC 停顿时间 200ms | 控制垃圾回收时最大暂停时间，提升交互响应流畅度                                 | 值越低交互越流畅，但可能增加 GC 频率（200ms 是平衡点）                          |
| `-XX:ParallelGCThreads=8`      | 并行 GC 线程数 8   | 垃圾回收时使用的并行线程数（建议为 CPU 物理核心数的 1~1.5 倍）                 | 4 核 CPU 可设 4~6；8 核及以上建议 8~10                                           |
| `-XX:ConcGCThreads=4`          | 并发 GC 线程数 4   | G1 垃圾回收的并发阶段线程数                                                      | 通常设为 ParallelGCThreads 的一半（如 8 核对应 4）                              |
| `-XX:InitiatingHeapOccupancyPercent=45` | 堆占用触发 GC 百分比 45% | 当堆内存使用率达到 45% 时启动 GC，避免内存耗尽                                 | 默认值可能过高，调低可提前触发 GC，减少卡顿                                     |

#### **调试与内存保护**
| **参数**               | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-XX:+HeapDumpOnOutOfMemoryError`  | OOM 时生成堆转储文件 | 内存爆炸时自动保存快照，方便排查崩溃原因                                     | 必开！遇到卡死/崩溃时通过日志定位问题                                           |
| `-XX:HeapDumpPath=$USER_HOME/clion_error.hprof` | 堆转储文件路径   | OOM 快照保存位置（用户目录下，避免覆盖其他 IDE 的 dump 文件）                | 自定义路径便于管理多个 IDE 的崩溃日志                                           |
| `-XX:+AlwaysPreTouch`          | 启动时预分配内存   | 启动时预先分配所有堆内存，避免运行时动态分配卡顿                             | 适合物理内存充足的机器（如 16GB+），提升启动稳定性                              |
| `-XX:-OmitStackTraceInFastThrow` | 禁用快速抛异常优化 | 避免 JVM 对重复异常（如空指针）省略堆栈信息，方便排查报错根源                 | 开发阶段必开！否则部分报错可能只有简单提示，难以定位                            |

#### **编译与性能优化**
| **参数**               | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-XX:TieredStopAtLevel=1`      | 编译优化层级 1     | 限制 JIT 编译优化层级，加快代码启动速度（牺牲部分峰值性能）                   | 适合开发阶段频繁修改代码的场景，减少编译等待时间                                |
| `-XX:CICompilerCount=8`        | JIT 编译器线程数 8 | 负责将热点代码编译为机器码的线程数，加速代码执行效率                         | 多核机器（如 8 核以上）可适当调高（默认 2~4），提升编译速度                     |
| `-XX:SoftRefLRUPolicyMSPerMB=100` | 软引用缓存策略 100ms/MB | 控制软引用对象（如缓存）的存活时间（每 MB 堆内存保留 100ms）                   | 提高常用代码/数据的缓存命中率，减少重复加载                                    |

#### **编码与渲染**
| **参数**               | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-Dfile.encoding=UTF-8`            | 文件编码 UTF-8   | 确保读写代码文件时用 UTF-8，避免中文/特殊字符乱码（必开！）                  | 处理多语言项目（如中英文混合代码）时必须开启                                     |
| `-Dsun.jnu.encoding=UTF-8`         | 系统路径编码 UTF-8 | 解决 Windows 下中文目录（如 `D:\项目\CLion代码`）显示/操作乱码问题            | 中文系统用户建议开启                                                             |
| `-Dsun.io.useCanonCaches=false`    | 禁用规范路径缓存   | 避免文件路径解析缓存导致的异常（如网络映射盘符变动）                         | 使用网络存储（如 NAS/SMB）或虚拟机共享目录时建议关闭                           |
| `-Djdk.attach.allowAttachSelf=true` | 允许 JVM 自身附加  | 支持 IDE 内部工具（如热部署插件）动态附加到当前 JVM 进程                       | 某些调试/热更新插件依赖此功能                                                   |
| `-Djdk.module.illegalAccess.silent=true` | 静默模块非法访问   | 兼容旧版库（如非模块化 jar 包）的反射调用，避免因 Java 模块化限制报错          | 使用旧版依赖（如传统工具包）时建议开启                                          |

#### **图形渲染**
| **参数**               | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `-Dsun.java2d.d3d=true`          | 启用 Direct3D 渲染 | 使用 Direct3D 加速图形绘制（适合 Windows 显卡支持的场景）                    | Windows 用户若显卡支持 Direct3D，可开启提升界面流畅度                           |
| `-Dsun.java2d.opengl=false`      | 禁用 OpenGL 渲染   | 避免 OpenGL 渲染导致的兼容性问题（如部分显卡驱动异常）                       | 若遇到 OpenGL 渲染故障（如界面闪烁），建议关闭                                  |
| `-Dsun.java2d.renderer=sun.java2d.marlin.MarlinRenderingEngine` | Marlin 渲染引擎 | 替换默认渲染引擎，优化高分辨率屏幕的图形绘制性能                             | 高分辨率显示器（如 4K 屏）建议开启                                              |

#### **模块访问（兼容性）**
| **参数**                                 | **值**           | **作用说明**                                                                 | **适用场景提示**                                                                 |
|------------------------------------------|------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| `--add-opens=java.base/java.lang=ALL-UNNAMED` | 开放 java.lang 包访问 | 允许插件反射访问 JDK 核心类库（如 java.lang.reflect），解决“非法访问”报错     | 若遇到插件（如代码分析工具）不生效，检查是否缺少此参数                          |
| `--add-opens=java.base/sun.nio.fs=ALL-UNNAMED` | 开放 sun.nio.fs 包访问 | 支持插件对文件系统操作的反射调用（如处理符号链接/网络路径），提升兼容性       | 某些文件操作相关的插件（如远程开发工具）可能需要此权限                          |

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
### **🔧 CLion Performance Tuning Core Parameters (With Brief Notes)**

#### **Basic Memory Allocation**
| **Parameter**               | **Value**           | **Description**                                                                 | **Use Case Tips**                                                                 |
|-----------------------------|---------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| `-Xms1024m`                 | Initial heap 1GB    | JVM startup allocated heap to avoid runtime lag from frequent memory requests.  | Lightweight projects (small C/C++ apps): 512m; medium/large projects: ≥1GB.      |
| `-Xmx4096m`                 | Max heap 4GB        | Maximum JVM heap memory, directly impacts CLion's compilation/runtime speed.    | Machines with ≥8GB RAM: 4~6GB; 4GB RAM machines: ≤3GB.                           |
| `-XX:ReservedCodeCacheSize=1024m` | Code cache 1GB     | Stores JIT-compiled hotspot code to reduce redundant compilation overhead.      | ≥1GB for large/complex C/C++ projects; 512m for small projects.                  |
| `-XX:+UseG1GC`              | Use G1 GC           | Low-latency GC balancing throughput/pause time, ideal for CLion responsiveness. | Better than CMS/Parallel GC for reducing IDE lag.                                |

#### **Garbage Collection & Parallelism**
| **Parameter**               | **Value**           | **Description**                                                                 | **Use Case Tips**                                                                 |
|-----------------------------|---------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| `-XX:MaxGCPauseMillis=200`  | Max GC pause 200ms  | Controls maximum GC pause time, improving interactive responsiveness.           | Lower = smoother interaction, but may increase GC frequency (200ms is balanced). |
| `-XX:ParallelGCThreads=8`   | Parallel GC threads | Threads for parallel GC (recommended: 1~1.5x CPU cores, e.g., 4~6 for 4-core, 8~10 for 8+). | Adjust based on CPU cores (e.g., 8 for 8-core CPU).                              |
| `-XX:ConcGCThreads=4`       | Concurrent GC threads | Threads for G1 concurrent phase (typically half of ParallelGCThreads).          | Usually half of ParallelGCThreads (e.g., 4 for 8-core).                          |
| `-XX:InitiatingHeapOccupancyPercent=45` | Heap GC trigger 45% | Starts GC when heap usage reaches 45%, avoiding memory exhaustion.              | Default may be too high; lower to trigger GC earlier, reducing lag.              |

#### **Debugging & Protection**
| **Parameter**               | **Value**           | **Description**                                                                 | **Use Case Tips**                                                                 |
|-----------------------------|---------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| `-XX:+HeapDumpOnOutOfMemoryError` | OOM heap dump       | Auto-saves memory snapshot on crash to diagnose memory leaks.                   | Must-enable: Locate OOM causes via logs when CLion freezes.                      |
| `-XX:HeapDumpPath=$USER_HOME/clion_error.hprof` | OOM dump path    | Saves OOM snapshots to user directory (avoids overwriting other IDE dumps).     | Custom path for easy debugging.                                                  |
| `-XX:+AlwaysPreTouch`       | Pre-allocate memory | Pre-allocates all heap memory on startup, avoiding runtime dynamic allocation lag.| Suitable for machines with ≥16GB RAM, improves stability.                        |
| `-XX:-OmitStackTraceInFastThrow` | Disable fast-throw  | Preserves full stack traces for repeated exceptions (e.g., NullPointerException). | Essential for debugging in development (avoids vague error messages).            |

#### **Compilation & Performance**
| **Parameter**               | **Value**           | **Description**                                                                 | **Use Case Tips**                                                                 |
|-----------------------------|---------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| `-XX:TieredStopAtLevel=1`   | Compilation level 1 | Limits JIT optimization tiers, speeding up code startup (trades peak performance). | Ideal for frequent code changes during development.                              |
| `-XX:CICompilerCount=8`     | JIT compiler threads | Threads compiling hotspot code to machine code, accelerating execution.         | Higher for multi-core machines (≥8 cores) to boost compilation speed.            |
| `-XX:SoftRefLRUPolicyMSPerMB=100` | Soft ref policy 100ms/MB | Controls lifetime of soft-referenced objects (e.g., caches) per MB heap.        | Improves cache hit rate for frequently used code/data.                           |

#### **Encoding & Rendering**
| **Parameter**               | **Value**           | **Description**                                                                 | **Use Case Tips**                                                                 |
|-----------------------------|---------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| `-Dfile.encoding=UTF-8`     | File encoding UTF-8 | Ensures UTF-8 for file I/O, prevents Chinese/special character corruption.      | Must-enable for multi-language projects (e.g., Chinese comments).                |
| `-Dsun.jnu.encoding=UTF-8`  | Path encoding UTF-8 | Resolves Unicode path (e.g., Windows) display/operation issues.                 | Recommended for non-English Windows systems.                                     |
| `-Dsun.io.useCanonCaches=false` | Disable canon caches | Avoids path resolution issues (e.g., network drive changes).                    | Use for network storage (NAS/SMB) or virtual machine shared folders.             |
| `-Djdk.attach.allowAttachSelf=true` | Allow JVM self-attach | Supports IDE internal tools (e.g., hot-deploy plugins) attaching to current JVM. | Required by some debugging/hot-update plugins.                                   |
| `-Djdk.module.illegalAccess.silent=true` | Silent illegal access | Allows reflection on legacy libraries (non-modular JARs) without Java module errors. | Use when relying on older dependencies (traditional toolkits).                   |

#### **Graphics Rendering**
| **Parameter**               | **Value**           | **Description**                                                                 | **Use Case Tips**                                                                 |
|-----------------------------|---------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| `-Dsun.java2d.d3d=true`     | Enable Direct3D     | Uses Direct3D for graphics acceleration (suitable for Windows with supported GPUs). | Windows users with Direct3D-capable GPUs can enable for smoother UI.             |
| `-Dsun.java2d.opengl=false` | Disable OpenGL      | Avoids OpenGL rendering compatibility issues (e.g., driver glitches).           | Disable if encountering OpenGL-related rendering problems (e.g., flickering).    |
| `-Dsun.java2d.renderer=sun.java2d.marlin.MarlinRenderingEngine` | Marlin renderer | Replaces default renderer, optimizing high-DPI screen drawing performance.      | Enable for 4K/Retina displays.                                                   |

#### **Module Access (Compatibility)**
| **Parameter**                                 | **Value**           | **Description**                                                                 | **Use Case Tips**                                                                 |
|-----------------------------------------------|---------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| `--add-opens=java.base/java.lang=ALL-UNNAMED` | Open java.lang      | Allows plugins to reflectively access JDK core classes (e.g., java.lang.reflect). | Fix "illegal access" errors for plugins (e.g., code analyzers).                  |
| `--add-opens=java.base/sun.nio.fs=ALL-UNNAMED` | Open sun.nio.fs     | Supports plugins' reflection on filesystem operations (e.g., symlinks/network paths). | Required by plugins handling file system operations (e.g., remote dev tools).    |

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
📄 免责声明 / Disclaimer​
(中英双语对照 / Bilingual Version - Self-Deprecating Style)
中文版
```diff
⚠️ 本人非科班野生程序猿，才疏学浅，所分享的 JetBrains IDE 调优参数均来自个人实测 + 社区经验总结，非官方指南！

🤣 虽然调优后我的 IDEA/PyCharm/WebStorm/GoLand/CLion 跑得飞起（亲测 3 天 6 篇爆款，CSDN 日均 2000+ 访问），
但你的电脑配置、项目类型、JDK 版本可能和我完全不同——

⚠️ 用前请务必备份原配置文件（Help → Edit Custom VM Options），调优后若出现卡顿加剧、插件冲突、IDE 崩溃等问题，
本人概不负责！

😎 欢迎各位大神在 Issue 区/评论区友好交流，但拒绝抬杠（比如"你这参数没用""我按你说的更卡了"）。

🎯 本项目的核心目标是帮开发者省下折腾 IDE 的时间，多敲几行代码，少熬几根头发～

📌 最后喊一句：技术无国界，但参数得看人！按需调整，理性冲浪，咱们一起让 JetBrains 全家桶真的起飞！
```
English Version
```diff
⚠️ I'm just a self-taught (non-CS-major) wild programmer with limited skills. All JetBrains IDE tuning
parameters shared here are based on my personal tests + community experiences, NOT official guides！

🤣 Although my IDEA/PyCharm/WebStorm/GoLand/CLion run like lightning after tuning (proven by 3 days of 6 viral
posts, 2000+ daily visits on CSDN), your hardware specs, project types, and JDK versions may be totally different from mine——

⚠️ BACK UP YOUR ORIGINAL CONFIGURATION FILE (Help → Edit Custom VM Options) BEFORE USING THESE PARAMS！
If you encounter slower performance, plugin conflicts, or IDE crashes after tuning, I TAKE NO RESPONSIBILITY！

😎 Feel free to discuss friendly in Issues/Comments, but NO ARGUING (e.g., "These params are useless"
or "My IDE got slower after following your guide").

🎯 The goal of this project is to save developers time on tweaking IDEs, so you can write more code and
lose fewer hairs～

📌 Final shoutout: Tech has no borders, but parameters depend on the user！Adjust wisely, surf rationally,
 and let's make the JetBrains family truly fly together！
```
