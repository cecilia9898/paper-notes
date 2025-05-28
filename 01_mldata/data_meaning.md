## MS1 Spectrum（`.mzML`）

| 字段名                          | 类型           | 说明                                  |
| ---------------------------- | ------------ | ----------------------------------- |
| `index`                      | int          | 谱图在文件中的序号（从 0 开始）                   |
| `id`                         | str          | 此谱图的唯一标识符                           |
| `defaultArrayLength`         | int          | `m/z array` 和 `intensity array` 的点数 |
| `scanList`                   | dict         | 扫描参数，包括保留时间、极性等                     |
| `MS1 spectrum`               | bool         | 标记该谱图为 MS1                          |
| `ms level`                   | int          | 质谱层级，这里为 1（MS1）                     |
| `positive scan`              | bool         | 正离子模式（如使用）                          |
| `centroid spectrum`          | bool         | 数据是否为质心格式                           |
| `base peak m/z`              | float        | 最强峰的 m/z 值                          |
| `base peak intensity`        | float        | base peak 的强度                       |
| `total ion current`          | float        | 所有离子总强度（TIC）                        |
| `FAIMS compensation voltage` | float (可选)   | FAIMS 补偿电压（如果适用）                    |
| `lowest observed m/z`        | float        | 此谱图中的最小 m/z                         |
| `highest observed m/z`       | float        | 此谱图中的最大 m/z                         |
| `count`                      | int          | 数据点数（通常等于 `defaultArrayLength`）     |
| `m/z array`                  | list\[float] | 该谱图中的 m/z 值数组                       |
| `intensity array`            | list\[float] | 对应的强度数组                             |

---

## MS2 Spectrum（`.mzML`）

| 字段名                          | 类型           | 说明                                     |
| ---------------------------- | ------------ | -------------------------------------- |
| `index`                      | int          | 谱图在文件中的索引（从0开始）                        |
| `id`                         | str          | 唯一标识该谱图的字符串                            |
| `defaultArrayLength`         | int          | `m/z array` 和 `intensity array` 的数据点数量 |
| `scanList`                   | dict         | 包含扫描参数，如保留时间（retention time）等          |
| `precursorList`              | dict         | 前体离子信息（m/z, 电荷, 强度, isolation window）  |
| `MSn spectrum`               | bool         | 标记该谱图为 MSn 类型（本例为 MS2）                 |
| `ms level`                   | int          | 质谱层级，这里为 2 表示 MS2 谱图                   |
| `positive scan`              | bool         | 正离子扫描（负离子为 `negative scan`）            |
| `centroid spectrum`          | bool         | 说明该谱图是质心格式（非连续 profile）                |
| `base peak m/z`              | float        | 强度最大峰的 m/z 值                           |
| `base peak intensity`        | float        | base peak 的强度值                         |
| `total ion current`          | float        | 所有离子强度的总和                              |
| `FAIMS compensation voltage` | float (可选)   | FAIMS 补偿电压，只有使用 FAIMS 时有该字段            |
| `lowest observed m/z`        | float        | 此谱图中观测到的最小 m/z                         |
| `highest observed m/z`       | float        | 此谱图中观测到的最大 m/z                         |
| `scan description`           | str (可选)     | 扫描描述字符串（如有）                            |
| `count`                      | int          | 数据点数量，通常等于 `defaultArrayLength`        |
| `m/z array`                  | list\[float] | 实际碎片离子的 m/z 数组                         |
| `intensity array`            | list\[float] | 对应碎片的强度数组                              |

---

## MS1 和 MS2 在结构上非常相似，区别主要是：

- MS2 多了 precursorList 字段用于记录前体信息；

- MS1 有 MS1 spectrum 标志，而 MS2 是 MSn spectrum

---

当然可以，以下是上述内容的中文版，依然保持 Markdown 格式，适合整理为项目文档或笔记。

---

# 搜索结果数据各列含义说明

以下表格解释了蛋白质组学中搜索引擎输出结果表中的每一列，通常用于肽段-谱图匹配（Peptide-Spectrum Match, PSM）分析。

| **列名**              | **说明**                                                                                       |
| ------------------- | -------------------------------------------------------------------------------------------- |
| `scanf`             | MS2（二级质谱）扫描的唯一标识符，常见格式如 `controllerType=0 controllerNumber=1 scan=1234`，用于对照 `.mzML` 文件中的谱图。 |
| `charge`            | 肽段的前体离子电荷状态（如 +2, +3），用于计算理论的 m/z 值。                                                         |
| `peptide_score1`    | 搜索引擎对该肽段与谱图匹配的评分（如 XCorr、Hyperscore 或 E-value），评分高低代表匹配可信度（具体取决于打分方式）。                       |
| `og_pre_m_z`        | 原始的母离子（precursor）m/z，可能未经过校准，常用于与理论值比对。                                                      |
| `activation_type`   | MS2 的碎裂方式，如 CID（碰撞诱导解离）、HCD（高能碰撞）、ETD（电子转移解离）等。                                              |
| `activation_energy` | 碎裂时的能量值，单位常为 NCE（归一化碰撞能），会影响碎片离子的强度和类型。                                                      |
| `peptide`           | 完整的肽段序列，包括修饰信息，例如 `AC[+57]DEFG`。                                                             |
| `trimmed_peptide`   | 去除修饰后的肽段序列，也可能去除了接头或标签，常用于计算理论的 b/y 离子质量。                                                    |
| `reference`         | 该肽段匹配到的蛋白参考序列名，可能是 Uniprot ID、RefSeq ID 或者 FASTA 文件中的蛋白标识。                                   |
| `search_name`       | 此条记录来自哪个搜索任务或哪个批次，用于合并多次搜索结果。                                                                |
| `parent_scan`       | 前体离子（母离子）所在的 MS1 扫描号，可用于进一步提取 MS1 特征或定量。                                                     |
| `gene_symbol`       | 匹配蛋白所属的基因缩写名称，例如 `TP53`、`ACTB`，通常由注释工具添加。                                                    |
| `m_z`               | 理论或匹配到的碎片离子 m/z 值，用于与实验谱图比对。                                                                 |
| `intensity`         | 上述碎片离子对应的实验谱图峰强度。                                                                            |

---

## 使用提示

* 使用 `trimmed_peptide` 计算该肽段的理论 **b/y 离子**。
* 利用 `scanf` 匹配 `.mzML` 文件中的 MS2 谱图，获取该扫描的 `m/z array` 和 `intensity array`。
* 结合 `charge` 和 `peptide_score1` 筛选高可信度的鉴定结果。
* 将理论碎片 m/z 与实际谱图中的 `m_z` 和 `intensity` 进行匹配，以评估碎片支持程度。

---

