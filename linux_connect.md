# 从终端连接HPC #
## 使用 umassmed 账号远程登陆 ##
` ssh zixuan.ye-umw@hpc.umassmed.edu `
- ssh is secure shell, 用于远程登陆和数据传输

## 申请一个 interactive node（正式“上机”）##
` bsub -Is -q interactive -R "rusage[mem=4G]" -n 2 /bin/bash `
- bsub is batch submit, 将计算任务（如脚本、程序或命令）提交到 LSF 集群的队列，等待调度器分配资源后运行。
- -q is queue, 表示制定队列
- -R is resource, 用于定义作业运行所需的资源要求
- rusage：表示“资源使用”（resource usage），是 LSF 中的一种资源请求方式，用于指定作业在运行时需要的资源量。
- mem=5G：具体资源需求，这里表示内存（memory）需求为 5GB。
- -n 申请核心
- /bin：这是 Linux 文件系统中的一个目录，通常存放系统的基础可执行文件（二进制文件）。
- Bash 是一种 shell（命令行解释器），负责接收用户输入的命令，解释并执行它们


# 从终端连接 Wanglab 电脑 #
- 之前已经设置好了，直接输入下列指令可以连入
  ` ssh wanglab `
- 包括连入 hpc，输入下面就行
  ` ssh hpc `

## 已经在 wanglab 装好 micromamba, pytorch, snakemake, 以及各种数据包(例如numpy, pandas) ##
- 已经设置好指令，要退出 micromamba 环境，直接输入
  ` bye `

## 在 mac 保存了脚本，能够将 proteomics_model_2 文件夹的代码同步传递到 wanglab ##
- 打开 ` nano sync_proteomics_wanglab.sh `
- 运行 ` ~/sync_proteomics_wanglab.sh `
