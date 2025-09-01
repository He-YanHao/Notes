# Linux用户管理

## 用户

Linux是一个多用户系统。其中涉及很多关于用户的地方。

每个用户可以有自己的家目录 ` /home ` ，多个用户可以属于一个用户组，以便进行统一管理。

如果想知道自己的用户身份，可以在终端输入 ` id ` 查询。

用户在被创建时会被分配用户ID ` uid ` 和属组ID ` gid ` ，其中 root 用户ID为0，其他非系统虚拟用户的ID根据发行版的不同从 500 或 1000 开始。

所以的用户信息被记录在 ` /etc/passwd ` 里面，其中包含大量的系统虚拟用户。

### /etc/passwd文件分析

```shell
# root用户 固定为0
root:x:0:0:root:/root:/bin/bash

# 各种进程虚拟出的用户 以最小权限运行
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
...
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
...
nvidia-persistenced:x:122:124:NVIDIA Persistence Daemon,,,:/nonexistent:/usr/sbin/nologin
# 普通用户 从1000开始
hing:x:1000:1000:he:/home/hing:/usr/bin/fish
```



**普通用户配置分析**

```
hing  : x  : 1000 : 1000    : he : /home/hing : /usr/bin/fish
用户名  密码  用户ID  用户组ID 用户名  家目录        用户运行终端的程序
```

- 密码x表述被加密过，现在一般将密码存放在shadow文件中。shadow文件的格式和passwd类似，不过普通用
    户没有访问权限。 

### ./shadow文件分析

文件内容：

```
hing:$6$FDjTBRmXck9aDYAk$ArSI7YlBe4I047DSiD12v/uLS/uMb/M/4jKDT76CzLI9rkMwK0g9YiYWtmO4/9XLnRNWpHb317whD1CWXwdVj/:20326:0:99999:7:::
```

- 用户名：hing

- 加密算法：6

    表示使用的是 **SHA-512** 加密哈希算法。

- 加密后的密码：

    - **盐值 (Salt)**：`FDjTBRmXck9aDYAk`

        随机的字符串，防止密码重复。

    - 密码：`ArSI7YlBe4I047DSiD12v/uLS/uMb/M/4jKDT76CzLI9rkMwK0g9YiYWtmO4/9XLnRNWpHb317whD1CWXwdVj`

        最终的密码哈希值是将“明文密码 + 盐值”通过**SHA-512**算法计算后得到的一长串固定长度的字符串。

- 最后一次修改密码的天数：20326

    这表示最后一次修改密码的时间是自1970年1月1日（Unix纪元）以来的第20326天。

- 最小密码年龄：0

    设置密码后最少要多久才可以再次设置密码。

- 最大密码年龄：99999

    密码最多可以用多少天。

- 警告期：7

    在密码到期多少天之前可以收到提示。

- 密码不活跃期：空白

- 账户过期日期：空白

- 保留字段：空白



## 单个用户管理

### 添加用户

以创建一个叫 myuser 的用户为例：

```shell
useradd -m -G wheel -s /bin/bash myuser
```

- -m：选项会自动为新手创建家目录（通常在 /home/myuser）。

- -G：wheel 将用户添加到 wheel 组（通常用于获取 sudo 权限）。

- -s：/bin/bash 设置用户的默认 shell 为 bash。



### 删除用户



```shell
```







```shell
# 删除用户
userdel

# 修改用户
usermod

# 密码
passwd
```



## 用户组管理

```shell
# 添加用户
groupadd

# 删除用户
groupdel

# 修改用户
groupmod

# 移动用户组
newgrp
```



