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
这张截图是 **DIA-NN 的运行日志**，它把你设置的参数和当前分析进度打印出来了
---

## 🔍 参数设置确认

* **Min peptide length set to 7 / Max peptide length set to 30**
  → 只考虑长度在 7–30 氨基酸之间的肽段。

* **Min precursor m/z 300 / Max precursor m/z 1800**
  → 质荷比范围 300–1800。

* **Precursor charge set to 1–4**
  → 只考虑 1 到 4 电荷态的前体离子。

* **In silico digest will involve cuts at K\*,R\***
  → 胰蛋白酶切割规则（在 K, R 之后切，除非后面是 P）。

* **Maximum number of missed cleavages = 1**
  → 允许最多一个漏切。

* **Cysteine carbamidomethylation = fixed**
  → 固定修饰：半胱氨酸烷基化。

* **Oxidation of M (UniMod:35, +15.9949 Da) = variable**
  → 可变修饰：甲硫氨酸氧化。

---

## ⚠️ 警告信息

* **WARNING: incorrect settings … library must be generated … without activating FASTA digest**
  → 提示：如果你想单独生成一个谱库（不跑数据），需要在 pipeline 分两步来做。现在你同时开了 “FASTA digest + 生成谱库”，它会先跑 library-free 模式再用。⚡这个可以忽略，你这样设置是合理的。

* **WARNING: peptidoform scoring enabled …**
  → 因为有 variable mod（氧化），DIA-NN 会自动打开肽形式评分。正常现象。

* **WARNING: MBR turned off, at least two files are required**
  → 你只有 1 个文件，MBR（Match Between Runs）没法启用 → 所以 DIA-NN 自动关闭了。✔ 不是错误。

---

## ✅ 实际进度

* **Loading FASTA** → 读取 `three_species.fasta`。
* **Processing FASTA** → 胰酶切肽段。
* **Assembling elution groups** → 生成肽段的洗脱组。
* **7707052 precursors generated** → 预测出了 \~770 万个前体离子（肽段 + 电荷组合）。
* **Library contains 31080 proteins, 30103 genes** →

  * 库里总共有 \~31k 蛋白，\~30k 基因
  * 这和文章里说的 “benchmark database \~31,657 sequences” 基本一致！👏
* **Encoding peptides for spectra and RTs prediction / Predicting spectra and IMs** → 正在用深度学习预测碎片谱、保留时间、离子迁移度。

---

## 🎯 总结

这段日志说明：

1. 你的 **FASTA 加载正确**（three\_species.fasta 生效了）。
2. **谱库已经生成**（包含 \~31k 蛋白，符合预期）。
3. MBR 自动关闭是因为只有 1 个样本 → 不影响结果。
4. DIA-NN 正在跑 **library-free 模式**，预测谱图、RT、IM。

换句话说：**流程走通了，结果会正常出来** 🎉

---

