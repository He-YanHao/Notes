# SSH

每个 SSH Key 由两部分组成：

- **私钥 (Private Key)**：保存在本地计算机上，**绝对不能分享**
- **公钥 (Public Key)**：可安全分享给服务端（GitHub、GitLab、服务器等）

创建SSH密钥

```
ssh-keygen -t ed25519 -C "your_email@example.com"
```

其中`t ed25519`是加密算法。

`-C "your_email@example.com"`是注释。



```markdown
# 1. 生成密钥（默认保存到 ~/.ssh/）
ssh-keygen -t ed25519 -C "your_email@example.com"

# 2. 设置保存路径（按Enter使用默认）
> Enter file in which to save the key (/home/you/.ssh/id_ed25519): 

# 3. 设置密码（可选但推荐）
> Enter passphrase (empty for no passphrase): [输入密码]
> Enter same passphrase again: [再次输入]

# 4. 生成成功提示
Your identification has been saved in /home/you/.ssh/id_ed25519
Your public key has been saved in /home/you/.ssh/id_ed25519.pub
```



查看公钥

```
cat ~/.ssh/id_ed25519.pub
```



