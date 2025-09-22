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





