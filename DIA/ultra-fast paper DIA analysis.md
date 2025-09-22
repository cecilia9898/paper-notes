# （Astral，300×2Th，380–980 m/z，三物种+contams）

---

# 方案 A｜先建库、再分析（最稳妥）

**A1. 生成 in-silico speclib（只跑一次就能一直复用）**

```powershell
& "C:\DIA-NN\2.0\diann.exe" `
  --fasta "D:\three_species_plus_contams.fasta" `
  --library "C:\DIA-NN\2.0\libs\three_plus_contams_20250922.speclib" `
  --gen-spec-lib --predictor `
  --cut "Trypsin/P" --missed-cleavages 1 `
  --met-excision 1 `
  --fixed-mods "Carbamidomethyl (C)" `
  --var-mods "Oxidation (M)" `
  --min-pep-len 7 --max-pep-len 30 `
  --precursor-range 380 980 `
  --fragment-range 200 1800 `
  --threads 8
```

**A2. 用 speclib 跑 Astral DIA**

```powershell
& "C:\DIA-NN\2.0\diann.exe" `
  --f "D:\PXD046444_mzml\mzML\*.mzML" `
  --out "D:\DIA-NN\outputs\astral_20250922_report.parquet" `
  --lib "C:\DIA-NN\2.0\libs\three_plus_contams_20250922.speclib" `
  --use-window-file "D:\astral_windows.csv" `
  --mass-acc 0 --mass-acc-ms1 0 `
  --mbr 1 --matrices --qvalue 0.01 `
  --threads 8
```

---

# 方案 B｜一把梭：建库 + 分析同时进行

（DIA-NN 会先按 FASTA 预测 speclib，再立刻用它分析你的 mzML）

```powershell
& "C:\DIA-NN\2.0\diann.exe" `
  --f "D:\PXD046444_mzml\mzML\*.mzML" `
  --out "D:\DIA-NN\outputs\astral_20250922_report.parquet" `
  --fasta "D:\three_species_plus_contams.fasta" `
  --gen-spec-lib --predictor `
  --library "C:\DIA-NN\2.0\libs\three_plus_contams_20250922.speclib" `
  --use-window-file "D:\astral_windows.csv" `
  --cut "Trypsin/P" --missed-cleavages 1 `
  --met-excision 1 `
  --fixed-mods "Carbamidomethyl (C)" `
  --var-mods "Oxidation (M)" `
  --min-pep-len 7 --max-pep-len 30 `
  --precursor-range 380 980 `
  --fragment-range 200 1800 `
  --mass-acc 0 --mass-acc-ms1 0 `
  --mbr 1 --matrices --qvalue 0.01 `
  --threads 8
```

---

# 方案 C｜只分析（你已经在 GUI 里建过库了）

```powershell
& "C:\DIA-NN\2.0\diann.exe" `
  --f "D:\PXD046444_mzml\mzML\*.mzML" `
  --out "D:\DIA-NN\outputs\astral_20250922_report.parquet" `
  --lib "C:\DIA-NN\2.0\libs\three_plus_contams_20250922.speclib" `
  --use-window-file "D:\astral_windows.csv" `
  --mass-acc 0 --mass-acc-ms1 0 `
  --mbr 1 --matrices --qvalue 0.01 `
  --threads 8
```
<img width="1942" height="1171" alt="image" src="https://github.com/user-attachments/assets/b0b3906b-b732-41b1-abf6-17f5f350b9f8" />
