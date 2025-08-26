# SPS ion intensity 和 reporter ion intensity 的关系

## 1. 实验逻辑上的联系

* **Reporter ion** 出现在 MS2（或 MS3）谱图里，是 **标签（TMT/iTRAQ 等）碎裂后的信号**，直接反映样品定量。
* **SPS ions** 是在 MS3 里挑选的一组前体碎片离子（通常 *top-N strongest fragment ions*），用来触发和增强 reporter ion 的生成信号。
  👉 所以：你挑选的 **SPS 离子越强**，它们携带的“定量标签”越可能在 MS3 里被打出强的 reporter ion。

---

## 2. 信号强度传递关系

* **高强度 SPS ions** → 更高概率带有 TMT 标签 → 在 MS3 里会贡献更多 reporter ion 信号。
* 如果 **SPS ion 很弱**，reporter ion 的信号往往也跟着低（可能接近噪音），因为没有足够的碎片离子携带 reporter 标签。

📊 从统计上看，**SPS ion intensity 和 reporter ion intensity 正相关**，原因就是信号链路的“传递”。

---

## 3. 额外因素（为什么不总是完美相关）

* **共碎片化（co-isolation）**：如果 SPS 里混进了别的肽段，reporter ion 强度可能虚高（来自 contamination）。
* **碎裂效率**：某些离子强但不容易碎成 reporter ion，导致强度关系没那么线性。
* **仪器方法**：SPS-MS3 通常选择多个 fragment ion，reporter ion 强度是这些离子“合力”的结果。

---

## 为什么不是严格线性？

1. **碎裂效率差异**

   * 不同 SPS ion 在 MS3 碎裂时，能不能“高效”生成 reporter ion 差别很大。
   * 有些离子带标签但不好裂 → 强 SPS ion ≠ 强 reporter ion。

2. **共碎片化 (co-isolation)**

   * SPS ion 里混入别的肽 → reporter ion 强度可能虚高（外来标签贡献）。

3. **信号饱和/检测噪音**

   * 在高强度区，detector 可能接近饱和，导致 reporter ion intensity 增幅小于 SPS ion。
   * 在低强度区，噪音占比高，线性关系更差。

---

## 什么关系更合理？

* **整体趋势**：SPS ion intensity ↑ → reporter ion intensity ↑（正相关）。
* **数学模型**：更接近于 **线性 + 噪声项**，可以写成：

$$
I_{\text{reporter}} \approx \alpha \cdot I_{\text{SPS}} + \epsilon
$$

其中：

* $\alpha$：碎裂效率和标签携带比例决定的系数；
* $\epsilon$：噪音、共碎片化等影响。

在实际数据里，你会看到 **散点图是正斜率的云团**，但不是一条直线。

---

