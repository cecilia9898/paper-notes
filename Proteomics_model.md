## 蛋白质组学机器学习模型思路 ##
---
## 数据处理-Snakemake ##
input_file = "0012_spsion_zixuan.csv"

第一个数据预处理板块：preprocess

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





第二个处理特征板块：
1. 将 top10_SN_ratio 这一列的数定义为 y_true, 并做以下处理：
    y_true = np.array(top10_SN_ratio)
    y_true = np.log1p(y_true)
    y_min, y_max = np.min(y_true), np.max(y_true)
    y_true = (y_true - y_min) / (y_max - y_min)


