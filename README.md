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