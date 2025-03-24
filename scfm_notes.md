## scfm.py code 
## 🔍 代码本体：

```python
def loss(self, model, guide, *args, **kwargs):
    """
    Not needed by default.
    """
    raise NotImplementedError
```

---

## 🧠 中文解释：

你在这里**故意不实现 `loss()` 方法**，并且显式抛出 `NotImplementedError`，意思是：

> “默认的 loss 接口我不打算支持，我只支持 differentiable_loss。”

---

## 🤔 那问题来了：Pyro 有两个 loss 方法，啥区别？

| 方法名 | 是否可导？ | 用途 | 默认返回值 |
|--------|------------|------|------------|
| `loss()` | ❌ 不可导（只返回标量 loss） | 用于 logging、early stopping、打印 ELBO | `float` |
| `differentiable_loss()` | ✅ 可导 | 用于优化器的反向传播 | `torch.Tensor` 或多个 tensor |

Pyro 的 `Trace_ELBO` 默认支持两个接口：

```python
loss = elbo.loss(model, guide)                # logging 用
loss_tensor = elbo.differentiable_loss(model, guide)  # 训练用（backprop）
```

---

## 🎯 你为什么禁用了 `loss()`？

因为你自定义的版本**返回的是一个 loss 的 tuple（每个变量一个）**，而 Pyro 默认的 `loss()` 期望的是一个标量。

举个例子，别人家的 `loss()` 返回：

```python
-312.4
```

你家的 `differentiable_loss()` 返回：

```python
(loss_latent1, loss_latent2, ..., loss_latentN)
```

这两个接口根本不兼容，Pyro 自己也没法知道怎么自动加和、展示、反传，所以你干脆明说：

> 不给你 `loss()`，别调用它。

---

## 💡 如果你不禁用它会怎么样？

Pyro 会在训练时可能尝试访问 `.loss()`：

```python
elbo = Trace_ELBO_site(["z1", "z2"])
elbo.loss(model, guide)
```

这时候就会返回一个错误的结果（或者出 bug），因为你本意是分变量追踪 loss，不是求和。

所以最安全的做法就是：抛出 `NotImplementedError`，告诉使用者不要碰它。

---

## ✅ 总结一句话：

> 你禁用了 `loss()`，是因为你只实现了更细粒度、更灵活的 `differentiable_loss()`，这也是你自定义 ELBO 的主要目的——**追踪每个变量的 loss，而不是只要一个总和标量**。

---
---

## 📣 一句话解释：

> **反向传播（backpropagation）是一种用链式法则（链式求导）计算神经网络中各个参数对最终 loss 的梯度的方法。**

用更中二一点的说法：
> 它是一场从 loss 出发、穿越神经网络每层、逐个告诉参数该怎么更新的旅程。

---

## 🧮 数学上是啥？

假设你有一个神经网络，输入 $x$，输出是 $\hat{y}$，然后有一个 loss 函数：

\[
L = \text{Loss}(y, \hat{y})
\]

反向传播的目标就是：

\[
\frac{\partial L}{\partial \theta}
\]

也就是**损失函数对参数 $\theta$ 的梯度**，告诉你怎么用梯度下降法（或其他优化器）来更新 $\theta$。

---

## 🔁 用链式法则一步步计算

神经网络是由很多函数叠起来的，比如：

\[
\hat{y} = f_3(f_2(f_1(x)))
\]

假设 loss 是 $L(\hat{y})$，那你要算：

\[
\frac{dL}{d\theta} = \frac{dL}{d f_3} \cdot \frac{d f_3}{d f_2} \cdot \frac{d f_2}{d f_1} \cdot \frac{d f_1}{d \theta}
\]

这就是链式法则，也叫 **链式求导（chain rule）**。

---

## 🔋 反向传播在神经网络中怎么用？

1. **正向传播**（forward pass）：输入数据 → 经过神经网络 → 得到预测值 $\hat{y}$ 和 loss $L$

2. **反向传播**（backward pass）：
   - PyTorch、TensorFlow 自动用链式法则从 loss 开始向后传播
   - 逐层计算梯度 $\frac{dL}{d\theta}$

3. **优化器更新参数**（如 Adam、SGD）：

\[
\theta \leftarrow \theta - \eta \cdot \frac{\partial L}{\partial \theta}
\]

---

## 🧠 在 PyTorch 中：

```python
loss = compute_loss(model(x), y)
loss.backward()    # 🔥 开始反向传播！
optimizer.step()   # 更新参数
```

PyTorch 自动维护一个**计算图（computation graph）**，它会记录每一个操作（比如加法、乘法、非线性），然后通过 `backward()` 把梯度一层一层传回去。

---

## 🤔 那在概率模型 / Pyro 中呢？

在 Pyro 中的 `guide()` 通常包含参数 $\phi$，你希望最大化 ELBO（或最小化负 ELBO）：

\[
\text{loss} = -\text{ELBO}(\phi)
\]

当你调用：

```python
loss.backward()
```

就会触发：

> ELBO 对 $\phi$ 的反向传播，也就是 $\frac{\partial \text{ELBO}}{\partial \phi}$

---

## 🔥 总结类比一波：

| 术语 | 含义 | 类比 |
|------|------|------|
| 正向传播 | 数据流动方向 | 输入 → 输出 |
| 反向传播 | 梯度回传方向 | loss → 每个参数 |
| 链式法则 | 多层求导法则 | 多米诺骨牌 |
| `.backward()` | 调用反向传播 | 点燃导火索🔥 |

---

# 💬 一句话总结：

> 反向传播 = 用链式求导从 loss 开始，一步步把梯度“倒推”到每个可训练参数，告诉你该怎么更新模型。

---
---

# 💡 一句话解释

> **重参数化变量**：你可以把采样过程写成一个可微的函数，直接对输入的噪声采样 + 参数做函数变换。

> **非重参数化变量**：没法这么干！只能用 score function 梯度，也就是 REINFORCE-style 的估计器，噪声和参数分离不了。

---

# 🎯 举个简单例子：标准正态变量

```python
z ~ Normal(μ, σ)
```

你可以重参数化成：

```python
ε ~ Normal(0, 1)
z = μ + σ * ε
```

这个 `z` 是一个**重参数化变量**，因为它是 μ 和 σ 的**可微函数**，你可以做 backprop！

---

# ❌ 那啥是非重参数变量？

比如：

```python
z ~ Categorical(probs=[0.2, 0.5, 0.3])
```

这个 $z$ 是离散变量，你没法写成：

```
z = f(θ, ε)
```

- 它的采样过程是不可微的（argmax 随机选择）
- PyTorch 自动微分系统完全没法处理这个过程

所以只能用 Pyro 的 score function estimator（aka REINFORCE）去处理它的梯度。

---

# 🔧 Pyro 怎么知道变量能不能重参数？

Pyro 的 `pyro.sample(...)` 会自动检查：

```python
pyro.sample("z", dist.Normal(...))        ✅ 重参数化
pyro.sample("z", dist.Categorical(...))   ❌ 非重参数化
```

并在内部创建：

- entropy term ✅（如果能重参数）
- score function term ✅（如果不能重参数）

你在 `site["score_parts"]` 中能看到这两个。

---

# 🤖 Pyro 是怎么处理非重参数变量的？

1. **重参数变量**（如 Gaussian）：
   - 用 reparameterization trick
   - 直接 backprop 过去，计算图可微 ✅

2. **非重参数变量**（如 Categorical、Bernoulli）：
   - 用 score function estimator（REINFORCE）
   - 手动加一个修正项：
     \[
     \mathbb{E}_{q(z)}\left[ \log \frac{p(z)}{q(z)} \cdot \nabla \log q(z) \right]
     \]
   - 这时候就需要 `log_r`！

---

# 🧪 总结对比表格

| 属性 | 重参数化变量 | 非重参数化变量 |
|------|--------------|----------------|
| 举例 | Normal, LogNormal | Categorical, Bernoulli |
| 是否可导 | ✅ 是 | ❌ 否 |
| 是否能用 backprop | ✅ 可以 | ❌ 不行（需要 REINFORCE） |
| Pyro 梯度来源 | entropy_term | score_function_term |
| 是否需要 `log_r` | ❌ 不用 | ✅ 必须 |

---

# 🔥 总结一句话：

> **非重参数变量 = 没法对采样过程做反向传播的变量。你只能用 score function gradient 来估计它对 loss 的贡献（并且特别 noisy！）**

---
---
## 🔍 代码回顾：

```python
log_r = None
```

**这是一个初始化变量的动作。**

---

## 🎯 中文解释：

> "我先不算 log_r，等我真的用到它的时候再算，而且只算一次。"

---

## 💡 log_r 是啥？

它是 Pyro 提供的一个内部函数输出：

```python
log_r = _compute_log_r(model_trace, guide_trace)
```

它的数学意义是：

\[
\log r(z) = \log \frac{p(z)}{q(z)}
\]

也就是**模型和 guide 的 log 概率比值**。这个在使用 **score function gradient estimator**（非 reparameterizable 变量）时是必须的。

---

## 🧠 为什么一开始不直接算？

### 理由很简单也很实用：

1. **节省计算资源**：如果 guide 中所有变量都是可重参数化（reparameterizable），那么就不需要 `log_r`，所以不必白算。

2. **只算一次**：如果 guide 里有多个变量都需要 `log_r`，你也只想算一次，避免重复调用 `_compute_log_r(...)`。

---

## 🧪 举个例子：

想象你 guide 中有三个变量："z1", "z2", "z3"，其中只有 "z2" 是 discrete，不支持 reparameterization。

那你的代码跑起来大概像这样：

```python
log_r = None
for name in guide_trace.nodes:
    if needs_score_fn_estimator(name):  # e.g., name == "z2"
        if log_r is None:
            log_r = _compute_log_r(...)
        use(log_r)
```

这样你只在**第一次真正需要它时才调用**，而且之后可以重复使用。

---

## 🚀 这个设计叫什么？

> **懒计算（lazy computation） + 缓存（memoization）**！

这种技巧特别常见于：

- PyTorch 的 forward 函数（比如某些 layer）
- JAX / TensorFlow 的 trace 编译器
- NumPy 的 lazy slicing

---

## ✅ 总结一句话：

> `log_r = None` 是一个聪明的预设，意思是：
>
> “我可能会需要 log_r，但只有当我真的遇到非重参数变量时，我才去算这个 log 比值，而且只算一次。”


## 代码解释ELBO

@staticmethod
	def _differentiable_loss_particle(model_trace, guide_trace):		# pylint: disable=W0221
		"""
		Adapted to output a dictionary each for loss and surraget loss.
		"""
		from collections import defaultdict
		from pyro.infer.trace_elbo import is_identically_zero,torch_item,_compute_log_r
		loss=defaultdict(float)
		surrogate_loss=defaultdict(float)
		log_r = None

		# compute elbo and surrogate elbo
		for name, site in model_trace.nodes.items():
			if site["type"] == "sample":
				loss[name]=loss[name]-torch_item(site["log_prob_sum"])
				surrogate_loss[name]=surrogate_loss[name]-site["log_prob_sum"]

		for name, site in guide_trace.nodes.items():
			if site["type"] == "sample":
				_, score_function_term, entropy_term = site["score_parts"]

				loss[name]=loss[name]+torch_item(site["log_prob_sum"])

				if not is_identically_zero(entropy_term):
					surrogate_loss[name]=surrogate_loss[name]+entropy_term.sum()

				if not is_identically_zero(score_function_term):
					if log_r is None:
						log_r = _compute_log_r(model_trace, guide_trace)
					site = log_r.sum_to(site["cond_indep_stack"])
					surrogate_loss[name]=surrogate_loss[name]-(site * score_function_term).sum()
		loss=dict(loss)
		surrogate_loss=dict(surrogate_loss)
		return loss,surrogate_loss


# 🧠 首先：ELBO 的整体形式

标准 ELBO 的形式是：

\[
\text{ELBO}(q) = \mathbb{E}_{q(z)}[\log p(x, z) - \log q(z)]
\]

可以拆成：

\[
\text{ELBO}(q) = \underbrace{\mathbb{E}_{q(z)}[\log p(x \mid z)]}_{\text{重建项}} - \underbrace{\text{KL}(q(z) \| p(z))}_{\text{正则项}}
\]

---

## ✅ Pyro 的细化写法（site-wise）

假设我们模型中有多个 latent variable：$z_1, z_2, \dots, z_n$，那么：

\[
\text{ELBO}(q) = \sum_{i=1}^n \mathbb{E}_{q(z_i)}[\log p(z_i) - \log q(z_i)]
\quad (\text{我们忽略 } \log p(x \mid z) \text{ 项，只看 latent 变量部分})
\]

你的代码做的事就是：把这个 ELBO 拆成每个 site 单独的：

\[
\text{ELBO}_i = \mathbb{E}_{q(z_i)}[\log p(z_i)] - \mathbb{E}_{q(z_i)}[\log q(z_i)]
\]

---

## ✅ Pyro 实际计算两部分：

### 1. 普通 loss（无梯度的）

代码：

```python
loss[name] -= torch_item(site["log_prob_sum"])   # from model
loss[name] += torch_item(site["log_prob_sum"])   # from guide
```

数学形式：

\[
\text{loss}_i = -\log p(z_i) + \log q(z_i)
\]

注意这里的 `log_prob_sum` 实际就是 $\log p(z_i)$ 或 $\log q(z_i)$，Pyro 自动求和了 plate 维度。

这个 loss 是 `.item()` 过的，是普通数值，不保梯度。

---

### 2. Surrogate loss（有梯度的）

代码：

```python
surrogate_loss[name] -= site["log_prob_sum"]   # model
surrogate_loss[name] += entropy_term.sum()     # guide (if reparam)
surrogate_loss[name] -= (log_r * score_term).sum()  # guide (if not reparam)
```

我们可以数学上写为：

#### 如果支持重参数化（reparameterizable）：

\[
\text{surr\_loss}_i = -\log p(z_i) + \underbrace{\mathbb{E}_{q(z_i)}[-\log q(z_i)]}_{\text{Entropy}}
\]

也就是：

\[
\text{surr\_loss}_i = -\log p(z_i) + \text{Entropy}[q(z_i)]
\]

Pyro 会通过 entropy_term.sum() 提供这个熵项的估计。

---

#### 如果不支持重参数化（非重参数变量）：

Pyro 使用 score function 估计：

\[
\text{surr\_loss}_i = -\log p(z_i) + \log q(z_i) + \left( \log \frac{p(z_i)}{q(z_i)} \cdot \nabla \log q(z_i) \right)
\]

那一项 `log_r * score_function_term` 对应的正是：

\[
\log \frac{p(z)}{q(z)} \cdot \nabla \log q(z)
\]

注意这里不是直接的 loss 项，而是对 **梯度的估计器**，所以叫 surrogate loss。

---

## 📦 所以整个函数做的事是：

对于每个 latent variable $z_i$：

- 返回：
  - 标量 loss（无梯度）：$\boxed{\log q(z_i) - \log p(z_i)}$
  - 可导 loss（有梯度）：根据是否支持重参数，用 entropy 或 score function estimator 加上去

---

## 🧠 总结一下公式结构

最终对每个变量 $z_i$，你得到的是：

### 无梯度 loss：
\[
\text{loss}[z_i] = \log q(z_i) - \log p(z_i)
\]

### 有梯度 surrogate_loss：

- 如果支持重参数化：

\[
\text{surrogate\_loss}[z_i] = -\log p(z_i) + \text{Entropy}[q(z_i)]
\]

- 如果不支持重参数化：

\[
\text{surrogate\_loss}[z_i] = -\log p(z_i) + \log q(z_i) + \left( \log \frac{p(z_i)}{q(z_i)} \cdot \nabla \log q(z_i) \right)
\]

---

## 🎯 用图表示的话像这样：

```
        z_i ~ guide
          ↓
    ┌─────────────┐
    │ log q(z_i)  │──────────┐
    └─────────────┘          │
                             ▼
    ┌───────────────┐     surrogate_loss[z_i]
    │ entropy_term  │──▶  if reparam
    └───────────────┘
                             ▲
    ┌───────────────┐        │
    │ score_fn_term │──▶  if not reparam
    └───────────────┘
          ▲
          │
    ┌────────────┐
    │ log p(z_i) │───────→  - log p(z_i) for both
    └────────────┘
```

---
