# Git 上传项目操作笔记

## 1. 初始化 Git 仓库并首次提交

```bash
cd ~/Documents/sim_snakemake_1

git init
git add .
git commit -m "Initial commit"

```

## 2. 在 GitHub 上创建新仓库

- 网页创建新仓库：https://github.com/new
- 不勾选 README、LICENSE、.gitignore
- 仓库地址示例：https://github.com/cecilia9898/dictys_snakemake.git

## 3. 添加远程并推送

```bash
git remote add origin https://github.com/cecilia9898/dictys_snakemake.git
git branch -M main
git push -u origin main
```

## 4. 删除 GitHub 上多余文件

```bash
git pull origin main
git rm a.txt 1.ipynb EOF
git commit -m "Remove unnecessary files"
git push
```

## 5. 添加 README.md 文件

```bash
nano README.md
# 粘贴说明内容后保存退出

git add README.md
git commit -m "Add project README"
git push
```

## 6. 可选：添加 .gitignore 文件

```bash
echo "*.ipynb" >> .gitignore
echo "*.log" >> .gitignore
echo "log/" >> .gitignore

git add .gitignore
git commit -m "Add .gitignore"
git push
```

## 7. GitHub 仓库地址

```
https://github.com/cecilia9898/dictys_snakemake
```

## 8. 添加第二个 remote，例如叫 backup
```bash
git remote add backup https://github.com/用户名/仓库名.git
```
- 推送到第一个仓库（origin）
```bash
git push origin main
```

- 推送到第二个仓库（backup）
```bash
git push backup main
```
- 配置让 git push 一次性推到多个远程仓库
```bash
git remote set-url --add --push origin https://github.com/your_old_repo.git
git remote set-url --add --push origin https://github.com/你的用户名/new_repo.git
```
## 9. 创建 toc.md 去记录每步流程
```bash
cat > toc.md << EOF
```

## 10. 更新 github repo 代码

```bash

git status
cd ~/Documents/sim_snakemake_1
git add .
git commit -m "Update workflow"
git push origin main
git push backup main

```

## 11. 记录 github repo 以及关联的命名

- origin main ~ cecilia9898/dictys_snakemake
- backup main ~ grnlab/private_netlib_pipeline
- sfcm main ~ grnlab/private_netlib


## 12. 撤回 git init 请求
```bash
cd "/Users/zixuanye/Desktop/3 - Yu Lab"
rm -rf .git
```

## 13. 避免 push 被拦住：拉一下再推（rebase）
```bash
git pull --rebase origin main
git push
```

好，那我帮你整理一个**只用 SSH 的完整 CLI 版**，照着走就能把本地仓库推到 GitHub 👇

---

# GitHub 仓库设置 & 推送（SSH 版）

## 0) 初始化本地仓库（如果还没做）

```bash
cd /mnt/c/Users/cecilia.DESKTOP-8JQ74OI/Documents/03_DIA_QUANT
git init
echo "# 03_DIA_QUANT" > README.md
git add README.md
git commit -m "chore: initial commit"
git branch -M main
```

---

## 1) 确认 SSH 能连上 GitHub

```bash
ssh -T git@github.com
```

* 如果输出：

  ```
  Hi cecilia9898! You've successfully authenticated...
  ```

  说明 SSH key 已经配置好，可以直接用。
* 如果失败：

  ```bash
  eval "$(ssh-agent -s)"
  ssh-add ~/.ssh/id_ed25519   # 或你实际的私钥名，比如 id_ed25519_wsl
  ssh -T git@github.com
  ```

---

## 2) 设置远端地址为 SSH

```bash
git remote add origin git@github.com:YuLabProteomics/03_DIA_QUANT.git
# 如果已经存在 origin：
git remote set-url origin git@github.com:YuLabProteomics/03_DIA_QUANT.git
```

检查：

```bash
git remote -v
```

应该显示：

```
origin  git@github.com:YuLabProteomics/03_DIA_QUANT.git (fetch)
origin  git@github.com:YuLabProteomics/03_DIA_QUANT.git (push)
```

---

## 3) 推送到远端

```bash
git push -u origin main
```

以后只要做了修改，三步即可：

```bash
git add -A
git commit -m "update something"
git push
```

---

## 常见问题

* `src refspec main does not match any` → 说明你还没 commit，回到 **0) 初始化** 做一次提交。
* `Permission denied (publickey)` → SSH key 没加到 GitHub，去 GitHub **Settings → SSH and GPG keys** 把公钥加上。
* 如果有多个 SSH key，记得用 `ssh-add` 确保正确的 key 在 agent 里。

---

# 🚀 GitHub SSH 推送步骤（整理版）

### 1. 启动 agent 并加载 SSH key

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

### 2. 切换远端到 SSH

```bash
git remote set-url origin git@github.com:YuLabProteomics/04_FDRG.git
git remote -v   # 应显示 git@github.com:YuLabProteomics/04_FDRG.git
```

### 3. 检查 SSH 认证

```bash
ssh -T git@github.com
# 期望输出: "Hi cecilia9898! You've successfully authenticated..."
```

### 4. 确认本地分支是 main

```bash
git branch -M main
```

### 5. 如果仓库还没 commit，先提交一次

```bash
git add .
git commit -m "Initial commit"
# 已经有提交的话会提示 nothing to commit，可以忽略
```

### 6. 推送到远端

```bash
git push -u origin main
```

---

# 🎯 目标

把误提交的超大文件（比如 `.mzML`）**从所有历史里清除**，让仓库变瘦；然后**用 SSH** 正确强推到 GitHub 远端。

---

## 🧭 快速路线图（Checklist）

1. ✅ 备份仓库（可选但建议）
2. ✅ 安装 `git-filter-repo`
3. ✅ 找出并删除历史中的大文件/目录
4. ✅ 加 `.gitignore` 防再犯
5. ✅ 瘦身：清 reflog + GC
6. ✅ 配置 SSH 远端并强推
7. ✅ 自测 + 让协作者同步干净历史
8. ✅（可选）改用 Git LFS / DVC 管数据

---

## 0) 进入项目 & 备份（稳）

```bash
cd /mnt/c/Users/cecilia.DESKTOP-8JQ74OI/Documents/04_FDRG
cd ..
cp -r 04_FDRG 04_FDRG_backup_before_clean
```

---

## 1) 安装 `git-filter-repo`

```bash
python3 -m pip install --user git-filter-repo
export PATH=$HOME/.local/bin:$PATH   # 让本 shell 找到它
```

---

## 2) 分析仓库里最大的历史对象（可视化心里有数）

```bash
cd 04_FDRG
git filter-repo --analyze
# 看 .git/filter-repo/analysis/largest-blobs.txt
```

---

## 3) 真正的清理（按你的情况三选一或组合）

### A. 删除整个误传目录（例如原始 mzML 都在 `00_mzml/`）

```bash
git filter-repo --force --path 00_mzml/ --invert-paths
```

### B. 按后缀清理所有历史中的大文件

```bash
git filter-repo --force \
  --path-glob '*.mzML' \
  --path-glob '*.raw' \
  --path-glob '*.mzXML' \
  --invert-paths
```

### C. 粗暴按大小阈值清理（比如 >90MB 全砍）

```bash
git filter-repo --force --strip-blobs-bigger-than 90M
```

> 说明：`--invert-paths` 表示“把匹配的**从历史里移除**”。
> 建议优先用 A 或 B；C 当兜底。

---

## 4) 防再犯：写 `.gitignore`

```bash
printf "00_mzml/\n*.mzML\n*.raw\n*.mzXML\n" >> .gitignore
git add .gitignore
git commit -m "chore: ignore raw/mzML data"
```

---

## 5) 彻底瘦身（不做这步，磁盘体积可能看不出明显变小）

```bash
# 清理 filter-repo 留下的原始引用
git for-each-ref --format='delete %(refname)' refs/original | git update-ref --stdin || true
rm -rf .git/refs/original/

# 过期所有 reflog
git reflog expire --expire=now --all

# 强力 GC
git gc --prune=now --aggressive
```

---

## 6) 配置 SSH 远端并强推

### 6.1 设置/修正远端

```bash
git remote -v            # 看看有没有远端
# 如果没有：
git remote add origin git@github.com:YuLabProteomics/04_FDRG.git
# 如果是 HTTPS，改成 SSH：
git remote set-url origin git@github.com:YuLabProteomics/04_FDRG.git
```

### 6.2 验证 SSH 身份（应显示你的账号名）

```bash
ssh -T git@github.com
# 期望输出：Hi cecilia9898! You've successfully authenticated...
```

### 6.3 强推（重写历史必须强推）

```bash
git push -u origin main --force-with-lease
```

---

## 7) 验证干净度 & 协作者同步

**本地再扫一遍最大对象：**

```bash
git filter-repo --analyze
# 再看 largest-blobs.txt，确认没有 100MB+ 的怪物
git count-objects -vH
```

**协作者同步（告诉队友照抄）：**

```bash
git fetch origin
git checkout main
git reset --hard origin/main
git gc --prune=now
```

---

## 8)（可选）如果你必须把大文件放 GitHub → Git LFS

> 不建议放成吨 DIA 数据；LFS 有**存储/带宽配额**，超了就拉跨。

```bash
git lfs install
git lfs track "*.mzML" "*.raw" "*.mzXML"
git add .gitattributes
git commit -m "chore: track mass-spec data with Git LFS"

# 把历史中的这些类型迁移为 LFS 指针（也会重写历史）
git lfs migrate import --include="*.mzML,*.raw,*.mzXML"
git push -u origin main --force-with-lease
```

---

## 🧯 常见报错速查

* **`fatal: 'origin' does not appear to be a git repository`**
  → 远端没配置：`git remote add origin git@github.com:YuLabProteomics/04_FDRG.git`

* **`Repository not found`**
  → 远端仓库不存在或你没权限。请在 org 下创建仓库，并给你写权限；或在 SSH key 页面 **Enable SSO**。

* **还是提示 100MB 限制**
  → 清理不彻底。再跑 `git filter-repo --analyze` 看 `largest-blobs.txt`，对症下刀（目录/后缀/大小）。

* **SSH 还是不通**
  → `ssh -T git@github.com -v` 看它到底用了哪把 key；确认 `~/.ssh/id_ed25519.pub` 已加到 GitHub；WSL/Windows 不要混用过期的 HTTPS 凭据（清一下 Windows Credential Manager 里的 github 项）。

---



