# 🧬 Proteomics 课题组：Windows + Linux (WSL) 混合开发实用指南

本指南总结了在 Windows 电脑上使用 Linux 终端（WSL）进行蛋白质组学开发、分析、Notebook 使用、数据管理等常见任务的最佳实践，适合 Jupyter + Python + Snakemake 等常见生信/计算工作流。

---

## 🌟 为什么要用 WSL？好处是什么？

WSL（Windows Subsystem for Linux）是 Windows 官方提供的一种方式，允许你在 Windows 上运行原生 Linux 环境。

### ✅ 用 WSL 而不是只用 Windows 的好处：

| 好处                          | 说明                                                                 |
|-------------------------------|----------------------------------------------------------------------|
| **更好支持科研工具链**         | 很多生信工具（如 Snakemake、ProteoWizard CLI、Pyteomics）原生为 Linux 开发 |
| **无需双系统 / 重启切换**     | 在 Windows 下就能直接用 Linux 工具，避免重启或装虚拟机                   |
| **命令行更强大、包管理更灵活** | 比 Windows 的 CMD/Powershell 更适合科研开发、批处理                    |
| **兼容远程服务器 / 代码一致性** | Linux 命令和路径风格与集群、高性能服务器一致，迁移代码更方便            |
| **性能优秀**                  | WSL2 使用虚拟硬盘，支持原生 Linux 内核，IO 性能大大优于 Docker 或 Git Bash |
| **能访问 Windows 文件**       | `/mnt/c/...` 可直接访问 Windows 文件，也可拷贝数据进 Linux              |

---

## 🖥️ 1. 基本环境构建

### ✅ 使用 WSL2 + Ubuntu
- 推荐安装 [WSL2 + Ubuntu 20.04/22.04](https://learn.microsoft.com/en-us/windows/wsl/install)；
- 可以在 WSL 内使用 Linux 原生包管理器、编译工具、文件系统等功能；
- 更适合运行 Python 包、Snakemake、ProteoWizard CLI 等。

---

## 🧪 2. 安装与使用 micromamba 环境（推荐替代 conda）

### 安装 micromamba（推荐二进制直接下载）

```bash
mkdir -p ~/bin
cd ~/bin
wget https://micro.mamba.pm/api/micromamba/linux-64/latest -O micromamba
chmod +x micromamba
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
````

### 创建环境 & 安装依赖

```bash
micromamba create -n mldata_env python=3.11 -c conda-forge
micromamba activate mldata_env
micromamba install ipykernel pandas jupyter notebook -c conda-forge
```

---

## 🧠 3. Notebook 使用推荐：Cursor + Jupyter 服务器分离式连接

### 方法：在 Linux（WSL）内启动 Jupyter Server，然后在 Cursor 中连接

```bash
micromamba activate mldata_env
jupyter notebook --no-browser --port=8888
```

复制输出的链接（如 `http://localhost:8888/?token=xxxx`），
然后在 **Cursor → Select Kernel → Enter an existing Jupyter Server** 中粘贴即可。

---

## ⚙️ 4. 启动 Jupyter 的自动化建议

### 推荐方式（已验证稳定）：

在 `~/.bashrc` 中添加：

```bash
if [[ "$WSL_DISTRO_NAME" && ! $(pgrep -f "jupyter-notebook") ]]; then
  echo "📢 Starting Jupyter Notebook server..."
  (micromamba activate mldata_env && nohup jupyter notebook --no-browser --port=8888 > ~/.jupyter.log 2>&1 &)
fi
```

每次打开 WSL 自动启动 Jupyter，无需手动输入。

---

## 📁 5. Windows 与 Linux 文件系统互通说明

### 关键原则：避免频繁从 WSL 直接读写 `/mnt/c/...` 路径中的大文件

| 操作                      | 推荐方式                                 |
| ----------------------- | ------------------------------------ |
| 项目开发（脚本、代码、模型）          | 保存在 `/home/zixuan/your_project`      |
| 原始数据（.csv, .mzML）       | 从 Windows 拷贝进 Linux 路径中处理            |
| 从 Windows 拷贝文件到 Linux   | 使用命令： `cp -r /mnt/c/... ~/your_path` |
| 从 Linux 访问 Windows 桌面文件 | 路径格式： `/mnt/c/Users/用户名/Desktop/`    |

---

## 🧪 6. 使用 Snakemake / DIA-NN / Proteomics 脚本建议

* 在 Linux 端运行 Snakemake 工作流性能更优；
* 推荐将 `.raw` → `.mzML` 文件转换步骤提前完成并放入 Linux 目录；
* 若 Windows 生成的数据过大，推荐定期复制进 WSL 并删除冗余。

---

## 🧰 7. 常见命令总结

```bash
# 激活环境
micromamba activate mldata_env

# 启动 Jupyter
jupyter notebook --no-browser --port=8888

# 查看是否复制成功
du -sh ~/01_mldata

# 复制文件从 Windows 到 Linux
cp -r /mnt/c/Users/cecilia/Desktop/transfer_test/01_mldata ~/01_mldata

# 检查路径
ls /mnt/c/Users/
ls ~/01_mldata
```

---

## ✅ 推荐工具组合（每人至少配置）

| 工具               | 用途                      |
| ---------------- | ----------------------- |
| Cursor           | 支持 AI 自动补全和 Notebook 编辑 |
| micromamba       | 轻量级环境管理器                |
| Jupyter Notebook | 可嵌入 Cursor 接口的计算服务      |
| WSL2 + Ubuntu    | 强大的 Linux 终端和软件支持       |

---

## 📌 附加建议

* **不要直接用 Git 管理 mzML/raw 等大文件**，用 `.gitignore` 忽略；
* 每次 Notebook 运行前 `os.getcwd()` / `os.chdir()` 明确当前目录；
* 熟练掌握路径 `/mnt/c/...` 与 `/home/zixuan/...` 的区别和切换方式；
* 项目正式运行时，统一数据、脚本、模型放在 Linux 下。

---

