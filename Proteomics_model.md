## 蛋白质组学机器学习模型思路 ##
---
## 数据处理-Snakemake ##
input_file = "0012_spsion_zixuan.csv"

第一个数据分割板块：split

做下列步骤：
1. 将 NCE 拆分成两列：fragmentation（只包含字母），collision_energy（只包含数字）
2. 只保留这些列：top10_SN_ratio，PrecusorCharge，Peptide，fragmentation，collision_energy
3. 拆分数据集：
- 从Peptide 列中提取所有唯一的肽段序列。
- 使用 pandas.Series.unique() 方法去除重复项，只保留不重复的肽段。这意味着分割是以肽段级别（而不是行级别）进行的。
- 将所有独特肽段使用分层分割（Stratified Split）

计算肽段长度：使用 df['Peptide'].str.len() 计算每个肽段的字符长度。
分桶（Binning）：将长度分为若干区间（例如按分位数或固定区间），生成一个分层变量。
分层分割：在 train_test_split 中使用 stratify 参数，按长度桶进行分割。
验证分布：检查分割后各子集的长度分布是否一致

- 将 训练/验证/测试集 比例调整为 60/20/20

输出：得到
        train="data/train_split.csv",
        val="data/val_split.csv",
        test="data/test_split.csv"

第二个数据预处理板块：preprocess
1. 将 top10_SN_ratio 这一列的数定义为 y_true, 并做以下处理：
- 按肽段最大值归一化：
对于每个肽段，找到其 10 个 top10_SN_ratio 中的最大值，将所有值除以该最大值
- 对数变换：
对归一化后的值应用 np.log1p，缓解可能的偏态（例如某些肽段内仍有大值差异）
2. 全局标准化（用训练集的 y_min 和 y_max）
3. 由于 y_true 有高值和低值少、中值多的不平衡问题，所以对于 y_true 进行加权采样，将加权采样分别应用于训练集，而测试集验证集未使用加权采样。增加高值和低值样本的采样频率，使模型更关注这些稀有区域，从而缓解中值主导的问题。
4. 分层分割后，验证长度分布和 y_true 分布，确保效果

注：（用训练集的 y_min 和 y_max）分别一个一个对 train,val,test 数据集进行处理，防止信息泄露

第三个特征处理板块：feature_process
1. 🧬 Peptide 序列 (Peptide)
- 用自定义的 parser 处理氧化修饰（M* → "M(ox)"
- 然后转成固定长度（32）的整数数组，用于表示氨基酸种类。

2. ⚡ Precursor Charge (PrecusorCharge)
- 用 OneHotEncoder 进行独热编码。

3. 🔥 Collision Energy (collision_energy)
- 同样是 OneHotEncoder

4. 💣 Fragmentation (fragmentation)
- 手动映射 ['cid', 'hcd'] → one-hot

5. charge_tensor, ce_tensor, pep_tensor, frag_tensor 全都用 torch.cat(..., dim=1) 拼在了一起作为模型输入。

6.归一化之后的 y_true = top10_SN_ratio，转成了 torch.float32 类型，配合模型训练

注：也是分别一个一个对 train,val,test 数据集进行处理，防止信息泄露


## 建立模型 ##




