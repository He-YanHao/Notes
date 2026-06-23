# 资源定位UR家族

## 统一资源标识符URI

统一资源标识符



## 统一资源定位符URL

**URL 不仅标识了资源是什么，还提供了如何定位（找到）这个资源的方法。** 它包含了访问该资源所需的全部信息，例如：

- **协议**（`http://`, `https://`, `ftp://`）
- **位置**（域名或IP地址，如 `www.example.com`）
- **路径**（资源在服务器上的具体位置，如 `/images/cat.jpg`）
- 可能还包括端口号、参数等。

### URL基本格式



```http
http://user:password@www.glasscom.com:80/dir/file1.htm
协议   用户    密码      Web服务器名   端口号  文件路径
```



```http
ftp://user:password@ftp.glasscom.com:21/dir/file1.htm
协议   用户    密码     FTP服务器域名   端口号  文件路径
```



```http
file://localhost/c:/path/file1.zip
       计算机名      文件路径
```





```http
mailto:tone@glasscom.com
            邮箱地址
```





```http
news:comp.protocols.tcp-ip
          新闻组名
```







## 统一资源名称URN



**命名**一个资源（给它一个唯一名字）





## 总结

URL和URN都是URI的一种。

