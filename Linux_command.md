# 🧠 Linux / Bash 基础命令与概念速查表

---

## 📂 基础概念的使用

### 1. **终端（Terminal）**
- **怎么用**：打开终端程序（如 Ubuntu 的 Terminal 或 macOS 的 iTerm），直接输入命令。
- **例子**：
  ```bash
  echo Hello
  ```

---

### 2. **Shell**
- **怎么用**：默认就在用（通常是 Bash），可以用 `bash` 执行脚本。
- **例子**：
  ```bash
  bash script.sh
  ```

---

### 3. **命令（Command）**
- **怎么用**：直接敲命令名加参数。
- **例子**：
  ```bash
  ls -l
  ```

---

### 4. **路径（Path）**
- **怎么用**：在命令中指明文件或目录位置。
- **例子**：
  ```bash
  cd /home/user
  ```

---

## 📁 常用文件和目录命令

### 5. **cd** 切换目录
```bash
cd ~/Documents
```

### 6. **ls** 列出目录内容
```bash
ls -a
```

### 7. **pwd** 显示当前路径
```bash
pwd
```

### 8. **mkdir** 新建目录
```bash
mkdir test
```

### 9. **rm** 删除文件或目录
```bash
rm file.txt
rm -r folder/
```

### 10. **cp** 复制文件或目录
```bash
cp file1.txt file2.txt
```

### 11. **mv** 移动或重命名
```bash
mv file.txt /home
mv file.txt newname.txt
```

### 12. **cat** 显示文件内容
```bash
cat note.txt
```

---

## 🔒 权限与执行相关命令

### 13. **chmod** 修改权限
```bash
chmod +x script.sh
```

### 14. **chown** 修改文件所有者
```bash
chown john file.txt
```

### 15. **sudo** 用管理员权限执行
```bash
sudo apt update
```

### 16. **./** 运行当前目录下的程序或脚本
```bash
./run.sh
```

---

## 🧪 环境与配置相关命令

### 17. **source** 或 `.`：加载环境配置文件
```bash
source ~/.bashrc
```

### 18. **export** 设置环境变量
```bash
export PATH=$PATH:/usr/bin
```

### 19. **bash** 执行 Bash 脚本
```bash
bash myscript.sh
```

### 20. **env** 查看环境变量
```bash
env | grep PATH
```

---

## 🔍 其他实用命令

### 21. **man** 查看命令手册
```bash
man cp
```

### 22. **grep** 搜索文件内容
```bash
grep "error" log.txt
```

### 23. **|（管道）**：把前一个命令输出作为下一个命令输入
```bash
ls -l | grep "txt"
```

### 24. **& 和 &&**
- 后台运行：
  ```bash
  sleep 10 &
  ```
- 顺序执行（前面成功才跑后面）：
  ```bash
  mkdir test && cd test
  ```

### 25. **nano / vim** 编辑文件
```bash
nano config.txt
# 保存 Ctrl+O，退出 Ctrl+X
```

---

## 🗂️ 文件系统与路径符号

### 26. **/** 根目录
```bash
cd /
```

### 27. **~** 主目录
```bash
cd ~
```

### 28. **..** 上一级目录
```bash
cd ..
```

### 29. **.*（隐藏文件）**
```bash
ls -a
cat .bashrc
```

---

## ✅ 特殊命令详解

### 30. **source**
- **功能**：让脚本的设置（如 alias、环境变量）在当前终端立即生效。
- **例子**：
  ```bash
  source ~/.bashrc
  ```

---

### 31. **type**
- **功能**：显示命令或名字的“来源”。
- **用途**：用来检查一个命令到底是：
  - alias（别名）
  - shell 内置命令（builtin）
  - 外部可执行程序（binary）
  - 函数（function）
  - shell 关键字（keyword）

- **例子**：
  ```bash
  type ls
  # 输出：ls is /bin/ls

  type cd
  # 输出：cd is a shell builtin

  type mamba
  # 输出：mamba is aliased to `micromamba`

  type python
  # 输出：python is /usr/bin/python
  ```
### 31. ** -p **
- 自动创建中间不存在的目录，也不会报错

### 32. ** touch **
- 创建一个空文件（如果文件不存在），或者更新一下已有文件的时间戳（如果文件已经存在）
- 💡 因为 Python 把一个文件夹当作“模块”或“包”，前提是这个文件夹里有个 __init__.py 文件。哪怕这个文件是空的，也行。
- 所以：
```bash
touch utils/__init__.py
```
- = 我告诉 Python：“utils/ 是个包哦，你可以 import utils.xxx 啦～”

---

## 🧪 实战例子

### ✅ 安装工具（如 miniconda/micromamba）：
```bash
cd ~/Downloads
chmod +x install.sh
./install.sh
source ~/.bashrc
```

### ✅ 删除文件夹：
```bash
rm -r old_folder
```

---

## 📌 小贴士

- 用 `-h` 或 `--help` 查看命令帮助：
  ```bash
  cp --help
  ```

- 多敲多试，加深记忆（除了 `sudo rm -rf /`，千万别试😅）

- 推荐将常用设置（alias、路径等）写进 `~/.bashrc`，用 `source ~/.bashrc` 使其生效

