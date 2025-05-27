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
