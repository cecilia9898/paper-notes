# Git 上传项目操作笔记

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

## 创建 symlink 和 利用 alias 简化激活环境的命令
```bash
#先进入环境目录
cd /home/zixuan/micromamba/envs
ls

#加入 symlink
ln -s sim_env2 env
#ln = link, -s = symbolic link

#加 alias
nano ~/.bashrc
alias mamba='micromamba'
source ~/.bashrc
```
最后只用输入下面命令就可以轻松激活环境

```
mamba activate env
```

