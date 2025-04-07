# 创建文件

## 创建 Snakefile.config
- 把路径、变量、参数抽离出主 Snakefile
- 🧠 单独定义 `dirbase` 和 `dirlbase`

在 Snakemake 工作流中，统一管理**数据输出路径**和**日志路径**，不仅让项目结构更清晰，而且能极大提升复现性与可维护性。

| 变量名     | 含义                      | 示例路径         | 用途说明 |
|------------|---------------------------|------------------|-----------|
| `dirbase`  | data base directory（数据根目录） | `data/`           | 所有分析步骤输出都放在这个目录下，比如：<br>📂 `data/raw/`<br>📂 `data/qc/`<br>📂 `data/results/` |
| `dirlbase` | log base directory（日志根目录） | `log/`            | 所有日志输出都集中在这里，结构对应于数据目录，比如：<br>📂 `log/qc/sample1.log`<br>📂 `log/align/sample2.log` |


