
# 目标（What）

在 **MS2 层**做一个“on-the-fly FDR gating”的**离线算法**：
对每条 MS2 光谱与同窗（isolation window）内的 **target** 参考谱与 **decoy** 参考谱进行快速比对，
得到分数差 **Δ = S_target − S_decoy**，并结合 target 本身分数阈值，判断该谱是否“通过 gate”（值得触发/保留、进入下游）。

> 这不是必须实时跑在仪器上的版本；先做**离线筛选器**验证价值，再考虑实时化。

---

# 场景与价值（Why）

* **适用场景**：TMT-SPS-MS3（MS3 时间宝贵）或任何你想在 MS2 就“早筛”掉伪匹配的工作流。
* **潜在收益**：

  1. 省出扫描预算 → 优先投给**低丰度 target**；
  2. 降低共分离/串扰 → **缺失率↓、CV↓**；
  3. 给后续 FDR 提供更干净的候选集。

---

# 输入/输出（I/O）

## 输入

1. **Observed MS2**（每条谱）：

   * `scan_id`、`window_start`、`window_end`
   * `peaks_mz[]`、`peaks_intensity[]`
2. **Target/Decoy 参考库**（谱库或理论离子表）：

   * 每个候选包含：`peptide`、`charge`、`precursor_mz`
   * `ion_mz[]`、`ion_intensity[]`、`ion_type[]`（如 b/y、是否中性丢失）

> 库可来自实验谱库或预测谱库（Prosit等）；decoy 为一一配对（反向/乱序，m/z/电荷匹配）。

## 输出

* 每条谱的：`S_target`、`S_decoy`、`Δ`、`gate_pass(True/False)`、`best_target_pep`、`best_decoy_pep`
* 全局：基于 Δ 的 **q-value**（target–decoy 估计）与阈值建议。

---

# 算法流程（How）

## Step 0. 预处理（MS2 → 干净峰表）

* **去弱信息**：去掉 **b1 / y1**（通过从**理论谱**中移除这些离子实现；或在观测谱低 m/z < 180–200 直接滤掉）。
* **降噪/稀疏化**：保留 **Top-K（默认 40）** 或累计解释强度 **≥80%** 的峰；
* **强度稳健化**：对强度取 **sqrt** 或 rank-norm，避免极强峰一票否决。

## Step 1. 候选生成（同窗内）

* 在 `window_start ~ window_end` 内，收集 **target 候选**与**decoy 候选**（可加 iRT 近邻过滤以提速）。

## Step 2. 快速打分（S）

* **对齐**：按 ppm 容差（默认 20 ppm）将观测峰与理论离子逐一匹配（取最近峰）。
* **加权 cosine**（核心分数）：

  * 对每个匹配峰，乘 **离子类型权重**（b/y 主离子高、带中性丢失低）与 **ppm 高斯惩罚**。
  * 得到 `S_cosine ∈ [0,1]`。
* **解释强度 (Explained Intensity, EI)**：匹配峰强度 / 总强度。
* **混合分数**：`S = α·S_cosine + (1−α)·EI`（默认 α=0.7）。

  > 直觉：既像（cosine），也“解释得多”（EI），对缺峰/偏位更稳。

## Step 3. 竞争（target vs decoy）

* **同一条 MS2**：取最强 **target 分数** `S_target` 与最强 **decoy 分数** `S_decoy`。
* 定义 **Δ = S_target − S_decoy**。

## Step 4. 门控（Gate）

* 通过条件：

  * `S_target ≥ s_min`（默认 0.2）
  * **Δ ≥ τ**（默认 0.1）
* 通过则 `gate_pass=True`，否则 False。

  > 逻辑：既要“像 target”，还要“**明显**赢过 decoy”。

## Step 5. FDR（离线估计）

* 把所有谱的 **Δ** 从高到低排序；
* 逐点累计 **target 通过** 与 **decoy 通过**，计算 **FDR**；
* 取 **q-value ≤ 1%** 的 Δ 作为最终门槛（可分 RT-bin 自适应）。

---

# 默认超参（可先不动）

* `min_mz=180`、`topk=40`、`ppm=20`、`alpha=0.7`、`s_min=0.2`、`tau=0.1`、`sigma_ppm=5`
* 离子权重：b/y 主离子=1.0；带中性丢失/多电荷=0.6（先简单化，后续可调）。

---

# 评估与“止损线”（Eval & Go/No-go）

## 样本设计（两周内可跑完）

* **两物种混样**（人+酵）或 TMT SPS-MS3 QC 数据，3×对照 vs 3×gating（或离线前后对比）。
* 等梯度、等载量、等方法。

## KPI（满足 ≥1 条就继续；全不达标就停）

1. **低丰度四分位(Q1) 缺失率 ↓ ≥20%**
2. **蛋白层 CV ↓ ≥15%**（技术重复或 QC 通道）
3. **跨物种假阳性 ↓ ≥20%**（酵母↔人错配率）
4. **等 FDR 下差异蛋白 ↑ ≥10%**（或 spike-in 比例偏差更小）

> 如果只看到“通过数+5–10%”，但上述质量指标**几乎不变**，就别继续砸时间。

---

# 产出与集成（Deliverables & Integration）

## 产出

* `scored.csv`：每谱 `S_target/S_decoy/Δ/gate_pass`
* `qvalues.csv`：Δ 的 q-value、建议阈值
* 报告（1 页）：识别曲线、Δ 分布、KPI 表格

## 集成到你现有项目

* 建议新目录：`12_gating/`（或 `15_gating/`），输入 `10_search/` 的峰表与库，输出给 `11_fdr/` 或直接进入 `20_quant/`。
* Snakemake 中加两条规则：`score_duel` → `td_qvalue`。

---

# 可选升级（等你决定再做）

## V1.5（仍很快）

* **EI 融合**（已做）、**ppm 高斯惩罚**（已做）
* **co-elution 小特征**（锚定离子 XIC 与前体的相关性，2–3 帧）

## V2（数学加码，离线重评分）

* **Bradley–Terry/逻辑回归胜率**：用少量特征把“胜负”变成**后验概率**（可校准做 q-value）。
* **最优传输 (OT/Sinkhorn)**：替代/融合 cosine，对偏位/缺峰/嵌合更稳。
* **Mixture-FDR（decoy-free）**：用两组混合模型从分数分布直接学假阳性率，降低 decoy 假设偏差；与 TD-FDR 并行汇报。

---

# 时间线（建议）

* **Day 1–3**：整理 I/O、跑 V1 baseline（cosine+EI+Δ）。
* **Day 4–7**：小规模 A/B，出 KPI；**达标就注册参数/阈值**。
* **Week 2**：补 V1.5（ppm/共流出小特征），写 1 页报告。
* **Week 3+**：若继续，做 V2（胜率模型或 OT），同步考虑 real-time 可行性（若 Thermo 配合）。

---

# 风险与对策

* **库质量**差 → Δ 不稳：先在“人+酵”混样或 SRMAtlas 子集上验证；
* **阈值泛化**：用 RT-bin/窗口-bin 自适应阈值；
* **收益稀释**：把省下的“扫描预算”**定向投给低丰度 target**或关键 panel（策略性门控）。

---

# 一句话总结

> 我们在 MS2 层做“目标-诱饵二人转”竞争打分：**S_target 要高**，还要**显著赢过 S_decoy（Δ≥τ）**。
> 先离线证明确实能让 **低丰度更少缺失、CV 更稳定、假阳性更低**，再考虑把门控前移到实时。

---

# A. 采集端“门控”：和你想法最接近的

* **RTS-MS3（Real-Time Search）**
  Thermo 在 Orbitrap 上做的“MS2→（实时数据库搜索）→只在命中时触发 MS3”的工作；实测能把 **SPS-MS3 的 duty cycle 压下来**，并保持/提升定量质量。这和你要的“在 MS2 层先筛，再决定要不要 MS3”是一个路子，只是他们用**数据库搜索**而非简单 cosine。([PMC][1])
  你可改进：把他们的“是否触发”判定函数，换成你提的 **target–decoy 对决 + 快速打分**（cosine/entropy/小模型）。

* **RTLS（Real-Time Library Search）**
  McGann 等人做了**实时光谱库匹配**，毫秒级决定触发/峰选择，特别针对 **TMT 多重定量**提升准确度。理念=“MS2 出来立判去留”。这和你想的“在 MS2 做 cosine 比对+门控”几乎同源（只是他们侧重库匹配实现）。([PMC][2])
  你可改进：把 **decoy 参照**显式纳入 RTLS 判定（他们主要讲库匹配；你可以做“target vs decoy margin”这一刀）。

> 结论：**RTS/RTLS 已经证明“MS2 先判、再触发”是有效的**。你的贡献可以是把**判定规则**从“命中/不命中”升级成**target–decoy 竞争 + 更鲁棒的相似度**，并提供更好的 **离线/在线 FDR 标定**。

---

# B. 谱库打分：从 cosine 出发的“更强相似度”

* **SpectraST / dot-product 传统谱库匹配**
  经典做法就是**加权点积（cosine 家族）**，很多引擎/库都这么干，是你 baseline 的学术根基。([PubMed][3])

* **Spectral Entropy（熵相似度）**
  近年证据显示：**熵相似度**在 MS/MS 谱库匹配里经常**优于点积/纯 cosine**，对噪声更鲁棒，也利于 FDR 估计（还有“ion entropy”扩展）。这恰好是你“替代 cosine”的直接候选。([PMC][4])

> 你能做的：
>
> 1. 把 **cosine→熵相似度** 作为首个替换；
> 2. 再加 **ppm 偏差惩罚 + 解释强度 (Explained Intensity)**，组成轻量“组合分数”；
> 3. 做**target–decoy margin**（S_target − S_decoy）门控，并给出**等价 q≤1% 的阈值**。

---

# C. 打分→FDR 的统计&ML基线（你可在此之上增强）

* **Percolator / mokapot（半监督重打分）**
  用 ML 对 PSM 特征重打分，提高真阳性、控制 FDR；mokapot 是 Python 版，易改。你的“二阶段小模型（logistic/BT）胜率”就能直接借这个范式。([PubMed][5])

* **PeptideProphet / iProphet（混合模型 & 概率化）**
  老牌**混合模型**把分数变成**后验概率**并传递到肽/蛋白层；这是“把阈值变概率”的教科书做法，你的 **Δ 分数**完全可以走这套，把 **门控变成 P(胜) ≥ p***。([PMC][6])

* **Decoy-free / Mixture-model FDR**
  直接对分数分布做**两组（或多组）混合**去估 FDR，**不依赖 decoy 假设**（适合 DIA/单细胞/低丰度）。这正好能和你的“decoy 辅助、但不被 decoy 束缚”结合。([Oxford Academic][7])

> 你能做的：
>
> * 把 **Δ 或 熵相似度**喂给 **半监督重打分**（离线），再给**混合模型 FDR**做 decoy-free 校准（并与经典 TD-FDR、two-species entrapment 对照）；
> * 给出**门控曲线**（阈值 ↔ q-value 对照表），便于未来移植到实时/准实时。

---

## 你可以直接“站在肩膀上”的组合拳（可写进计划书）

1. **RTS/RTLS 的思想 + 你老师的规则**

   * 采纳“MS2 先判、再触发/保留”的框架（RTS/RTLS 证实其可行）；
   * 把“是否判为 target”的**打分函数**从点积升级为 **熵相似度 + EI + ppm 核**，并引入 **target–decoy margin**；
   * 输出**q≤1%** 的门控阈值（离线求、在线用）。

2. **离线重打分 & decoy-free FDR**

   * 用 mokapot/简易 logistic 把少量特征（相似度、EI、#matched、ppm-RMSE、简化共流出）变后验；
   * 用 **混合模型**做 **decoy-free FDR** 对照，展示在低丰度/拥挤窗口的稳健性。([PubMed][8])

3. **评估指标跟社区接轨**

   * TMT/SPS-MS3：**缺失率↓、蛋白层 CV↓、两物种 entrapment 假阳性↓、差异蛋白↑**；
   * 报告中引用 RTS/RTLS 的对比语境，证明“我们在**判定函数**与**FDR 标定**上做了升级”，不是重复造轮子。([PMC][1])

---

## 小抄：你写 related work 时能用的 1 句话

* “我们沿用 **RTS/RTLS** 的‘**MS2 先判，命中才触发**’思想，但把判定从**单一命中**升级为**target–decoy 对决 + 熵相似度组合分数**，并用**混合模型/decoy-free**给出**可迁移的 q-value 门槛**。” ([PMC][1])

---

[1]: https://pmc.ncbi.nlm.nih.gov/articles/PMC7295121/?utm_source=chatgpt.com "Full-Featured, Real-Time Database Searching Platform ..."
[2]: https://pmc.ncbi.nlm.nih.gov/articles/PMC11554524/?utm_source=chatgpt.com "Real-time spectral library matching for sample multiplexed ..."
[3]: https://pubmed.ncbi.nlm.nih.gov/17295354/?utm_source=chatgpt.com "Development and validation of a spectral library searching ..."
[4]: https://pmc.ncbi.nlm.nih.gov/articles/PMC11492813/?utm_source=chatgpt.com "Spectral entropy outperforms MS/MS dot product similarity ..."
[5]: https://pubmed.ncbi.nlm.nih.gov/17952086/?utm_source=chatgpt.com "Semi-supervised learning for peptide identification from ..."
[6]: https://pmc.ncbi.nlm.nih.gov/articles/PMC3489532/?utm_source=chatgpt.com "A statistical model-building perspective to identification of MS/MS ..."
[7]: https://academic.oup.com/bioinformatics/article/36/Supplement_2/i745/6055912?utm_source=chatgpt.com "New mixture models for decoy-free false discovery rate ..."
[8]: https://pubmed.ncbi.nlm.nih.gov/33596079/?utm_source=chatgpt.com "mokapot: Fast and Flexible Semisupervised Learning for ..."
