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

---
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

---
# 在 Wanglab 使用生物信息工具 #

## Micromamba ##
- 查看已有 micromamba 环境:
  ` micromamba env list `
- 激活某个 micromamba 环境:
  ` micromamba activate ml-env `

## Snakemake ##
- 检查 ml-env 环境里有没有安装 snakemake:
  ` which snakemake `

## 查找想要的文件夹 ##
- ` find ~ -type d -name "proteomics_model_2" `
- 位置：` /home/zixuan/Desktop/cool-project/proteomics_model_2 `
- `ls`: list - 列出当前目录下的文件和文件夹
- `cd`: change directory - 进入某个目录
- 进去 proteomics 项目查看：
  ` cd /home/zixuan/Desktop/cool-project/proteomics_model_2 `
  ` ls -lh `

## 运行 snakemake ##
- dry-run 一下试试流程对不对: ` snakemake -n -p `
- 直接运行： ` snakemake -j 4 `

## 查找电脑写好的环境脚本 ##
- ` find ~/Desktop/cool-project/proteomics_model_2 -type f \( -name "*.yml" -o -name "*.yaml" \) `
- 叫做： ` /home/zixuan/Desktop/cool-project/proteomics_model_2/envs/ml-env.yaml `
- 查看脚本： ` cat envs/ml-env.yaml `

## 安装包 ##
- 查找： ` micromamba list | grep -E "scikit-learn|pandas|numpy" `
- 安装： ` micromamba install numpy pandas scikit-learn -c conda-forge `
- 测试： `python -c 'import numpy; import pandas; import sklearn; print("✅ All three packages are installed and working!")'`

## 对于 proteomics 项目，运行 snakemake ##
- 用 --config 参数从命令行传: ` snakemake -np --config input_csv=data/0012_spsion_zixuan.csv `

## 看建立的环境是否有 pytorch ##
- 因为 PyTorch 不是一个独立的可执行文件（不像 python、pip 这种），所以终端不会告诉你路径。
- 在终端输入：` pip show torch `

## Python 虚拟环境退出指南 ##

| 环境类型            | 激活方式示例                                 | 退出方式                           | 说明 |
|---------------------|----------------------------------------------|------------------------------------|------|
| **venv**            | `source env/bin/activate`                    | `deactivate`                       | Python 官方内建 |
| **virtualenv**      | `source env/bin/activate`                    | `deactivate`                       | 和 venv 一样 |
| **conda**           | `conda activate myenv`                       | `conda deactivate`                 | 来自 Anaconda/Miniconda |
| **mamba**           | `mamba activate myenv`                       | `mamba deactivate`                 | conda 的加速替代 |
| **micromamba**      | `micromamba activate myenv`                  | `micromamba deactivate`            | 超轻量，需先初始化 shell |
| **pipenv**          | `pipenv shell`                               | `exit` 或 `Ctrl+D`                 | 开了一个子 shell，直接退出 |
| **poetry**          | `poetry shell`                               | `exit` 或 `Ctrl+D`                 | 同上，退出 subshell 即可 |
| **Docker 容器**     | `docker exec -it <container> bash`          | `exit` 或 `Ctrl+D`                 | 技术上也算一种“环境” |
| **Jupyter kernel**  | 在 Notebook 里选择某个 kernel                | 切换或重启 kernel                  | 不需要手动退出 shell |


### 🧠 小贴士

- `deactivate` 是 shell function，不是独立命令，如果环境没完全初始化，可能找不到它。
- `conda` / `micromamba` 要确保 shell 初始化过：先运行 `source ~/.bashrc`（或 `.zshrc`）。
- 如果你不确定用的是什么环境，先运行 `which python`，看路径来自哪里。
- 退出 pipenv / poetry 时记得：**它们打开的是一个新的 shell，退出方法是 `exit` 或 `Ctrl+D`**。


### 💡 Bonus：快速判断当前环境类型

- 查看当前使用的 Python 路径
` which python `

- 示例输出：
`/home/zixuan/micromamba/envs/ml-env/bin/python`  ← 说明你在 micromamba 环境中 

---
## 查找 Python 虚拟环境的方法

以下是查找本地 Python 虚拟环境的几种方法，适用于不同工具（如 `micromamba`、`conda`、`venv` 等）。

### 1. 使用 `micromamba` 管理的环境

运行以下命令列出所有 `micromamba` 管理的虚拟环境：

```bash
micromamba env list
```

输出示例：
```
Name                    Path
ml-env                  /home/zixuan/micromamba/envs/ml-env
sfcm-env                /home/zixuan/micromamba/envs/sfcm-env
base                    /home/zixuan/micromamba
```

- **说明**：当前激活的环境会在名称前显示 `*`。

### 2. 使用 `conda` 管理的环境

如果也使用 `conda`，运行：

```bash
conda env list
```

输出格式与 `micromamba` 类似。

### 3. 查找手动创建的 `venv` 环境

对于用 `python -m venv` 创建的环境，假设存放在 `~/.envs/`：

```bash
ls ~/.envs/
```

或搜索用户目录下所有可能的 `venv` 路径：

```bash
find ~ -type d -name "bin" -path "*/envs/*" 2>/dev/null
```

---
## 已经写好 bash 脚本可以自动用 micromamba 创建环境，下载安装包
- 脚本文件保存在：` ~/Documents/bash_script `
- 每次运行 ` source scfm_env.sh `, 环境就自动创建好！

---
Absolutely，给你一个 **简约又清晰的 Markdown 版本**，适合贴进笔记、README 或你自己的 HPC 文档 ✅

---

## 📝 Bash 脚本编写与执行流程

```bash
# 1. 使用 nano 新建脚本
nano setup.sh
```

> 在编辑器中粘贴你的脚本内容，比如：

```bash
#!/bin/bash
echo "Hello, world!"
```

```bash
# 2. 保存并退出 nano
# Ctrl + O（写入），Enter（确认），Ctrl + X（退出）

# 3. 添加执行权限
chmod +x setup.sh

# 4. 运行脚本
./setup.sh

# 5. 可选：运行并记录日志
./setup.sh | tee run_log.txt
```

## 🔍 常用指令

```bash
# 查看脚本内容
cat setup.sh

# 再次编辑脚本
nano setup.sh

# 检查是否具有执行权限（应有 x 权限）
ls -l setup.sh
```

---
## 删除 Micromamba 已有的环境 ##
- ` micromamba env list `
- ` micromamba remove -n sfcm_env --all `
- 检查是否还有：` micromamba env list `
- 不行的话删除还剩的目录：` rm -rf /home/zixuan/micromamba/envs/sfcm_env `

---
# 用别名 alias 简化命令

## 已在 wanglab 电脑简化以下命令
```bash
mamba is aliased to `micromamba'
act_env is aliased to `micromamba activate sim_env'
```

## 步骤
```bash
nano ~/.bashrc
```
- 添加下面两行到文件
```bash
# Shorten micromamba commands
alias mamba="micromamba"
alias act_env="micromamba activate sim_env"
```
-保存并退出（在 nano 中按 Ctrl+O, 回车, 然后 Ctrl+X）
```bash
#使 alias 生效
source ~/.bashrc
```
-验证 alias 是否生效
```bash
type mamba
type act_env
```
---

## 设置 SSH alias ##

- 打开 ~/.ssh/config 文件
```bash
nano ~/.ssh/config
```
- 文件末尾加上下面几行
```bash
Host wanglab
  HostName wanglab-a16-2.ad.umassmed.edu
  User zixuan
  IdentityFile ~/.ssh/id_ed25519  #This is the key
```
- 之后可以输入来直接远程连上
```bash
ssh wanglab
```
- 学校的 HPC 同理
```bash
Host hpc
   Hostname hpc.umassmed.edu
   User zixuan.ye-umw
   IdentityFile ~/.ssh/hpc_umass_key
```

---

## 创建 symlink 和 利用 alias 简化激活环境的命令
```bash
#先进入环境目录
cd /home/zixuan/micromamba/envs
ls

#加入 symlink
ln -s sim_env2 env
#ln = link, -s = symbolic link

#加 alias
nano ~/.bashrc
alias mamba='micromamba'
source ~/.bashrc
```
最后只用输入下面命令就可以轻松激活环境

```
mamba activate env
```
