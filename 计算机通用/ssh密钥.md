# ssh密钥

## 公钥与私钥

每个 SSH Key 由两部分组成：

**私钥 (Private Key)**：保存在本地计算机上，**绝对不能分享**

```shell
hing@he-books ~> cat ./.ssh/id_ed25519
-----BEGIN OPENSSH PRIVATE KEY-----
488个字符 每90个为一行 余38个字符
等效为256位密钥长度
-----END OPENSSH PRIVATE KEY-----
```

**公钥 (Public Key)**：可安全分享给服务端（GitHub、GitLab、服务器等）

```shell
hing@he-books ~> cat ./.ssh/id_ed25519.pub
ssh-ed25519 ????????????????? ???@163.com
# 加密算法    88位公钥主体       注释
```

## 涉及重要的文件和目录

**本地客户端（你的电脑）**:

*   `~/.ssh/`：SSH 配置和密钥的默认存储目录。
*   `~/.ssh/id_rsa` 或 `~/.ssh/id_ed25519`：默认的 SSH **私钥**文件。**此文件权限必须非常严格（通常是 600），绝不能泄露给他人。**
*   `~/.ssh/id_rsa.pub` 或 `~/.ssh/id_ed25519.pub`：与私钥对应的**公钥**文件。这个文件的内容就是你需要添加到服务器上的。
*   `~/.ssh/known_hosts`：存储你曾经连接过的所有服务器的公钥指纹，用于验证服务器的真实性，防止“中间人攻击”。
*   `~/.ssh/config`：客户端的配置文件，可以为你经常访问的服务器设置别名、默认用户名、端口等，极大简化命令。

**远程服务器**:

*   `/etc/ssh/sshd_config`：SSH **服务器**的配置文件。修改此文件后需要重启 `sshd` 服务才能生效。
*   `~/.ssh/authorized_keys`：存储所有被允许通过公钥认证登录到此用户的公钥列表。



## 创建密钥

```shell
ssh-keygen -t ed25519 -C "your_email@example.com"
```

- -t：指定加密算法，这里为 ed25519。
- -C：添加注释，一般设置为邮箱地址。

```shell
hing@he-books ~> ssh-keygen -t ed25519 -C "测试"
# 创建SSH密钥 使用ed25519加密算法

Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/hing/.ssh/id_ed25519): 
# 指定ssh密钥文件的保存路径和名称 不指定默认为~/.ssh加密算法名
Created directory '/home/hing/.ssh'.
Enter passphrase (empty for no passphrase): 
# 输入一个口令 假设有口令 使用时需要验证私钥和口令
Enter same passphrase again: 
# 再次输入口令
Passphrases do not match.  Try again.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/hing/.ssh/id_ed25519
# 私钥路径
Your public key has been saved in /home/hing/.ssh/id_ed25519.pub
# 公钥路径
The key fingerprint is:
SHA256:23zRBIEpYm5oNrjZ+5zun80ZmDRxsy+MDQFFpKHwsBQ 测试
The key's randomart image is:
+--[ED25519 256]--+
|  E.  o++  oo.   |
| . = .o+. o  .   |
|  ..o+..o.o   .  |
|  . = o  + o o   |
|   * o  S . . .  |
|  o .  . % . .   |
|     .  = B o    |
|    .. . + =     |
|     +*.o +      |
+----[SHA256]-----+
```

## 查看密钥内容

```shell
cat ~/.ssh/id_ed25519.pub
# 查看公钥
cat ~/.ssh/id_ed25519
# 查看私钥
```

## 查看密钥信息

```shell
ssh-keygen -l -f ~/.ssh/id_ed25519
256 SHA256:公钥指纹 注释 (ED25519)
```





