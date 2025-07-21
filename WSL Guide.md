# Proteomics 课题组：Windows + Linux (WSL) 混合开发实用指南

本指南总结了在 Windows 电脑上使用 Linux 终端（WSL）进行蛋白质组子经学开发、分析、Notebook 使用、数据管理等常见任务的最佳实践，适合 Jupyter + Python + Snakemake 等常见生信/计算工作流。

---

## 为什么要用 WSL？好处是什么？

WSL（Windows Subsystem for Linux）是 Windows 官方提供的一种方式，允许你在 Windows 上运行原生 Linux 环境。很多蛋白质组学的软件都基于windows，但是对于软件开发和数据处理，Linux绝对是更好的选择，所以对于我们组来说，用windows和Linux混合开发是绝对更好的选择。

### 用 WSL 而不是只用 Windows 的好处：

| 好处                  | 说明                                                         |
| ------------------- | ---------------------------------------------------------- |
| **更好支持科研工具链**       | 很多生信工具（如 Snakemake、ProteoWizard CLI、Pyteomics）原生为 Linux 开发 |
| **无需双系统 / 重启切换**    | 在 Windows 下就能直接用 Linux 工具，避免重启或装虚拟机                        |
| **命令行更强大、包管理更灵活**   | 比 Windows 的 CMD/Powershell 更适合科研开发、批处                      |
| **兼容远程服务器 / 代码一致性** | Linux 命令和路径风格与集程、高性能服务器一致，迁移代码更方便                          |
| **性能优秀**            | WSL2 使用虚拟硬盘，支持原生 Linux 内核，IO 性能大大优于 Docker 或 Git Bash      |
| **能访问 Windows 文件**  | `/mnt/c/...` 可直接访问 Windows 文件，也可拷贝数据进 Linux                |

---

## 1. 基本环境构建

### 使用 WSL + Ubuntu

* 推荐安装最新版的 WSL+ Ubuntu
* WSL:https://ubuntu.com/desktop/wsl
* 或者 microsoft store 搜索 Ubuntu 24.04.1 LTS

---

## 2. 安装与使用 micromamba 环境（推荐替代 conda）

### 安装 micromamba(具体参见另一个笔记)

https://mamba.readthedocs.io/en/latest/installation/micromamba-installation.html

---

## 3. Notebook 使用推荐：Cursor + Jupyter 服务器分离式连接

### 方法：在 Linux（WSL）内启动 Jupyter Server，然后在 Cursor 中连接

```bash
micromamba activate mldata_env
jupyter notebook --no-browser --port=8888
```

复制输出的链接（如 `http://localhost:8888/?token=xxxx`），
然后在 **Cursor → Select Kernel → Enter an existing Jupyter Server** 中粘贴即可。

---

## 4. 启动 Jupyter 的自动化建议

### 推荐方式（已验证稳定）：

在 `~/.bashrc` 中添加：

```bash
# ~/.bashrc

# === Debug: 确认 .bashrc 是否运行 ===
echo "✅ .bashrc is running"

# Enable bash completion
if [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
fi

# Add local bin to PATH
export PATH="$HOME/bin:$PATH"

# === micromamba initialize ===
# !! Contents within this block are managed by 'micromamba shell init' !!
export MAMBA_EXE='/home/zixuan/bin/micromamba'
export MAMBA_ROOT_PREFIX='/home/zixuan/.local/share/mamba'
__mamba_setup="$("$MAMBA_EXE" shell hook --shell bash --root-prefix "$MAMBA_ROOT_PRE>if [ $? -eq 0 ]; then
    eval "$__mamba_setup"
else
    alias micromamba="$MAMBA_EXE"  # fallback
fi
unset __mamba_setup
# === end micromamba initialize ===

# === Auto-start Jupyter notebook in WSL ===
if [[ "$WSL_DISTRO_NAME" ]]; then
    echo "📢 Detected WSL: $WSL_DISTRO_NAME"
    if ! pgrep -f "jupyter-notebook" > /dev/null; then
        echo "🚀 Launching Jupyter Notebook with mldata_env..."
        (micromamba activate mldata_env && nohup jupyter notebook --no-browser --por>    else
        echo "🔄 Jupyter is already running"
    fi
fi
```

每次打开 WSL 自动启动 Jupyter，以及我自己已经新建立好的环境 mldata_env(你可以先建你自己需要的环境然后把它写入 ~/.bashrc)，无需手动输入。

---

## 5. Windows 与 Linux 文件系统互通说明

### 关键原则：避免频繁从 WSL 直接读写 `/mnt/c/...` 路径中的大文件

| 操作                      | 推荐方式                                 |
| ----------------------- | ------------------------------------ |
| 项目开发（脚本、代码、模型）          | 保存在 `/home/zixuan/your_project`      |
| 原始数据（.csv, .mzML）       | 从 Windows 拷贝进 Linux 路径中处理            |
| 从 Windows 拷贝文件到 Linux   | 使用命令： `cp -r /mnt/c/... ~/your_path` |
| 从 Linux 访问 Windows 桌面文件 | 路径格式： `/mnt/c/Users/用户名/Desktop/`    |

---

## 6. 使用 Snakemake / DIA-NN / Proteomics 脚本建议

* 在 Linux 端运行 Snakemake 工作流性能更优；
* 推荐将 `.raw` → `.mzML` 文件转换步骤提前完成并放入 Linux 目录；
* 若 Windows 生成的数据过大，推荐定期复制进 WSL 并删除冒余。

---

## 7. 常见命令总结

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

## 推荐工具组合

| 工具               | 用途                      |
| ---------------- | ----------------------- |
| Cursor           | 支持 AI 自动补全和 Notebook 编辑 |
| micromamba       | 轻量级环境管理器                |
| Jupyter Notebook | 可嵌入 Cursor 接口的计算服务      |
| WSL2 + Ubuntu    | 强大的 Linux 终端和软件支持       |

---

