* **Inputs**

  * `peptides_file` (`pep`): must have `peptide` (+ your other cols like `fragmentation`, `collision_energy`, `rank`, `sps_total_intensity`).
  * `vali_file` (`val`): must have `peptide` (or `Trimmed Peptide`) and `reporter_total_intensity`, plus activation info.

* **Clean & dedupe**

  * Strip spaces in `peptide` on both tables.
  * In `pep`, keep the **first** row per `peptide` (as the “base” row to carry other columns).

* **Filter by reporter intensity**

  * Convert `reporter_total_intensity` to numeric.
  * Keep only rows with `reporter_total_intensity > RI_MIN` (default `RI_MIN = 0` → removes zeros).

* **Build settings from `vali_file`**

  * Create `setting = ActivationType + ActivationEnergy` (e.g., `CID25`, `CID34`).
  * Deduplicate `val` to unique `(peptide, setting)` rows (keep last).

* **Require both CID25 & CID34**

  * For each `peptide`, collect its settings from `val`.
  * Keep peptides **only if** the set equals `{"CID25","CID34"}` (exact pair).

* **Match (join)**

  * **Inner-join on `peptide`** → each kept peptide becomes **two rows** (one `CID25`, one `CID34`).

* **Set output values**

  * Override `fragmentation` and `collision_energy` from `setting` for each row.
  * If present:

    * `sps_total_intensity`: keep only for **CID25**; set **blank** for CID34.
    * `rank`: keep only for **CID25**; set **blank** for CID34.
  * Keep `reporter_total_intensity` from `vali_file`.

* **Why zeros matched before**

  * Previously there was no intensity filter; now `reporter_total_intensity > 0` is required.

* **Small example**

  ```
  pep (CID25+rank1):
  peptide, rank, sps_total_intensity, ...
  EDTEEHHLR, 1, 12345, ...

  val:
  peptide, Activation Type, Activation Energy, reporter_total_intensity
  EDTEEHHLR, CID, 25, 900
  EDTEEHHLR, CID, 34, 850

  output:
  peptide, ..., fragmentation, collision_energy, setting, reporter_total_intensity, rank, sps_total_intensity
  EDTEEHHLR, ..., CID, 25, CID25, 900, 1, 12345
  EDTEEHHLR, ..., CID, 34, CID34, 850,  , 
  ```

* **Tweaks**

  * Want “at least” CID25 & CID34 (and allow extra settings)? Replace equality with subset:

    * change `keep == want` to `want.issubset(keep)` in the code.


* **输入**

  * `peptides_file`（简称 *pep*）必须有 `peptide` 列（以及你已有的 `fragmentation`、`collision_energy`、`rank`、`sps_total_intensity` 等）。
  * `vali_file`（简称 *val*）必须有 `peptide`（或 `Trimmed Peptide` 会重命名为 `peptide`）和 `reporter_total_intensity`，并包含激活信息（`Activation Type` 与 `Activation/Collision Energy`）。

* **清洗与去重**

  * 两侧的 `peptide` 去空格。
  * 在 *pep* 中按 `peptide` 去重（保留首行），作为每个肽段的“基准信息”。

* **按 reporter 强度过滤**

  * 将 `reporter_total_intensity` 转为数值。
  * 只保留 `reporter_total_intensity > RI_MIN` 的行（默认 `RI_MIN = 0`，即剔除 0 与 NaN）。

* **由 *val* 构造 setting**

  * 生成 `setting = ActivationType + ActivationEnergy`（如 `CID25`、`CID34`）。
  * 仅保留 `(peptide, setting, reporter_total_intensity)`，并按 `(peptide, setting)` 去重。

* **要求同一肽段同时存在 CID25 与 CID34**

  * 对每个 `peptide` 收集其出现过的 `setting` 集合。
  * 仅保留集合**恰好等于** `{"CID25","CID34"}` 的肽段（刚好两种）。

* **匹配（连接）**

  * 在 `peptide` 上与 *pep* 做 **inner join**。
  * 每个保留的肽段在输出中会有 **两行**：一行 `CID25`、一行 `CID34`。

* **设置输出字段**

  * 用 `setting` 拆出的值覆盖：

    * `fragmentation`（如 `CID`）
    * `collision_energy`（如 `25` 或 `34`）
  * 若存在：

    * `sps_total_intensity`：**仅在 CID25 行保留**，CID34 行设为空字符串 `""`。
    * `rank`：**仅在 CID25 行保留**，CID34 行设为空字符串 `""`。
  * `reporter_total_intensity` 始终取自 *val*。

* **为什么之前 0 也会匹配上？**

  * 过去只按 `peptide` 合并，没有过滤 `reporter_total_intensity`，因此 0 也会被合并。现在先筛掉 `reporter_total_intensity <= RI_MIN` 的行后再做匹配。

* **迷你示例**

  ```
  pep（CID25+rank1 基表）:
  peptide, rank, sps_total_intensity, ...
  EDTEEHHLR, 1, 12345, ...

  val:
  peptide, Activation Type, Activation Energy, reporter_total_intensity
  EDTEEHHLR, CID, 25, 900
  EDTEEHHLR, CID, 34, 850

  输出:
  peptide, ..., fragmentation, collision_energy, setting, reporter_total_intensity, rank, sps_total_intensity
  EDTEEHHLR, ..., CID, 25, CID25, 900, 1, 12345
  EDTEEHHLR, ..., CID, 34, CID34, 850,  , 
  ```

* **可选调整**

  * 如果想放宽为“至少包含 CID25 与 CID34（允许多余 setting）”，把代码里集合判断从
    `keep == want` 改为 `want.issubset(keep)`。

