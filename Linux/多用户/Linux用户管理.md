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

### 添加用户useradd

以创建一个叫 myuser 的用户为例：

```shell
useradd -m -G wheel -s /bin/bash myuser
```

- `-m`：选项会自动为新手创建家目录（通常在 /home/myuser）。

- `-g`：

- `-G`：wheel 将用户添加到 wheel 组（通常用于获取 sudo 权限）。

- `-s`：指定用户的默认shell目录为/bin/bash。



### 删除用户userdel



```shell
sudo userdel user
# 删除用户user，保留家目录。
sudo userdel -r user
# 删除用户user，不保留家目录。
```





### 修改用户usermod

```shell
sudo userdel user
# 删除用户user，保留家目录。
sudo userdel -r user
# 删除用户user，不保留家目录。
```

**参数：**

#### 修改组

-   `-g`

    ```shell
    # -g：更改用户的主要组（Primary Group）。
    sudo usermod -g developers hing
    # 将用户 hing 的主要组改为 developers。
    ```

-   **`-G`**：更改用户的附加组（Supplementary Groups）。用户可以有多个附加组，从而获得这些组的权限。

    *   `sudo usermod -G wheel,audio,video hing`
    *   将用户 `hing` 加入到 `wheel`（常用于sudo权限）、`audio`（声音）、`video`（视频）组中。
    *   **重要**：使用 `-G` 时，它会**覆盖**用户当前所有的附加组。如果你只是想**添加**一个附加组而不影响其他组，必须结合 `-a`（append）选项。

-   **`-aG`**：**（最常用组合）** 将一个用户追加到一个或多个附加组中，而不移除其原有的其他附加组。
    *   `sudo usermod -aG docker hing`
    *   将用户 `hing` 添加到 `docker` 组，同时保留他之前的所有其他附加组。**忘记使用 `-a` 选项是一个常见的错误，会导致用户失去原有的组权限。**

#### 修改用户家目录

*   **`-d`**：更改用户的家目录路径。
*   **`-m`**：通常与 `-d` 一起使用，它将旧家目录的内容**移动**到新目录。
    *   `sudo usermod -d /new/home/hing -m hing`
    *   将用户 `hing` 的家目录从默认的 `/home/hing` 改为 `/new/home/hing`，并且把原目录下的所有文件（如 `.bashrc`, `.ssh` 等）都移动过去。

#### 修改用户名

*   **`-l`**：更改用户的登录名（Username）。
    *   `sudo usermod -l newhing oldhing`
    *   将用户 `oldhing` 的名字改为 `newhing`。**注意：** 这不会自动更改用户的家目录名，你需要手动或用 `-d` 和 `-m` 选项来同步修改。

#### 修改用户UID

*   **`-u`**：更改用户的 UID（User ID）。
    *   `sudo usermod -u 1050 hing`
    *   将用户 `hing` 的 UID 改为 `1050`。
    *   **注意**：更改后，你需要手动更改用户原有文件的所有者身份，否则这些文件仍属于旧的 UID。可以使用 `chown -R` 命令递归修改。

#### 修改用户的登录Shell

*   **`-s`**：更改用户默认的登录shell。
    *   `sudo usermod -s /bin/zsh hing`
    *   将用户 `hing` 的登录shell从默认的bash改为zsh。
    *   `sudo usermod -s /usr/sbin/nologin username`
    *   禁止某个用户登录系统（常用于系统服务账户）。

#### 修改用户注释信息

*   **`-c`**：修改用户的备注信息，通常是全名。
    *   `sudo usermod -c "Zhang San" zhangsan`
    *   将用户 `zhangsan` 的备注名改为 "Zhang San"。

#### 账户锁定与解锁

*   **`-L`**：锁定用户账户。
    *   `sudo usermod -L username`
    *   在用户密码前添加一个 `!`，使其密码失效，无法登录。这是最常用的锁定方式。
*   **`-U`**：解锁用户账户。
    *   `sudo usermod -U username`
    *   移除密码前的 `!`，使用户可以正常用密码登录。



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



