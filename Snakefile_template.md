# 创建文件

## 创建 Snakefile.config
- 把路径、变量、参数抽离出主 Snakefile
- 🧠 单独定义 `dirbase` 和 `dirlbase`

在 Snakemake 工作流中，统一管理**数据输出路径**和**日志路径**，不仅让项目结构更清晰，而且能极大提升复现性与可维护性。

| 变量名     | 含义                      | 示例路径         | 用途说明 |
|------------|---------------------------|------------------|-----------|
| `dirbase`  | data base directory（数据根目录） | `data/`           | 所有分析步骤输出都放在这个目录下，比如：<br>📂 `data/raw/`<br>📂 `data/qc/`<br>📂 `data/results/` |
| `dirlbase` | log base directory（日志根目录） | `log/`            | 所有日志输出都集中在这里，结构对应于数据目录，比如：<br>📂 `log/qc/sample1.log`<br>📂 `log/align/sample2.log` |


# 常见错误

## 🐍 Snakemake 中 Python 函数使用易错点总结

在 Snakemake 中，你可以使用 Python 函数（如 `pdir(...)`, `ldir(...)`）来生成路径，但使用的位置有严格限制！

## ✅ 合法的位置：可以调用 Python 函数的字段

| 字段       | 支持函数调用 | 说明 |
|------------|---------------|------|
| `input:`   | ✅             | 支持调用自定义函数生成输入路径。 |
| `output:`  | ✅             | 支持调用函数生成输出文件路径。 |
| `log:`     | ✅             | 支持函数生成日志文件路径。 |
| `params:`  | ✅             | 支持函数，用于将路径或参数传入 `shell:`。 |
| `run:`     | ✅✅✅          | 是纯 Python 代码块，完全自由使用函数。 |


## ❌ 非法的位置：**不能**调用函数的字段

| 字段       | 支持函数调用 | 原因 |
|------------|---------------|------|
| `shell:`   | ❌             | 是字符串模板，只能插入变量（如 `{input}`、`{output}`），**不能执行函数**。 |
| `script:`  | ❌             | 和 `shell:` 一样，路径必须是纯字符串或变量，不解析函数。 |


## 🚨 错误示例（❌ 不要这么写）

```python
# ❌ 错误：shell 中不能执行函数
shell:
    "python {pdir('scripts/my_script.py')}"  # 报错！
```

## ✅ 正确示例（先放入 params 或 output，再引用）

```python
# ✅ 正确：先用函数生成路径，再传给 shell
rule myrule:
    output:
        result = pdir("result.csv")
    shell:
        "python do_something.py > {output.result}"
```

## 💡 小贴士

- 如果你想在 `shell:` 中用函数生成的路径，**一定要提前在 `params`, `input`, `output`, `log` 中定义好**。
- 使用 `.named` 风格（如 `{output.result}`）更清晰，可读性更高。
- 如果逻辑复杂，考虑直接用 `run:` 来写纯 Python，避免绕来绕去。


## 🧠 总结口诀

> 📢 **"shell 是模板，output 是代码。"**
> 
> 想调用函数？放到 `output` 或 `params`，再在 `shell` 里乖乖用变量！

