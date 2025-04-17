# Git 上传 Snakemake 项目操作笔记

记录从本地初始化 Git 仓库到上传 GitHub、删除多余文件和添加 README 的完整操作流程。

## 1. 初始化 Git 仓库并首次提交

```bash
cd ~/Documents/sim_snakemake_1

git init
git add .

# 添加 MIT License
curl -o LICENSE https://raw.githubusercontent.com/github/choosealicense.com/gh-pages/_licenses/mit.txt
git add LICENSE

git commit -m "Initial commit: Snakemake workflow with MIT License"
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

