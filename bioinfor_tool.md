# 什么是 Mamba, Sourmash 和 Snakemake？

## 1. Mamba
- **是什么？**  
  Mamba 是一个高效的包管理工具，类似于 `conda`，但速度更快。它用来创建和管理 Python 环境，安装软件包。
- **为什么用它？**  
  - 比 `conda` 更快，节省时间。
  - 支持从多个渠道（如 `conda-forge`、`bioconda`）安装软件。
- **例子：**  
  ```bash
  mamba create -n my_env python=3.10
  ```
  创建一个名为 `my_env` 的 Python 3.10 环境。

## 2. Sourmash
- **是什么？**  
  Sourmash 是一个用于快速比较基因组和序列数据的生物信息学工具。它基于 “MinHash” 算法，能快速估计序列相似性。
- **为什么用它？**  
  - 不需要完整比对，适合处理大数据。
  - 可以用来分类微生物、检查样本相似性。
- **例子：**  
  ```bash
  sourmash sketch dna -p k=31 input.fasta
  ```
  生成一个序列的 “签名”（sketch）用于比较。

## 3. Snakemake
- **是什么？**  
  Snakemake 是一个工作流管理工具，用来自动化运行复杂的数据分析任务。它用 Python 风格的脚本定义规则。
- **为什么用它？**  
  - 让多步骤任务（比如数据处理管道）更简单、可重复。
  - 支持并行计算，节省时间。
- **例子：**  
  ```python
  rule all:
      input: "output.txt"
  rule step1:
      input: "input.txt"
      output: "output.txt"
      shell: "cp {input} {output}"
  ```
  定义一个简单的工作流，复制文件。

---

## 总结
- **Mamba**: 管理环境和包，快如闪电。
- **Sourmash**: 快速比对序列，生物信息学的利器。
- **Snakemake**: 自动化工作流，科研效率翻倍。

---

## 4. `ipykernel` 是什么？

- 全称：`IPython Kernel`  
- 简单说：它是一个 Python 的“后台发动机”，专门让 Jupyter Notebook（或类似工具）能跑 Python 代码。  
- 通俗比喻：想象 Jupyter Notebook 是个前台界面（像个计算器），你输入代码后，它得有个“发动机”在背后算结果，这个发动机就是 `ipykernel`。

- **它干啥用？**

  - 核心作用：连接 Jupyter Notebook 和 Python 环境。  
  - 你在 Notebook 里写代码（比如 `print("Hello")`），`ipykernel` 负责把代码送到 Python 解释器跑，然后把结果送回来显示。  
  支持多环境：如果你有好几个 Python 环境（比如用 Conda 建的 `proteomics_env`），可以用不同的 `ipykernel` 让 Notebook 切换环境跑代码。

- **跟 Conda 有啥关系？**

  - 你之前用 `conda create -p ~/envs/proteomics_env python=3.12` 建了个环境，如果想在 Jupyter Notebook 里用这个环境的 Python，就得装 `ipykernel`。  
  - 装了 `ipykernel` 后，可以把这个环境“注册”到 Jupyter，让你选它跑代码。

---

## 5. 公钥和私钥（以 SSH 和 GitHub 为例）

### 🚀 一次 SSH 连接发生了什么？

你在本地生成一对密钥对（`ssh-keygen` 命令）：

- `id_rsa` → **私钥**，⚠️ 保密！别给任何人！
- `id_rsa.pub` → **公钥**，✅ 可以公开给 GitHub。

接着你：

1. 把 **公钥 `id_rsa.pub`** 上传到 GitHub（在 Settings → SSH and GPG keys）。
2. 当你使用 Git 推代码到 GitHub（比如 `git push`）时，SSH 会：
   - 用你本地的 **私钥签名** 一次身份验证请求；
   - GitHub 用你提供的 **公钥验证** 签名；
   - 如果匹配成功，GitHub 就知道你是自己人，准你访问仓库。

---

### 🔑 总结一张表：

| 操作                         | 用的钥匙  | 作用                         |
|------------------------------|------------|------------------------------|
| 本地 `ssh-keygen` 生成密钥对 | 公钥 + 私钥 | 生成一对密钥（绑定关系）     |
| 上传到 GitHub               | 公钥       | GitHub 用它验证你的身份      |
| 实际连接 GitHub（如 `git push`） | 私钥       | 本地用它签名，证明「我就是我」 |

👉 **你用私钥证明自己，GitHub 用你给的公钥验明身份。**

---

### 🚨 一些常见坑（友情提示）

- ❌ **私钥千万别上传！**
  - 别放进 GitHub 仓库、别复制粘贴给别人、别传微信！
- ✅ **公钥才是给 GitHub 的。**
- 🛑 加了公钥 ≠ 自动有 repo 权限。GitHub 那边还得给你加权限（Collaborator、Team 等）。
- 🖥️ 换了电脑？记得把新电脑的公钥也加到 GitHub。

---

# 🧬 What is `.mzML` and Why Use It?

## 📌 Definition
`.mzML` is an **open standard file format** for mass spectrometry data, developed by HUPO-PSI. It stores both raw spectral data and rich metadata in a vendor-neutral, XML-based structure.

---

## 🔬 What `.mzML` Contains

- MS1 / MS2 **scan-level spectra**
- **m/z and intensity** pairs for each scan
- **Retention time**, **scan number**, **charge state**
- Instrument settings and acquisition metadata

---

## 🆚 `.raw` vs `.mzML`

| Feature               | `.raw` (Vendor Format)       | `.mzML` (Open Standard)          |
|-----------------------|------------------------------|----------------------------------|
| Readability           | ❌ Requires Thermo SDK        | ✅ Readable in R/Python/C++      |
| Platform Support      | ❌ Windows-only               | ✅ Cross-platform (Linux/Mac/Win)|
| Tool Compatibility    | ❌ Few tools (`rawrr`)        | ✅ Broad support: `mzR`, `Spectra`, `MSnbase`, `OpenMS`, etc. |
| Automation-Friendly   | ❌ Difficult for pipelines     | ✅ Ideal for Snakemake/Nextflow  |
| Community Support     | 👎 Proprietary                | 👍 Standard in PRIDE, MassIVE    |
| Portability/Sharing   | ❌ Binary, large               | ✅ Compressible, transferable    |

---

## ✅ When to Use `.mzML`

- You work on **Linux or Mac**
- You need **batch processing or reproducible pipelines**
- You want to use R/Python libraries like `mzR`, `Spectra`, `MSnbase`
- You plan to **share or publish** data (PRIDE, MassIVE, etc.)
- You need a format that integrates well with **Snakemake workflows**

---

## 🛠️ How to Convert `.raw` → `.mzML`

1. **Install** [ProteoWizard](https://proteowizard.sourceforge.io/)
2. **Convert** using the command line:

   ```bash
   msconvert your_file.raw --mzML -o output_folder/

---

# 🐳 什么是 Docker？为什么在质谱数据处理中要用它？

## 🔍 Docker 是什么？

Docker 是一个**轻量级容器平台**，可以让你在任何系统（Linux/Mac/Windows）中运行别人已经配置好的软件环境，称为“容器”。

你不需要手动安装一堆依赖，只需要一条命令就能运行完整的程序，非常适合运行复杂工具（如 ProteoWizard）或者集成到 Snakemake 等自动化流程中。

---

## 💡 为什么质谱处理要用 Docker？

Thermo 的 `.raw` 文件只能通过 **Windows 下的 DLL 文件** 正确读取。而 ProteoWizard 的 `msconvert` 工具在 Linux 上的原生版本并不支持读取 `.raw`。

使用 Docker，可以在 Linux 中模拟 Windows 环境，**让你在 Linux 上也能运行支持 `.raw` 的 `msconvert`**。

---

## ✅ 使用 Docker 的优势

| 功能                       | 不使用 Docker               | 使用 Docker                               |
|----------------------------|------------------------------|--------------------------------------------|
| Linux 读取 `.raw` 支持     | ❌ 不支持 Thermo DLL          | ✅ 可通过 Wine 在容器中运行                |
| 依赖配置是否复杂           | ✅ 手动安装，容易出错         | ❌ 已封装好，开箱即用                      |
| 是否适合批量分析           | ❌ 难以集成自动流程           | ✅ 适合 Snakemake / Nextflow / 脚本运行     |
| 能否调用 Windows 工具      | ❌ Linux 无法运行 `.exe` 文件 | ✅ 容器内可运行 `msconvert.exe`             |

---

## 🛠️ 如何检查是否已安装 Docker

在终端中运行：

```bash
docker --version

---

# 🧪 ProteoWizard 工具总结

## 📌 简介

**ProteoWizard** 是一个用于质谱数据处理的跨平台开源工具集，广泛应用于蛋白质组学和代谢组学中。它提供了强大的命令行工具（如 `msconvert`）和编程库，支持各种原始厂商格式（如 Thermo `.raw`）向开放标准格式（如 `.mzML`）的转换。

---

## 🛠️ 核心功能

- 转换原始厂商格式（`.raw`, `.wiff`, `.d` 等）为：
  - `.mzML`（推荐标准）
  - `.mzXML`
  - `.mgf`
- 支持谱图过滤（如 centroid 化、去噪、保留特定 MS 级别）
- 提供图形界面（Windows）、命令行接口（多平台）
- 与 Skyline、OpenMS、MSFragger、MSnbase 等工具兼容

---

## 📦 常用组件

| 工具名称   | 用途                            |
|------------|---------------------------------|
| `msconvert` | 将原始数据转换为开放格式（主力工具） |
| `idconvert` | 转换鉴定结果（如 pepXML → mzid） |
| `msaccess`  | 提取谱图统计和 QC 信息            |

---

## ✅ 支持的文件格式

| 输入格式（厂商原始）       | 输出格式（开放标准）  |
|---------------------------|-----------------------|
| Thermo `.raw`             | `.mzML`, `.mgf`, `.mzXML` |
| Agilent `.d`              | `.mzML`               |
| Sciex `.wiff`             | `.mzML`               |
| Bruker `.d`               | `.mzML`               |

---

## 🚀 安装方式

### 🔵 Windows 用户

- 推荐安装 ProteoWizard GUI 安装包（内含 CLI 工具）
- 系统要求：Windows + .NET Framework ≥ 4.7.2
- 下载地址：[https://proteowizard.sourceforge.io/download.html](https://proteowizard.sourceforge.io/download.html)

### 🟢 Linux / Mac 用户

- 原生版本不支持 `.raw` 转换（无 DLL 支持）
- 推荐使用 Docker 镜像或 Wine 运行 Windows 版：

```bash
docker run -it --rm \
  -v $PWD/04_data:/data_in \
  -v $PWD/06_data:/data_out \
  chambm/pwiz-skyline-i-agree-to-the-vendor-licenses \
  wine msconvert /data_in/sample.raw --mzML -o /data_out
---
