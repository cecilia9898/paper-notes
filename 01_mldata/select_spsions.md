# SPS Ion Selection Criteria

Synchronous Precursor Selection (SPS) is used in TMT-based MS3 quantification to improve reporter ion accuracy. The following filtering criteria are commonly applied to select optimal MS2 fragment ions for SPS.

---

## 1. **Keep the top 200 MS2 ions**

* **Goal**: Select the most intense fragment ions to ensure high signal-to-noise ratio (S/N).
* **Reason**: Low-intensity ions are more likely to be noise or irrelevant background.
* **Effect**: Improves purity and quality of selected SPS ions.

---

## 2. **Exclude b1/y1 ions**

* **b1/y1 ions** are:

  * Short fragments (typically <200 Da)
  * Low in information content
  * Prone to interference from background
* **Reason for exclusion**: They are unstable and not useful for quantification.

---

## 3. **Keep all b-ions and K-ending y-ions**

* **b-ions**: Provide structural information from the N-terminus of peptides.
* **K-ending y-ions**: End with lysine (K), which is the TMT labeling site.
* **Why important**:

  * TMT labels attach to N-termini and lysines.
  * K-ending y-ions are more likely to retain TMT reporter tags, aiding quantification.

---

## 4. **Keep ions with exp\_mz ≥ 400**

* **Rationale**:

  * Fragment ions with m/z < 400 often fall into noisy regions of the spectrum.
  * Higher m/z fragments are typically more stable and informative.
* **Benefit**: Reduces interference and improves SPS ion quality.

---

## 5. **Exclude ions with m/z in the range of -50 to +5 of the MS1 precursor m/z**

* **Purpose**: Avoid selecting precursor ions or their isotopic peaks.
* **Why**:

  * Including these leads to redundant fragmentation (no new info).
  * Contaminates MS3 with leftover precursor ions.
  * Reduces the purity score for TMT quantification.

---

## ✅ Summary

| Criterion                          | Purpose                                        |
| ---------------------------------- | ---------------------------------------------- |
| Top 200 ions                       | Focus on high-intensity fragments              |
| Exclude b1/y1                      | Remove unstable, low-informative ions          |
| Keep b-ions and K-ending y-ions    | Preserve structural and TMT-relevant fragments |
| exp\_mz ≥ 400                      | Avoid noisy low-m/z range                      |
| Exclude ±50 to +5 of precursor m/z | Prevent precursor contamination                |

---

# SPS 离子筛选条件

在 TMT-MS3 定量实验中，Synchronous Precursor Selection（SPS）用于从 MS2 谱图中选择一组高质量的碎片离子用于 MS3 激发。这一过程对定量精度具有重要影响。以下是常用的五条 SPS 离子筛选规则及其解释。

---

## 1. **保留最强的 200 个 MS2 离子**

* **解释**：从 MS2 谱图中按强度降序排序，选择最强的前 200 个离子。
* **目的**：

  * 保证选中的离子信号强、噪声少。
  * 排除强度过低、可能为背景噪声的碎片。
* **结果**：提高 MS3 的信噪比和定量质量。

---

## 2. **排除 b1 和 y1 离子**

* **解释**：b1/y1 离子是从肽段最前端或末端产生的最小碎片，质量很小（通常 <200 Da）。
* **目的**：

  * 避免选择不稳定、容易受背景干扰的碎片。
  * 这类离子结构信息少，对定量贡献小。
* **结果**：提升 SPS 离子的代表性和稳定性。

---

## 3. **保留所有 b 离子以及以 K 结尾的 y 离子**

* **解释**：

  * b 离子来源于肽段 N 端碎裂，能提供结构信息。
  * TMT 标签通常连接在赖氨酸（K）残基上，因此以 K 结尾的 y 离子更可能保留 TMT 标签。
* **目的**：

  * 保留与肽段结构相关、对定量有意义的离子。
* **结果**：有助于后续 MS3 的 reporter ion 精准识别。

---

## 4. **仅保留实验 m/z ≥ 400 的离子**

* **解释**：低于 400 的 m/z 区域常常有背景离子或干扰，容易降低数据质量。
* **目的**：

  * 排除低 m/z 的噪声碎片。
  * 保留更高质量、稳定性好的碎片离子。
* **结果**：增强谱图清晰度，提升数据准确性。

---

## 5. **排除前体 m/z ±50 到 +5 范围内的碎片离子**

* **解释**：

  * 该范围内的离子可能是未碎裂完全的前体离子或其同位素峰。
  * 被选中会导致 MS3 再次碎裂前体本身，冗余无效。
* **目的**：

  * 防止污染 MS3 谱图，降低 reporter ion 的纯度评分（purity score）。
* **结果**：提高 MS3 数据的特异性和定量可靠性。

---

## ✅ 总结表格

| 筛选条件                   | 作用目的              |
| ---------------------- | ----------------- |
| 保留最强 200 个离子           | 提高信噪比，避免低强度噪声     |
| 排除 b1/y1 离子            | 去除不稳定、无效碎片        |
| 保留 b 离子与 K 结尾的 y 离子    | 保留结构信息与定量标签       |
| 仅保留 m/z ≥ 400 的离子      | 规避噪声干扰，提高碎片质量     |
| 排除前体 m/z ±50\~+5 范围内离子 | 避免前体污染，确保 MS3 特异性 |

---


