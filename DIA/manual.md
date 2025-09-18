## ✅ 接下来要勾选的设置

### 1. Precursor ion generation

* ✔ **FASTA digest for library-free search / library generation**
* ✔ **Deep learning-based spectra, RTs and IMs prediction**
* Protease: **Trypsin/P**
* Missed cleavages: **1**（默认就行）
* Modifications: **C carbamidomethylation** (固定) + **Ox(M)** (可变)

---

### 2. Algorithm 区

* **MBR**：✔
* **Protein inference**：✔
* **Machine learning**：NNs (cross-validated)
* **Quantification strategy**：QuantUMS (high precision)
* **Cross-run normalization**：RT-dependent

---

### 3. Output 区

* ✔ **Quantities matrices**（这样会输出 `protein_matrix.tsv` 等矩阵）
* **Main output**：建议设置成一个单独文件夹，例如

  ```
  C:\Users\cecilia\Documents\DIA_out\PXD046444\
  ```
* ✔ **Generate spectral library**（DIA-NN 会生成一个预测谱库 `.parquet` 文件）

---

### 4. Run

* 点击 **Run** 按钮开始运行（不要点 Execute）。
* DIA-NN 会在输出文件夹里生成：

  * `report.tsv`（所有识别结果）
  * `protein_matrix.tsv`
  * `gene_matrix.tsv`
  * `report-lib.parquet`（生成的预测库）

---

## 1. Precursor ion generation

* **FASTA digest for library-free search** ✅
  因为你提供了 `three_species.fasta`，DIA-NN 会**体外模拟胰蛋白酶消化**，生成肽段列表。这样就能在没有实验谱库的情况下自己预测一个 *in-silico spectral library*。
  👉 如果不勾，DIA-NN 不会用 FASTA，数据就对不上。

* **Deep learning-based spectra, RTs and IMs prediction** ✅
  DIA-NN 内置了一个深度学习模型（类似 Prosit），可以预测肽段的碎片谱、保留时间 (RT)、离子迁移度 (IM)。这比传统的理论打分准确很多。
  👉 勾上它会显著提高识别数和定量精度，尤其是三物种混合这种复杂样本。

* **修饰**：

  * `C carbamidomethylation`（固定修饰）：因为你做的实验里用 iodoacetamide/CAA 修复半胱氨酸 → 这一步是必须的。
  * `Ox(M)`（可变修饰）：甲硫氨酸氧化是最常见的生物学/实验修饰。默认加上。

---

## 2. Algorithm

* **MBR (Match Between Runs)** ✅
  如果你有多个 DIA 样本（比如不同注射），MBR 可以把在一个 run 里识别到的肽段迁移到另一个 run，提升识别覆盖度。

* **Protein inference** ✅
  这个选项能让 DIA-NN 把肽段级别的信息组装回蛋白质，输出蛋白矩阵。

* **Machine learning: NNs (cross-validated)** ✅
  DIA-NN 内部用神经网络模型区分真实肽段 vs 背景噪声。Cross-validated 方式更稳健，避免过拟合。

* **Quantification strategy: QuantUMS (high precision)** ✅
  QuantUMS 是作者在 DIA-NN v2 引入的新算法，针对 **DIA 的峰形建模**，能提供更高的定量精度，特别适合你这种 benchmark 数据。

* **Cross-run normalization: RT-dependent** ✅
  用保留时间对齐做归一化，比简单的强度归一化稳健，减少系统偏差。

---

## 3. Output

* **Quantities matrices** ✅
  勾上后会自动生成 `protein_matrix.tsv`、`gene_matrix.tsv`，方便你直接比较三物种的相对丰度。

* **Generate spectral library** ✅
  即使你现在不打算建库，生成一个 `.parquet` 谱库也方便以后做分析（比如和其他 DIA 文件对比）。

---

## 🎯 总结

这些设置的核心逻辑是：

1. **用 FASTA + deep learning** → 自动生成高质量谱库（library-free 模式）。
2. **用 ML + QuantUMS** → 在复杂 DIA 光谱里最大限度识别三物种的肽段。
3. **生成矩阵 + 库** → 后续方便定量和验证。

所以这些不是随便点，而是针对你这个数据类型（标准三物种 mix）+ DIA-NN 的优势做的合理配置。

---
