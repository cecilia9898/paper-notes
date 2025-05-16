```bash
#!/bin/bash

# ssh-debug.sh - A simple SSH debugging script

echo "=== Starting SSH Key Debugging ==="

# Step 1: Clear all SSH keys from the agent
ssh-add -D
echo "Cleared all cached SSH keys."

# Step 2: Load the private key
if [ -f ~/.ssh/id_ed25519 ]; then
    ssh-add ~/.ssh/id_ed25519
    echo "Private key id_ed25519 loaded."
else
    echo "Private key id_ed25519 not found. Exiting."
    exit 1
fi

# Step 3: Verify fingerprint consistency
echo "Verifying fingerprints..."
PRIV_FP=$(ssh-keygen -lf ~/.ssh/id_ed25519 | awk '{print $2}')
PUB_FP=$(ssh-keygen -lf ~/.ssh/id_ed25519.pub | awk '{print $2}')

if [ "$PRIV_FP" == "$PUB_FP" ]; then
    echo "Fingerprints match."
else
    echo "Fingerprints do NOT match. Exiting."
    exit 1
fi

# Step 4: Refresh GitHub known_hosts
ssh-keyscan github.com >> ~/.ssh/known_hosts
echo "GitHub known_hosts updated."

# Step 5: Test SSH connection to GitHub
ssh -T git@github.com

# Step 6: Check if Git remote is SSH, not HTTPS
REMOTE_URL=$(git remote get-url mldata 2>/dev/null)

if [[ $REMOTE_URL == *"https://"* ]]; then
    echo "Found HTTPS Git Remote. Switching to SSH."
    git remote set-url mldata git@github.com:YuLabProteomics/01_mldata.git
    echo "Git Remote switched to SSH."
else
    echo "Git Remote is already SSH."
fi

# Step 7: Display final Git remote info
git remote -v

echo "=== SSH Debugging Complete ==="
```

---

### **使用步骤**

1. **保存脚本**
   把上面的内容保存到 `~/ssh-debug.sh`

   ```bash
   nano ~/ssh-debug.sh
   ```

2. **赋予执行权限**

   ```bash
   chmod +x ~/ssh-debug.sh
   ```

3. **执行脚本**

   ```bash
   bash ~/ssh-debug.sh
   ```

---

### **脚本功能**

1. 清空缓存的 SSH Key
2. 重新加载 `id_ed25519`
3. 验证私钥和公钥的 fingerprint 是否匹配
4. 刷新 GitHub 的 `known_hosts`
5. 测试和 GitHub 的连接
6. 如果 Git Remote 是 HTTPS，会自动切换成 SSH
7. 打印最终的 Git Remote 配置

---

### **下次遇到 Permission Denied 的时候**

运行：

   ```bash
   bash ~/ssh-debug.sh
   ```

