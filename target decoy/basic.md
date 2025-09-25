# 相关文献
https://www.cell.com/fulltext/S0092-8674(16)30849-2

https://www.nature.com/articles/s41467-023-37462-4

https://pubs.acs.org/doi/10.1021/acs.jproteome.3c00085

---

在蛋白质组学里说的 **“target experiment”**，基本就是跟 **target–decoy strategy** 挂钩的。

### 背景

* 在蛋白质组学（尤其是质谱鉴定）里，我们需要控制 **假发现率 (FDR)**。
* 常用的方法就是 **target–decoy approach (TDA)**：

  * **Target database**：真实的蛋白质/肽段数据库（例如 Uniprot 物种 FASTA）。
  * **Decoy database**：人为生成的“假”序列（通常是把 target 蛋白序列反向或打乱）。

### Target experiment 的含义

* 当我们用 **target database** 去跑搜索引擎（Mascot, MaxQuant, DIA-NN, Spectronaut…），这次搜索就是 **target experiment**。
* 同时，我们也会用同样的数据去搜 decoy database（或混合数据库），得到 **decoy experiment**。
* 两者结果放在一起比较，能估计随机匹配带来的假阳性率，从而给出可靠的 FDR 控制。

### 举个例子

* 你有 1000 个 PSM（肽谱匹配）进了 target，30 个进了 decoy。
* 假设 decoy hits 可以当作随机匹配的 proxy，那就可以推算出 target 里大概有多少是假的，从而给 target 结果设一个 q-value cutoff。

👉 所以在蛋白质组学语境下，**target experiment = 用真实蛋白数据库搜索得到的实验结果**，用来跟 decoy 对照。

---

好嘞～这个在蛋白质组学里是超级经典的套路，我给你拆开讲清楚：

---

## 🎯 1. 为什么要有 target–decoy strategy？

在蛋白质组学里，我们用搜索引擎（Mascot, MaxQuant, DIA-NN, Spectronaut 等）把 MS/MS 光谱跟理论肽段做匹配。
问题是：**随机匹配** 也能打到一些“高分”，那我们怎么知道哪些是 **真 hits**，哪些只是噪声？
→ 需要 **FDR (False Discovery Rate)** 控制。

---

## 🧩 2. Target–decoy 的核心思想

* **Target database**：真实的蛋白质序列库（比如人类 Uniprot FASTA）。
* **Decoy database**：人为制造的“假”库，一般是 target 里每条蛋白反向或随机打乱。
* **合并搜索**：把 target 和 decoy 拼在一起，用同样的数据跑一遍。

👉 逻辑：

* 光谱匹配到 target → 可能是真肽段，也可能是假阳性。
* 光谱匹配到 decoy → **一定是假阳性**（因为 decoy 不存在于样本）。

这样就能通过 decoy hit 的数量来估计 **随机匹配的背景噪声水平**。

---

## 📊 3. 如何估算 FDR？

假设你设一个打分阈值，得到：

* **Target hits** = 1000
* **Decoy hits** = 20

那么：

$$
\text{Estimated FDR} \approx \frac{\text{Decoy hits}}{\text{Target hits}} = \frac{20}{1000} = 2\%
$$

常见的 cutoff 就是 FDR ≤ 1% 或 5%。
在实际实现中还会做 **q-value 计算**（逐个 PSM/肽段累积估算 FDR）。

---

## ⚙️ 4. 实际流程

1. **构建数据库**：把 target FASTA + decoy FASTA 拼起来。
2. **数据库搜索**：光谱 vs target+decoy 一起比。
3. **打分排序**：每个匹配都有一个 score。
4. **统计 decoy**：不同阈值下，数一数 decoy hits。
5. **设定阈值**：保证 FDR ≤ 1% → 留下可靠的鉴定结果。

---

✨ 总结：
**Target–decoy strategy = 通过在数据库里加入假序列，用 decoy 命中的比例来估计假发现率，从而对 target hits 做 FDR 控制。**

---

## 1️⃣ Human SRMAtlas (Kusebauch et al., *Cell*, 2016)

**讲了什么**

* 建立了 **SRMAtlas**：覆盖 99.7% 人类蛋白（20,277 个蛋白，166,174 条蛋白特异性肽段）。
* 每个蛋白至少有几个验证过的 **proteotypic peptides**，并配有高分辨 MS/MS 光谱、碰撞能量 (CE) 优化数据、保留时间 (iRT)。
* 提供了一个 **公开数据库 (srmatlas.org)**，科研人员可以直接拿来做 targeted proteomics (SRM/MRM/PRM)。

**对你项目的帮助**

* 给你 **target 肽段的黄金标准**。你要建 workflow，少不了确定 target 库，这篇文章等于把“全人类蛋白靶点坐标”都给你准备好了。
* 你可以用 SRMAtlas 里的肽段作为 **benchmark**，对比你 pipeline 的识别/定量表现。
* 还能帮你 **挑 proteotypic peptides** 来建 targeted assay（不必从零开始）。

---

## 2️⃣ Real-Time Spectral Library Matching (McGann et al., *JPR*, 2023)

**讲了什么**

* 提出了 **RTLS (Real-Time Library Search)**：在采集过程中，质谱仪实时把采到的 MS2 光谱和光谱库比对，用毫秒级反馈决定要不要采 MS3 / SPS。
* 应用在 **TMT 多重定量**里，能提升定量精度，减少缺失值。
* 比传统 RTS (Real-Time Database Search) 更快更准，尤其在 **复杂或嵌合光谱 (chimeric spectra)** 情况下。
* 实验表明：RTLS 在同样梯度时间下能检测到 **多 15% 的显著调控蛋白**。

**对你项目的帮助**

* 给你 **workflow 里的智能采集模块思路**。传统 target workflow 靠预定义肽段，RTLS 让采集更灵活高效。
* 如果你要结合 **TMT + target strategy**，这篇就是 cutting-edge 技术支撑，能解释你为什么要在 pipeline 里考虑 **real-time matching**。
* 它也提示你：spectral library（不管是 SRMAtlas 的经验库还是 Prosit 的预测库）都是 workflow 的核心资源。

---

## 3️⃣ PepQuery2 (Wen & Zhang, *Nat Commun*, 2023)

**讲了什么**

* 提供了 **PepQuery2**：一个 peptide-centric 查询工具。
* 支持在 **10 亿条以上公共 MS/MS 光谱**中快速搜索（来自 PRIDE、MassIVE、iProX、jPOSTrepo 等），像 BLAST 查 DNA 一样。
* 应用场景包括：

  * 验证基因组预测的新肽段（突变肽、splicing、missing proteins）。
  * 给 target proteomics 选 **proteotypic peptides**。
  * 独立验证小规模实验结果，降低假阳性。
* 优势是比传统的 target-decoy / 全局搜索更快更直接，适合“我要查某个肽到底有没有被别人实验测到”。

**对你项目的帮助**

* 给你 **验证环节的工具**。你建立 target workflow，不仅要能自己跑数据，还要能 **跨数据库验证**：

  * 你挑的 target 肽段在公共数据里有没有出现过？
  * 你的 workflow 里新识别的变异肽是不是可信？
* PepQuery2 就是一个 **external validation module**，能让 workflow 更扎实。

---

## 🌐 整体串起来

* **SRMAtlas** = 给你 target 库（靶点肽段、参考光谱）。
* **RTLS** = 给你采集策略（实时触发、定量更准）。
* **PepQuery2** = 给你验证工具（跨公共库快速确认）。

→ 三篇文章合起来，就是一个从 **资源 → 采集 → 验证** 的闭环。你可以跟老师说：

> “我准备把 SRMAtlas 作为 target 资源库，用 RTLS 提升采集效率，再用 PepQuery2 做 cross-dataset 验证，这样 workflow 就覆盖了从靶点定义到结果确认的全过程。”

---

好问题！🌟 “proteotypic peptides”（常缩写 **PTPs**）是蛋白质组学里一个非常关键的概念。

---

## 🔑 定义

**Proteotypic peptides** = 一类在质谱实验里 **最容易被检测、并且能唯一标识某个蛋白** 的肽段。

* **唯一性 (uniqueness)**：这段肽序列只出现在某一个蛋白里，不会和其他蛋白共享 → 所以检测到它，就能确定是这个蛋白。
* **可检测性 (detectability)**：在 LC-MS/MS 里它稳定、容易电离、碎片离子丰富 → 所以每次实验都能比较可靠地被检测出来。

---

## ⚙️ 怎么选？

研究人员通常会根据以下特征来挑 proteotypic peptides：

1. **长度适中**（7–20 个氨基酸左右）。
2. **不包含麻烦氨基酸**（比如易修饰的 M, N, Q, C 等，容易氧化/脱酰胺/修饰）。
3. **电离效率高**（带正电荷多，碱性强）。
4. **碎裂模式清晰**（MS/MS 出现丰富的 b/y 离子）。
5. **不落在跨膜区或极度疏水的区域**。

---

## 🧪 为什么重要？

* **Targeted proteomics (SRM/MRM/PRM)**：你要定量一个蛋白，通常挑 2–3 个最好的 proteotypic peptides 作为 “代表”。
* **光谱库构建**：像 Human SRMAtlas 这种资源，核心就是为每个蛋白挑选了一组 PTPs，并且生成高质量的参考光谱。
* **避免假阳性**：如果你用的肽段不是 proteotypic，而是 “shared peptides”（多个蛋白共有），那你根本没法确定到底是哪个蛋白。

---

## 🎯 举个例子

假设你要测 **GAPDH** 蛋白。

* 它酶切后可能有几十个肽段，但不是每个都能稳定测到。
* 经过经验和计算筛选，可能有 3–4 个肽段（比如 *VPTANVSVVDLTCR*）几乎总能在 LC-MS/MS 里被检测到，并且只属于 GAPDH。
* 这些就是 **proteotypic peptides**。你就拿它们做定量。

---

✨ 总结：
**Proteotypic peptides = 蛋白质在质谱实验里的 “指纹肽”**。
它们是 target proteomics workflow 的核心构件：你用它们来确保结果既 **可靠检测** 又 **特异归属**。

---



