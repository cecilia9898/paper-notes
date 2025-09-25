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

## 🎨 5. 一个通俗比喻

想象你去夜店相亲：

* **Target** = 真正的帅哥美女（真实蛋白）。
* **Decoy** = 店里请来的托儿（随机反转序列）。
* 你聊到的“托儿”数量 = 说明这家店里有多少水分。
* 如果你聊到 100 个对象，结果有 5 个是托，那大约 5% 的“匹配”是假阳性。

---

✨ 总结：
**Target–decoy strategy = 通过在数据库里加入假序列，用 decoy 命中的比例来估计假发现率，从而对 target hits 做 FDR 控制。**

---
