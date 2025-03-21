# 网络推断的基类（Base Classes for Network Inference）

## 【1】导入 Python 的抽象基类（ABC）模块

```python
from abc import ABC, abstractmethod
```

### **1. 什么是 ABC（抽象基类）？**
- **ABC 的作用**：确保所有子类 **必须** 实现你规定的方法（但具体实现可以不同）。
- **如果子类不实现这些方法，Python 会直接报错**，防止你在使用时出错。

---

### **2. 现实世界的类比**
#### **假设你是一家餐厅的老板**
你经营一家餐厅，所有厨师 **必须会做**：
- 炒饭 🍚
- 煮汤 🍜
- 煎牛排 🥩

但不同厨师的做法不同：
- **中餐厨师**：用大火爆炒加酱油。
- **法国厨师**：用黄油加红酒。
- **印度厨师**：加入咖喱和香料。

如果你不强制要求厨师做这三道菜，有的厨师可能只会炒饭，不会煎牛排，导致顾客不满。

---

### **3. 如果没有“标准接口”会发生什么？**
```python
class Chef:
    def cook_fried_rice(self):
        print("做了一份炒饭")

    def cook_soup(self):
        print("做了一锅汤")

# 这个厨师不会煎牛排！
```
当你让他煎牛排：
```python
chef = Chef()
chef.cook_steak()  # ❌ 方法不存在，代码崩溃！
```
💥 **你的餐厅要被顾客投诉了！**

---

### **4. 使用 ABC 强制所有厨师实现三道菜**
```python
from abc import ABC, abstractmethod

# 规定：所有厨师必须会做这三道菜
class Chef(ABC):

    @abstractmethod
    def cook_fried_rice(self):
        pass  # 具体做法由子类决定

    @abstractmethod
    def cook_soup(self):
        pass

    @abstractmethod
    def cook_steak(self):
        pass
```
这时候，**如果厨师忘记实现 `cook_steak()`，Python 直接报错**！

---

### **5. 具体厨师（子类）实现自己的做法**
```python
class ChineseChef(Chef):
    def cook_fried_rice(self):
        print("中餐厨师：用大火爆炒炒饭！")

    def cook_soup(self):
        print("中餐厨师：用骨头煲汤！")

    def cook_steak(self):
        print("中餐厨师：用酱油和蒜蓉煎牛排！")

class FrenchChef(Chef):
    def cook_fried_rice(self):
        print("法国厨师：加黄油炒饭！")

    def cook_soup(self):
        print("法国厨师：煮一锅法式洋葱汤！")

    def cook_steak(self):
        print("法国厨师：用红酒酱煎牛排！")
```

---

### **6. 测试一下**
```python
chinese_chef = ChineseChef()
chinese_chef.cook_fried_rice()  # 中餐厨师：用大火爆炒炒饭！
chinese_chef.cook_soup()        # 中餐厨师：用骨头煲汤！
chinese_chef.cook_steak()       # 中餐厨师：用酱油和蒜蓉煎牛排！

french_chef = FrenchChef()
french_chef.cook_fried_rice()  # 法国厨师：加黄油炒饭！
french_chef.cook_soup()        # 法国厨师：煮一锅法式洋葱汤！
french_chef.cook_steak()       # 法国厨师：用红酒酱煎牛排！
```

---

### **7. 如果厨师少学了一道菜，Python 直接报错**
```python
class BadChef(Chef):
    def cook_fried_rice(self):
        print("这个厨师会做炒饭")

    def cook_soup(self):
        print("这个厨师会煮汤")

    # 这个厨师忘记了 cook_steak 方法
```
创建 `BadChef` 实例：
```python
bad_chef = BadChef()
```
💥 **Python 报错**：
```
TypeError: Can't instantiate abstract class BadChef with abstract method cook_steak
```
**Python 发现 `BadChef` 没有实现 `cook_steak()`，所以直接拒绝创建对象！**

---

### **8. 和你的 `base.py` 代码的对应关系**
你的 `base.py` 代码定义了 `base(ABC)` 作为基类：
```python
class base(ABC):
    @abstractmethod
    def _infer(self, d):
        pass  # 这里不提供具体实现
```
然后，具体的方法（`dag`, `lr`）继承 `base` 并实现 `_infer()`：
```python
class lr(base):
    def _infer(self, d):
        return d  # 这里是线性回归的推断逻辑
```
**这就确保了所有网络推断方法都有 `_infer()`，不会有遗漏！**

---

### **9. 现实世界 vs. Python ABC**
| 现实世界           | Python ABC                       |
|--------------------|----------------------------------|
| 你是餐厅老板       | `ABC` 抽象基类                  |
| 你规定厨师必须会炒饭、煮汤、煎牛排 | `@abstractmethod` 规定所有子类必须有 `_infer()` |
| 具体怎么炒饭、煎牛排，每个厨师可以自己决定 | 子类 (`dag`, `lr`) 可以自己实现 `_infer()` |
| 只会炒饭不会煎牛排的厨师不能上岗 | 如果子类缺少 `_infer()`，Python 直接报错 |

---

### **10. 总结**
1. **ABC 让所有子类都必须实现某些方法（如 `_infer()`）。**
2. **如果子类忘记实现，Python 会直接报错，防止程序崩溃。**
3. **但子类可以用自己的方式实现方法**（比如中餐厨师 vs. 法国厨师）。
4. **这样你可以放心让计算机执行你的规则，而不怕代码遗漏关键方法！**

🎯 **ABC = 你是老板，规定必须做哪些菜，但具体怎么做可以随意！** 🚀

---

## 【2】定义网络推断的抽象基类 `base`

```python
# 从上一级目录的 backend 模块导入 base 作为 backend_base
from ..backend import base as backend_base

class base(ABC):
    """
    网络推断的基类，所有具体的网络推断算法都应该继承自这个类
    """
    
    def __init__(self, backend: backend_base, docheck: bool = True, nth=1):
        """
        初始化基类
        
        参数：
        backend: backend_base 类的实例，表示计算后端
        docheck: bool 类型，是否执行检查（默认 True）
        nth: int 类型，使用的 CPU 线程数量（默认 1）
        """
        self.docheck = docheck  # 是否执行数据检查
        self.b = backend  # 存储计算后端实例
        self.nth = nth  # 设定使用的 CPU 线程数量

    @abstractmethod
    def _infer(self, d):
        """
        从数据中推断网络结构，具体方法需由子类实现
        
        参数：
        d: 矩阵类型的数据，其中 d[i, j] 表示样本 i 在节点 j 的值
        
        返回值：
        inet:      网络强度矩阵，表示 i -> j 的边的权重
        inetp:     P 值矩阵，表示 i -> j 这条边的显著性水平
        inetsigma: 标准误差矩阵，表示 i -> j 这条边的不确定性
        """
        pass  # 该方法需要在子类中具体实现，基类中不提供实现

    def check(self, d, ans):
        """
        进行推断结果的合理性检查（Sanity Check）
        
        参数：
        d: 输入数据，通常是一个数据矩阵
        ans: 期望的正确答案，用于验证推断结果的正确性
        
        作用：
        这个方法可能会在子类中实现，用于对推断的网络结构进行评估。
        """
        if len(ans) < 3:
            raise TypeError('Return must have length of at least 3.')  # 确保返回值至少包含 3 个元素
    
    def infer(self, d, *a, **ka):
        """
        执行网络推断，并根据需要执行检查。
        该方法不应被子类重载，而应重载 `_infer` 方法。
        
        参数：
        d: 输入数据矩阵，其中 d[i, j] 代表样本 i 在节点 j 的值。
        *a, **ka: 传递给 `_infer` 方法的参数。
        
        返回值：
        inet:  网络强度矩阵，表示 i -> j 的边。
        inetp: P 值矩阵，表示 i -> j 这条边的统计显著性。
        """
        ans = self._infer(d, *a, **ka)  # 调用 `_infer` 进行实际推断
        if self.docheck:
            self.check(d, ans)  # 执行检查
        return ans  # 返回推断结果
```

---

### **用数学公式和通俗方式解释这些方法**

#### **1. `__init__` 方法：初始化网络推断器**
##### **数学表达**
假设：
- $B$ 是计算后端（backend），可能是一个矩阵运算库（如 NumPy 或 TensorFlow）。
- $d$ 是输入数据矩阵，其中 $d_{i,j}$ 表示**样本 $i$ 在节点 $j$ 的值**。
- $n_{th}$ 是使用的 CPU 线程数。

初始化过程：
$$
\text{推断器} = (B, \text{是否检查}, n_{th})
$$

##### **通俗解释**
把 `__init__` 想象成：
- 你要去跑步（做网络推断），先 **换上跑鞋（backend）**，决定 **要不要热身（docheck）**，以及 **找几个人陪你跑（nth 线程数）**。

---

#### **2. `_infer(d)` 方法（抽象方法）**
##### **数学表达**
假设：
- $d$ 是数据矩阵，其中 $d_{i,j}$ 表示样本 $i$ 在节点 $j$ 的值。
- 目标是推断出 **网络连接矩阵** $I$。

输出：
- $I_{\text{net}}$：网络强度矩阵，$I_{i,j}$ 表示 **节点 $i$ 指向 $j$ 的边强度**。
- $I_{\text{p}}$：P 值矩阵，表示显著性。
- $I_{\sigma}$：误差矩阵，表示不确定性。

数学表示：
$$
\_infer(d) \rightarrow (I_{\text{net}}, I_{\text{p}}, I_{\sigma})
$$

##### **通俗解释**
把 `_infer()` 想象成：
- 你有一堆篮球比赛数据（$d$）。
- 你要分析 **谁传球给谁最多（$I_{\text{net}}$）**，**这个结果是不是偶然（$I_{\text{p}}$）**，以及 **误差有多大（$I_{\sigma}$）**。
- 但 **具体分析方法** 由子类决定！

---

#### **3. `check(d, ans)` 方法**
##### **数学表达**
若 `ans` 是 `_infer(d)` 返回的元组：
$$
ans = (I_{\text{net}}, I_{\text{p}}, I_{\sigma})
$$
则：
$$
\text{len}(ans) \geq 3
$$

##### **通俗解释**
把 `check()` 想象成：
- 你做了一份数学作业（网络推断）。
- 交给老师检查：
  - **答案数量不够**，老师会说：“你没写完整！”
  - **格式不对**，老师直接让你重写！

---

#### **4. `infer(d, *a, **ka)` 方法**
##### **数学表达**
流程：
$$
ans = \_infer(d, *a, **ka)
$$
若 `docheck = True`：
$$
\text{check}(d, ans)
$$
返回：
$$
(I_{\text{net}}, I_{\text{p}}, I_{\sigma})
$$

##### **通俗解释**
把 `infer()` 想象成：
- 你考试（推断）。
- 先自己做题（`_infer(d)`）。
- 若认真点（`docheck=True`），找同学检查（`check(d, ans)`）。
- 最后交卷（返回 `ans`）。

---

#### **5. 总结表格**
| 方法         | 数学表示                              | 通俗解释                          |
|--------------|---------------------------------------|-----------------------------------|
| `__init__()` | $(B, \text{是否检查}, n_{th})$       | 换跑鞋，决定热身，找朋友一起跑。  |
| `_infer(d)`  | $d \rightarrow (I_{\text{net}}, I_{\text{p}}, I_{\sigma})$ | 分析篮球数据，找出传球网络。      |
| `check(d, ans)` | $\text{len}(ans) \geq 3$            | 老师检查作业，看有没有少写。      |
| `infer(d, *a, **ka)` | $ans = \_infer(d, *a, **ka)$        | 考试后检查再交卷。                |

---

#### **6. 执行流程**
调用 `infer(d)` 时：
1. **调用 `_infer(d)`** → 计算 $I_{\text{net}}, I_{\text{p}}, I_{\sigma}$。
2. **若 `docheck=True`，调用 `check(d, ans)`**。
3. **返回 `ans`**。

---

#### **7. 好处**
- **数学上**：确保输出一致。
- **编程上**：避免遗漏 `_infer()`。
- **可维护性**：便于扩展。

---

#### **8. 小结**
- `_infer(d)`：核心推断逻辑，$d \rightarrow (I_{\text{net}}, I_{\text{p}}, I_{\sigma})$。
- `check(d, ans)`：检查结果。
- `infer(d)`：自动化流程。

---

## 【3】定义 DAG（有向无环图）推断基类

```python
class dag(base):
    """
    用于 DAG 结构网络推断的基类。
    """
    
    def check(self, d, ans):
        """
        进行 DAG 结构的合理性检查。
        
        参数：
        d: 输入数据。
        ans: `_infer` 方法的返回结果。
        
        抛出异常：
        - 如果检测到循环依赖，则抛出 `RuntimeError`。
        """
        import numpy as np
        import networkx as nx
        
        super().check(d, ans)  # 调用父类的 `check` 方法
        
        for xi in [[ans[0], 0], [ans[1], 1], [ans[2], 0]]:
            if xi[0] is None:
                continue
            n1 = np.array([self.b.numpy(x) for x in self.b.nonzero(xi[0] != self.b.ones(xi[0].shape, dtype=xi[0].dtype) * xi[1])]).T
            n2 = nx.DiGraph()
            n2.add_edges_from(n1)
            try:
                nx.find_cycle(n2)  # 检测是否存在循环依赖
                hascycle = True
            except nx.exception.NetworkXNoCycle:
                hascycle = False
            if hascycle:
                raise RuntimeError('Cycle found')  # 如果发现循环，则抛出异常
```

---

### **数学化解析 `check(self, d, ans)` 方法**

#### **1. DAG 的数学定义**
有向图 $G = (V, E)$：
- $V$：节点集合。
- $E$：有向边集合。

若无环：
$$
\forall \text{路径 } v_1 \to v_2 \to ... \to v_k, \quad \text{不存在 } v_k \to v_1
$$

---

#### **2. 输入**
- $d$：数据矩阵。
- $ans = (I_{\text{net}}, I_{\text{p}}, I_{\sigma})$。

---

#### **3. 解析 `for xi in [[ans[0], 0], [ans[1], 1], [ans[2], 0]]`**
遍历：
1. $I_{\text{net}}$（阈值 0）。
2. $I_{\text{p}}$（阈值 1）。
3. $I_{\sigma}$（阈值 0）。

若：
$$
X_{i,j} \neq \text{阈值} \Rightarrow i \to j
$$

---

#### **4. 构建有向图 $G$**

```python
n1 = np.array([self.b.numpy(x) for x in self.b.nonzero(xi[0] != self.b.ones(xi[0].shape, dtype=xi[0].dtype) * xi[1])]).T
```
数学表示：
$$
E = \{(i, j) \mid X_{i,j} \neq \text{阈值}\}
$$

```python
n2 = nx.DiGraph()
n2.add_edges_from(n1)
```
$$
G = (V, E)
$$

---

#### **5. 检测环**
```python
try:
    nx.find_cycle(n2)
    hascycle = True
except nx.exception.NetworkXNoCycle:
    hascycle = False
```
$$
\exists v_1 \to v_2 \to ... \to v_k \to v_1 \Rightarrow \text{报错}
$$

---

#### **6. 总结**
1. 从 $I_{\text{net}}, I_{\text{p}}, I_{\sigma}$ 提取边。
2. 用 `networkx` 检测环。
3. 若无环，则通过。

---

#### **7. 类比**
- 团队指挥链检查：若 A → B → C → A，则混乱！

---

#### **8. 公式总结**
| 步骤       | 数学公式                          |
|------------|-----------------------------------|
| 提取边     | $E = \{(i, j) \mid I_{i,j} \neq \text{阈值}\}$ |
| 生成图     | $G = (V, E)$                     |
| 检测环     | 若 $\exists v_1 \to v_2 \to ... \to v_k \to v_1$，则报错 |

---

## 【4】线性回归（Linear Regression）推断基类

```python
class lr(base):
    """
    使用线性回归（Linear Regression）进行网络推断的基类。
    """
```

（未完待续）

---
