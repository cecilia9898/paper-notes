FAIMS 的全称是 **Field Asymmetric Ion Mobility Spectrometry**，中文一般叫“场不对称离子迁移谱”。它是可以装在质谱前端的一个接口（像个“滤波器”），用来在离子进入质谱之前先根据气相迁移率（mobility）进行一次额外分离。

### 简单理解

* **常规质谱**：色谱柱 → 电离 → 直接进质谱。
* **带 FAIMS 的质谱**：色谱柱 → 电离 → **FAIMS 过滤一遍**（电场让不同迁移率的离子在不同补偿电压下通过） → 进质谱。

这样就像在质谱前多加了一道门禁卡，只有符合条件的离子能进来。

### 为什么用 FAIMS？

1. **降低化学噪音**：去掉背景或低复杂度的离子 → 提升信噪比。
2. **选择性分析**：通过调节补偿电压（CV，比如文章里用 −50 V），只允许特定迁移特性的离子通过。
3. **提高灵敏度**：特别是在低样本量时（比如稀释梯度实验），能更好地检测到低丰度肽段。

### 在 DIA 实验里

* DIA 已经是全扫描碎裂，信息复杂度很高。加上 FAIMS，可以减少干扰信号，让 DIA 的定量更精准。
* 不同 CV 下的实验也可以组合起来，增加覆盖度。

---

# 💻 普通科研电脑 vs 🖥️ UMass Chan SCI Cluster

| 配置 | 普通科研电脑 | UMass Chan SCI Cluster |
|------|--------------|-------------------------|
| **CPU（核心数）** | 4–8 核 (i7/i9/AMD Ryzen) | 每节点 40 核 / 128 核，总共几千核 |
| **内存（RAM）** | 16–32 GB | 380 GB / 500 GB |
| **GPU** | 一张消费级显卡（RTX 3060 / 3080，6–12 GB 显存） | V100 (32 GB) ×40 张 + A100 (40 GB) ×4 张 |
| **存储** | 512 GB–2 TB SSD | 超过 2 PB（2000 TB）高速存储 |
| **操作系统** | Windows / macOS / Linux | Red Hat Enterprise Linux 8 |
| **网络** | 家用/实验室局域网 | Infiniband + 高速以太网（节点间低延迟） |
| **任务调度** | 自己盯着跑，满了就卡死 | IBM LSF 自动排队、分配资源 |
| **适合的任务** | 小规模数据分析，少量深度学习 | 超大规模质谱数据、并行仿真、大模型训练 |

---

## 🎯 一句话总结
- **科研电脑**：个人单兵作战工具，适合轻量分析。  
- **SCI Cluster**：科研界“数据工厂”，能帮你同时跑成百上千个任务，还能带你玩转深度学习和超大数据。

---
好问题！你看到的 `.parquet` 文件，其实是一种 **列式存储的表格数据格式**，全名叫 **Apache Parquet**。

---

## 🗂️ 什么是 `.parquet`？

* 类似于 `.csv` 或 `.tsv`，本质就是 **表格**。
* 但它不是一行行存的，而是 **按列压缩存储**，这样：

  * **读写快**（尤其是只需要某几列时）；
  * **文件小**（压缩率高）；
  * **兼容大数据生态**（Hadoop、Spark、Arrow 都支持）。

---

## 🔬 在 DIA-NN 里的 `.parquet`

* DIA-NN 的新版默认输出结果（比如 `report.parquet`、`report-lib.parquet`）就是这种格式。
* 它包含的内容和 `.tsv` 类似（比如识别到的 peptide、precursor、protein、强度等），只是换了个更现代的存储方式。

---

## 🛠️ 怎么打开？

* **Python / Jupyter**：

  ```python
  import pandas as pd
  df = pd.read_parquet("astral_20250922_report.parquet")
  print(df.head())
  ```
* **R**：

  ```R
  library(arrow)
  df <- read_parquet("astral_20250922_report.parquet")
  head(df)
  ```
* **命令行**（Linux）：

  ```bash
  parquet-tools head astral_20250922_report.parquet
  ```
* **Excel**：不能直接打开，需要先转成 `.csv`：

  ```python
  df.to_csv("report.csv", index=False)
  ```

---

## 📊 小结

* `.parquet` = 压缩过的高性能表格文件。
* DIA-NN 输出的 `.parquet` **= 你要分析的识别/定量结果表**。
* 打开方式：推荐用 **pandas (Python)** 或 **arrow (R)**。

---

