### 基础概念的使用
1. **终端（Terminal）**  
   - 怎么用：打开电脑的终端程序（比如 Ubuntu 的 Terminal，或者 Mac 的 iTerm），敲命令就行。
   - 例子：随便敲 `echo Hello`，屏幕会显示 “Hello”。

2. **Shell**  
   - 怎么用：默认就在用（通常是 Bash），不用特意管，但可以用 `bash` 跑脚本。
   - 例子：`bash script.sh` 运行一个脚本文件。

3. **命令（Command）**  
   - 怎么用：直接敲命令名，加参数或目标。
   - 例子：`ls -l`（列文件，错了，Linux 是 `ls`，我前面晕了，Windows 才用 `dir`），列出当前目录内容。

4. **路径（Path）**  
   - 怎么用：用在命令里指明位置。
   - 例子：`cd /home/user` 跳转到 `/home/user` 目录。

---

### 常用命令的使用
5. **cd**  
   - 怎么用：`cd 路径`，切换目录。
   - 例子：`cd ~/Documents` 去你的文档文件夹。

6. **ls**  
   - 怎么用：敲 `ls`（抱歉前面写错了，Linux 就是 `ls`），列目录内容。
   - 例子：`ls -a` 列出所有文件（包括隐藏的）。

7. **pwd**  
   - 怎么用：直接敲 `pwd`，显示当前路径。
   - 例子：`pwd` 输出 `/home/user`（如果你在这儿）。

8. **mkdir**  
   - 怎么用：`mkdir 名字`，新建文件夹。
   - 例子：`mkdir test` 创建一个叫 `test` 的文件夹。

9. **rm**  
   - 怎么用：`rm 目标`，删除文件或文件夹（小心用！）。
   - 例子：`rm file.txt` 删除文件，`rm -r folder` 删除文件夹。

10. **cp**  
    - 怎么用：`cp 源 目标`，复制东西。
    - 例子：`cp file1.txt file2.txt` 复制文件。

11. **mv**  
    - 怎么用：`mv 源 目标`，移动或改名。
    - 例子：`mv file.txt /home` 移动到 `/home`，或者 `mv file.txt newname.txt` 改名。

12. **cat**  
    - 怎么用：`cat 文件`，看文件内容。
    - 例子：`cat note.txt` 显示 `note.txt` 里的文字。

---

### 文件和权限的使用
13. **chmod**  
    - 怎么用：`chmod 权限 文件`，改文件权限。
    - 例子：`chmod +x script.sh` 让脚本可执行。

14. **chown**  
    - 怎么用：`chown 用户 文件`，改文件主人。
    - 例子：`chown john file.txt` 把文件给 john。

15. **sudo**  
    - 怎么用：`sudo 命令`，用管理员权限跑。
    - 例子：`sudo apt update` 更新软件包（Ubuntu 示例）。

16. **./**  
    - 怎么用：`./程序`，运行当前目录的东西。
    - 例子：`./run.sh` 执行脚本 `run.sh`。

---

### 环境和脚本的使用
17. **source** 或 `.`**  
    - 怎么用：`source 文件`，加载脚本到当前环境。
    - 例子：`source ~/.bashrc` 刷新终端设置。

18. **export**  
    - 怎么用：`export 变量=值`，设置环境变量。
    - 例子：`export PATH=$PATH:/usr/bin` 加个路径。

19. **bash**  
    - 怎么用：`bash 文件`，运行 Bash 脚本。
    - 例子：`bash myscript.sh` 执行脚本。

20. **env**  
    - 怎么用：敲 `env`，列环境变量。
    - 例子：`env | grep PATH` 看 `PATH` 变量。

---

### 其他实用的使用
21. **man**  
    - 怎么用：`man 命令`，查用法。
    - 例子：`man cp` 看 `cp` 的说明。

22. **grep**  
    - 怎么用：`grep 关键词 文件`，搜东西。
    - 例子：`grep "error" log.txt` 找日志里的 “error”。

23. **pipe（|）**  
    - 怎么用：`命令1 | 命令2`，把左边输出给右边。
    - 例子：`ls -l | grep "txt"` 筛选含 “txt” 的文件。

24. **&** 和 **&&**  
    - `&`：`命令 &`，后台跑。
    - 例子：`sleep 10 &` 让终端等 10 秒但不卡住。
    - `&&`：`命令1 && 命令2`，顺序执行。
    - 例子：`mkdir test && cd test` 先建文件夹再进去。

25. **nano** / **vim**  
    - 怎么用：`nano 文件` 或 `vim 文件`，编辑文件。
    - 例子：`nano config.txt` 打开编辑，保存是 Ctrl+O，退出 Ctrl+X。

---

### 文件系统的使用
26. **/**  
    - 怎么用：直接用在路径开头，表示根目录。
    - 例子：`cd /` 去根目录。

27. **~**  
    - 怎么用：代替主目录。
    - 例子：`cd ~` 回家。

28. **..**  
    - 怎么用：表示上一级目录。
    - 例子：`cd ..` 往上走一层。

29. **.*（隐藏文件）**  
    - 怎么用：用 `ls -a` 看，加名字操作。
    - 例子：`cat .bashrc` 查看隐藏的 `.bashrc` 文件。

---

### 实战小例子
假设你想装个工具（像 Miniconda），可能用到这些：
```bash
cd ~/Downloads        # 去下载文件夹
chmod +x install.sh   # 让安装脚本能跑
./install.sh          # 执行安装
source ~/.bashrc      # 刷新环境
```
或者删个文件：
```bash
rm -r old_folder      # 删除文件夹
```

### 小贴士
- **试着敲**：多敲几遍就熟了，错了也没事。
- **加 -h 或 --help**：不明白咋用，加个 `-h` 看看提示，比如 `cp --help`。
- **小心 `rm` 和 `sudo`**：别随便删或改系统东西。
