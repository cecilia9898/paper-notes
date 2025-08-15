* **读入 | Read**

  * `pep` = `peptides_{tag}.csv`
  * `val` = `zy_sl19_{tag}cid34.csv`（或别的组合）

* **清洗与去重 | Clean & dedupe**

  * 去空格：`peptide`
  * `pep` 按 `peptide` 去重保留首行

* **强度过滤 | Intensity filter**

  * `val.reporter_total_intensity` 转数值
  * 只保留 `> RI_MIN`（默认 0）

* **生成 setting | Build setting**

  * 从 `Activation Type` + `Energy` 得到如 `CID25` / `CID34`
  * 保留唯一 `(peptide, setting)`

* **目标与主条件 | Targets**

  * `want`：从 **vali 文件名**提取两种 setting（如 `{CID25, CID34}`）
  * `primary`：从 **pep 文件名**提取主 setting（如 `CID25`）

* **筛肽段 | Keep peptides**

  * 只保留在 `val` 里 **恰好等于** `want` 的肽段（正好两种）

* **合并与输出 | Merge & output**

  * 按 `peptide` 内连接 → 每个肽段两行（各自的 setting）
  * 用 `setting` 覆盖 `fragmentation` / `collision_energy`
  * 非 `primary` 行把 `rank`、`sps_total_intensity` 置空
  * 写出 CSV

* **小例子 | Mini example**

  * `peptides_cid25.csv` + `zy_sl19_cid25cid34.csv`
    ⇒ `primary = CID25`, `want = {CID25, CID34}`
    ⇒ 输出两行：`CID25` 行保留 `rank/sps_total_intensity`，`CID34` 行置空

* **可调 | Tunables**

  * 严格大于 0：`RI_MIN = 1e-9`
  * 允许 0：`RI_MIN = -1`（或去掉过滤）
