🎯 Pipeline 整体设计
1. 数据预处理模块（Data Preprocessing）
读取肽段序列文件 (.csv)，并解析肽段与修饰信息。

调用 parse_seq(seq) 函数实现。

2. 离子质量计算模块（Ion Mass Calculation）
计算肽段的理论 b、y 离子质量。

调用 ions_mass(seq) 与 calculate_mass() 函数实现。

3. 谱图匹配模块（Spectrum Matching）
从 mzML 文件读取实际谱图。

对比理论与实验的谱图，计算 ppm，确定匹配情况。

调用 spec_match_ppm() 与 t_mz() 函数实现。

4. SPS离子筛选模块（SPS Ion Selection）
基于老师要求的严格条件：

① 保留强度排名前200个的 MS2 离子

② 排除 b1/y1 离子

③ 保留所有 b 离子和以 K 结尾的 y 离子

④ 仅保留 m/z 大于等于400的离子

⑤ 排除在 MS1 precursor m/z 范围 -50 到 +5 内的离子

5. 输出结果模块（Output Results）
每个模块的输出结果导出 CSV 文件，便于查看比对结果。

📌 推荐的 Pipeline 结构
以下是推荐的结构化代码组织：

bash
Copy
Edit
pipeline/
├── input/
│   ├── peptides.csv
│   └── spectra.mzML
├── modules/
│   ├── preprocess.py         # 数据预处理 (parse_seq)
│   ├── ion_calculation.py    # 计算离子质量 (ions_mass, calculate_mass)
│   ├── spectrum_matching.py  # 谱图匹配 (spec_match_ppm, t_mz)
│   └── sps_selection.py      # SPS离子筛选 (自定义)
├── results/
│   ├── ion_masses.csv
│   ├── matched_spectra.csv
│   └── selected_sps_ions.csv
└── main.py                    # 主程序，调用各个模块
