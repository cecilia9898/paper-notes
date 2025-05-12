# MultiNotch MS3: 高灵敏度多重定量的蛋白质组学新纪元

---

## 📌 **背景与目的**
传统的质谱（MS）定量蛋白质组学使用的是 **isobaric chemical tags**（如 TMT 和 iTRAQ），这些标签允许在多路复用（multiplexing）下进行多样本比较。然而，在 MS2 层级的碎裂过程中，**干扰信号（interfering species）** 会引入误差，导致定量的失真。  
**MultiNotch MS3** 方法提出了一种新策略：通过 **Synchronous Precursor Selection (SPS)** 技术，在 MS3 阶段同时隔离并碎裂多个 MS2 片段，从而提高信号强度并减少干扰。

---

## 🔬 **研究方法**
### 1️⃣ **SPS 技术的实现**
- MultiNotch MS3 使用多频率凹槽（multi-frequency notches）的隔离波形，同时隔离多个 MS2 片段，并将它们一同进行 MS3 的碎裂。
- 显著提升了 TMT 报告离子（reporter ion）的强度，同时降低了 MS2 阶段的信号损耗。

### 2️⃣ **实验设计**
- 使用了两种模式：
  - **Two-Proteome Interference Model**：将酵母（Yeast）和人类细胞（HeLa）蛋白质混合，验证 MultiNotch MS3 的干扰去除效果。
  - **8-Plex Colorectal Cancer Cell Lines**：分析了 8 种不同的结直肠癌细胞系，通过三次生物重复来评估蛋白质表达差异。

### 3️⃣ **质谱分析**
- 所有 LC-MS 实验在 **Velos-Orbitrap Elite mass spectrometer** 上完成，结合高分辨率的液相色谱分离和 MS3 质谱分析。
- SPS 技术显著提高了 MS3 谱图中 TMT 报告离子的数量，大约是传统 MS3 方法的 **10 倍**。

---

## 📊 **结果分析**
### 1️⃣ **信号强度与准确性提升**
- MultiNotch MS3 方法显著减少了干扰离子的贡献，恢复了 TMT 报告离子的真实比例。
- 在两种蛋白质模型中，MultiNotch MS3 的 TMT 报告离子信号强度显著高于传统 MS3 方法。

### 2️⃣ **蛋白质鉴定与定量**
- 在 **结直肠癌细胞系** 的大规模分析中，共定量到 **8,378 个蛋白质**，其中 **6,168 个蛋白质** 在所有三次生物重复中都有可靠定量。
- 这些数据在多路复用 8 个 TMT 通道下，共产生 **172,704 个定量比值**，显示出超高的定量通量。

### 3️⃣ **数据重现性与统计显著性**
- 蛋白质表达比值在三次生物重复间具有高度的重现性（Pearson 相关系数 **0.86**）。
- 使用 ANOVA 分析，并结合 Benjamini-Hochberg 校正，发现 **4,107 个蛋白质** 在不同的癌细胞系中表现出统计显著差异。

---

## 🔍 **生物学意义**
### 1️⃣ **癌症相关蛋白的表达分析**
- 例如 **EGFR** 和 **MSH6**，在不同细胞系中的表达模式反映了已知的肿瘤生物学。
- **KRAS** 基因的突变（G13D）在四个细胞系（DLD-1, HCT-116, HCT-15, Lovo）中被可靠地检测到。

### 2️⃣ **细胞信号通路的富集**
- PCA 分析区分了高突变（hypermutated）和非高突变（nonhypermutated）的细胞系。
- **SMAD4** 和 **IDH2** 的表达被分别作为高突变和非高突变组的特征标志。

### 3️⃣ **新的发现**
- 研究发现高表达的 **IDH2** 是非高突变型细胞系的标志，表明其在结直肠癌中的潜在作用。

---

## ✅ **结论**
**MultiNotch MS3** 显示出：
- **高通量多重定量** 的能力；
- **去除干扰信号** 的显著改进；
- **数据准确性和灵敏度** 的大幅提升；
- **大规模癌症蛋白质谱分析** 中的优越表现。

该技术成功地解决了 MS2 方法中固有的干扰问题，并在不损失灵敏度的情况下，显著增强了 TMT 报告离子的强度，为大规模蛋白质组学的定量研究提供了新的解决方案。

---

## 🔎 **需要深入分析的图表**
- **Figure 1**：展示了不同 MS 方法（MS2, MS3, MultiNotch MS3）对 TMT 报告离子信号的影响。
- **Figure 3**：对比了三种方法对酵母-人类两种蛋白质混合物的定量结果。
- **Figure 4**：描述了 8 种结直肠癌细胞系的生物学重复分析，揭示了显著差异的蛋白质。
- **Figure 5**：通过生物学网络映射，展示了与结直肠癌相关的信号通路差异。

---
# MultiNotch MS3: 高灵敏度多重定量的蛋白质组学新纪元

---

## 📌 **背景与目的**
传统的质谱（MS）定量蛋白质组学使用的是 **isobaric chemical tags**（如 TMT 和 iTRAQ），这些标签允许在多路复用（multiplexing）下进行多样本比较。然而，在 MS2 层级的碎裂过程中，**干扰信号（interfering species）** 会引入误差，导致定量的失真。  
**MultiNotch MS3** 方法提出了一种新策略：通过 **Synchronous Precursor Selection (SPS)** 技术，在 MS3 阶段同时隔离并碎裂多个 MS2 片段，从而提高信号强度并减少干扰。

---

## 🔬 **研究方法**
### 1️⃣ **SPS 技术的实现**
- MultiNotch MS3 使用多频率凹槽（multi-frequency notches）的隔离波形，同时隔离多个 MS2 片段，并将它们一同进行 MS3 的碎裂。
- 显著提升了 TMT 报告离子（reporter ion）的强度，同时降低了 MS2 阶段的信号损耗。

### 2️⃣ **实验设计**
- 使用了两种模式：
  - **Two-Proteome Interference Model**：将酵母（Yeast）和人类细胞（HeLa）蛋白质混合，验证 MultiNotch MS3 的干扰去除效果。
  - **8-Plex Colorectal Cancer Cell Lines**：分析了 8 种不同的结直肠癌细胞系，通过三次生物重复来评估蛋白质表达差异。

### 3️⃣ **质谱分析**
- 所有 LC-MS 实验在 **Velos-Orbitrap Elite mass spectrometer** 上完成，结合高分辨率的液相色谱分离和 MS3 质谱分析。
- SPS 技术显著提高了 MS3 谱图中 TMT 报告离子的数量，大约是传统 MS3 方法的 **10 倍**。

---

## 📊 **结果分析**
---

### 🔎 **Figure 1：MultiNotch MS3 信号增强与准确性提升**
- **Panel A**：展示了实验设计。  
    - 酵母（Yeast）蛋白质使用 LysC 消化，并且用 TMT 进行标记，混合比例是 `10:4:1:1:4:10`。
    - HeLa 蛋白质的混合比例是 `1:1:1:0:0:0`。
    - 这些样品被组合后进入 MS 分析。

- **Panel B**：比较了三种 MS 方法对相同目标肽段的检测：
    1. **MS2**：使用 HCD（High-energy collision-induced dissociation）碎裂，信号明显受干扰离子影响，导致 `10:1` 比例被拉低到接近 `5:1`。
    2. **标准 MS3**：使用 CID（Collision-induced dissociation）对 MS1 片段进行二级碎裂，然后随机选一个 MS2 片段进行三次碎裂（HCD）。结果是低丰度的通道几乎检测不到。
    3. **MultiNotch MS3**：对 **多个 MS2 片段** 同时进行三次碎裂，恢复了低丰度通道的信号，而且准确地还原了原始的 `10:1` 的比例。

- **Panel C**：展示了三种方法在 MS3 级别中前体离子的丰度分布：
    - 标准 MS3 只捕获一个 MS2 离子，而 MultiNotch MS3 捕获了 **6 个 MS2 离子**，大大增加了后续 TMT 信号的强度。

---

### 🔎 **Figure 2：MultiNotch MS3 的前体捕获效率分析**
- **Panel A**：展示了对特定离子（m/z = 989）进行不同隔离窗口（notch width）设置时的捕获效率。  
    - 通过调节 isolation notch 的宽度和位置，可以显著改善离子的捕获效率。
    - 随着 `q-value` 的变化，最佳的 isolation notch 位置和宽度会改变。

- **Panel B**：展示了 MultiNotch MS3 对总前体信号的捕获能力：
    - 捕获 **10 个 MS2 片段**，能够保留大约 **40% 的 MS2 总信号**，而标准 MS3 只能保留 **5%**。
    - 最终，在 MS3 前体中，MultiNotch MS3 能够保持大约 **25% 的 MS1 总信号**，相比传统方法有巨大提升。

---

### 🔎 **Figure 3：两种蛋白质模型的多通道定量**
- **Panel A**：展示了三种方法对 TMT 报告离子的总数对比。
    - MS2 平均检测到 **3100 个 reporter ions**。
    - 标准 MS3 只能检测到 **200 个 reporter ions**。
    - MultiNotch MS3 达到了 **2500 个 reporter ions**，接近 MS2 的水平。

- **Panel B**：对比了三种方法对酵母与人类蛋白混合模型的定量准确性：
    - TMT 比例分别是 `10:1`, `4:1` 和 `1:1`。
    - **MS2 方法**：显著失真，10:1 被拉低到 5:1；4:1 被拉低到 2:1；1:1 扭曲为 2:1。
    - **标准 MS3**：尽管恢复了比例，但低丰度信号丢失严重。
    - **MultiNotch MS3**：几乎完美恢复了所有比例，且低丰度信号清晰可见。

---

### 🔎 **Figure 4：8 种结直肠癌细胞系的多重定量**
- **Panel A**：展示了实验设计：
    - 8 种结直肠癌细胞系的生物学三次重复。
    - 使用 MultiNotch MS3 进行 3 小时的 LC-MS3 分析。

- **Panel B**：两个生物学重复间的定量比值的散点图：
    - 显示出高度的相关性（Pearson 相关系数 `0.86`），代表多路复用后的定量结果在生物学重复间非常稳定。

- **Panel C**：使用 ANOVA 分析，鉴定出 **4,107 个显著差异表达的蛋白质**。

- **Panel D & E**：描述了结直肠癌相关蛋白（EGFR, MSH6 和 KRAS）的表达差异，并且能够清楚分辨 KRAS 的突变（G13D）。

---

### 🔎 **Figure 5：功能通路富集与分型分析**
- **Panel A**：癌症相关基因的显著差异表达情况（如 Chromatin Modification、PI3K、RAS 信号等）。
- **Panel B**：ReactomeFI 映射，揭示与结直肠癌生物学密切相关的模块。
- **Panel C**：主成分分析（PCA）区分了高突变（hypermutated）和非高突变（nonhypermutated）的细胞系。
- **Panel D, E & F**：SMAD4 和 IDH2 的差异表达揭示了新的生物标志物和潜在癌症驱动基因。

---

