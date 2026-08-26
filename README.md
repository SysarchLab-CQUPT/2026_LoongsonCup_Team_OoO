## 26年团体赛参赛 NSCore

仿照香山写的，所以取名**南山**，架构介绍看 [`design.pdf`](./doc/design.pdf)

### 分工

| 方向  | GitHub |
|------|--------|
| 整体架构流水线  | [@HisionWang](https://github.com/HisionWang) |
| 系统强相关方面  | [@Qumxian](https://github.com/Qumxian) |
| L2 Cache  | [@Be-discriminated](https://github.com/Be-discriminated) |

### 性能

| 指标 | 数值 |
|------|------|
| 主频（Core.scala 当前配置） | **81.81 MHz** |
| IPC 加速比 | **~1.76** |
| 总体加速比 | **~4.4×** |


| 指标 | 当前Core.scala中的配置 | 26年第一名 | 祝你可以达到 |
|------|------|---------------|----------|
| IPC 加速 | ~1.76 | **2.5** | 3 |
| 主频 | 81.81 MHz | **>100 MHz** | — |
| 整体加速 | ~4.4× | **>8×** | >10× |

### 💩山

项目💩山部分：
分发阶段的那一坨要小心

重命名的那一坨要小心

Rob重定向那一坨要小心

减小L1，再裁关键路径、主频还能提

CPU中还有bug，自编译的某些Linux无法启动，大概率是L2 cacop没有实现的问题

---

## 三等奖获奖感言

不得不感慨，AI 加持下，大家的平均水平真的被拉高太多了……个人努力的同时也多烧点优质Token吧。

然后，性能固然重要，但它不是第一性原理。答辩要提前认真准备，自研 SoC 这条路也必须得花时间探索了。

> 累了……

## 环境要求

执行前请确认以下工具可用：

- Bash
- `sbt`
- GNU Make
- Verilator、`g++`
- ChipLab 中的 LoongArch 工具链

工程使用 `sbt` 构建时的主要版本配置如下：

- Scala：`2.13.14`
- Chisel：`3.6.1`
- ChiselTest：`0.6.2`

`make ss` 会加载项目根目录的 `env.sh`，设置 `CHIPLAB_HOME` 并将 ChipLab 工具链加入 `PATH`。如直接执行 ChipLab 下的脚本，需要先手动加载环境：

```bash
cd ..
source env.sh
cd designCPUByChisel
```

## 仿真：`make ss`

### 快速运行

```bash
cd designCPUByChisel
make ss
```

该命令依次完成以下工作：

1. 执行 `sbt "runMain nscscc.CoreGen simu"`，生成带差分测试支持的仿真版 Verilog。
2. 将仿真版 Verilog 输出到 `../chiplab/IP/myCPU/Chisel/`。
3. 进入 `../chiplab/sims/verilator/run_prog/`，显示可运行的软件列表。
4. 根据输入的编号配置软件、编译 Verilator 仿真模型并启动测试。

运行过程中会看到类似下面的交互提示：

```text
Select simulation program:
   1) func/func_lab9
   2) func/func_lab14
   ...
Enter program number [1-...]:
```

输入程序编号后，脚本会调用 `configure.sh --run <program>`，随后执行仿真编译和运行。

## FPGA Verilog：`make ff`

### 生成 Verilog

```bash
cd designCPUByChisel
make ff
```

该命令执行：

```bash
sbt "runMain nscscc.CoreGen fpga"
```

生成的 FPGA 版 Verilog 位于：

```text
../chiplab/IP/myCPU/FPGA/
```

`CoreGen fpga` 会先清理并重建该输出目录，然后生成 `core_top.v` 及其依赖模块。FPGA 版关闭差分测试逻辑，适用于后续 Vivado 工程流程。