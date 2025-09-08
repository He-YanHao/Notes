# HTTP请求体HTTP Request Body

## 基本介绍

HTTP 请求体是 HTTP 请求消息中的一个组成部分，用于承载客户端（如浏览器、App）发送给服务器的**实际数据**。

可以把一个完整的 HTTP 请求想象成一封信：

*   **请求行（Request Line）**：包含了收信人地址（URL）和你要做什么（HTTP 方法，如 GET、POST）。这就像是信封上的地址和“这是一封申请信”的备注。
*   **请求头（Request Headers）**：包含了关于这封信的元信息，比如用什么语言写的（`Content-Type`）、信有多长（`Content-Length`）、你是谁（`Authorization`）等。这就像是信封上的寄件人、邮编、邮票等信息。
*   **请求体（Request Body）**：就是信件的**正文内容本身**，是你要告诉服务器的具体信息。

**关键点：请求体通常只在需要向服务器发送数据时才存在，例如提交表单、上传文件、调用 API 传送 JSON 数据等。**



## 哪些请求有请求体？

不是所有的 HTTP 请求都有请求体。通常，**检索数据**的请求没有请求体，而**创建或修改数据**的请求有。

*   **通常没有请求体的方法**：
    *   `GET`：用于请求资源，所有参数都通过 URL 的查询字符串（如 `?key=value`）传递。
    *   `HEAD`：与 GET 类似，但只请求响应头，不获取响应体。
    *   `DELETE`：虽然有时也可能有，但通常通过 URL 标识要删除的资源。
    *   `OPTIONS`：用于询问服务器支持的通信选项。




*   **通常有请求体的方法**：
    *   `POST`：最常用的有请求体的方法，用于提交数据（如创建新订单、发表评论）。
    *   `PUT`：用于替换整个资源（如完整更新用户信息）。
    *   `PATCH`：用于对资源进行部分更新。
    *   `CONNECT`, `TRACE` 等（较少见）。



## 请求体的常见数据格式

请求头中的 `Content-Type` 字段**至关重要**，它告诉服务器请求体中的数据是什么格式，以便服务器能正确解析。常见的格式有：

### `application/x-www-form-urlencoded`
*   **描述**：这是 HTML 表单**默认**的提交格式。数据被编码成键值对（key-value pairs），类似于 URL 中的查询字符串。
*   **格式**：`key1=value1&key2=value2`，其中特殊字符和中文会被 URL 编码（如空格变成 `%20`）。
*   **示例**：
    ```http
    POST /login HTTP/1.1
    Host: example.com
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 29

    username=john_doe&password=123456
    ```



### `multipart/form-data`

*   **描述**：这也是 HTML 表单常用的格式，主要用于**上传文件**。它将请求体分成多个部分（parts），每个部分对应一个表单字段，可以包含二进制文件数据。
*   **格式**：每个部分由一个唯一的边界（boundary）字符串分隔，并可以有自己的头部（如 `Content-Type`）。
*   **示例**：
    ```http
    POST /upload HTTP/1.1
    Host: example.com
    Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW
    Content-Length: [length]

    ----WebKitFormBoundary7MA4YWxkTrZu0gW
    Content-Disposition: form-data; name="username"

    john_doe
    ----WebKitFormBoundary7MA4YWxkTrZu0gW
    Content-Disposition: form-data; name="avatar"; filename="photo.jpg"
    Content-Type: image/jpeg

    [二进制文件数据...]
    ----WebKitFormBoundary7MA4YWxkTrZu0gW--
    ```



### `application/json`

*   **描述**：**现代 Web 开发中最常用的格式**，尤其是在 RESTful API 中。数据以 JSON（JavaScript Object Notation）这种轻量级、易于人阅读和机器解析的格式发送。
*   **格式**：一个标准的 JSON 对象或数组。
*   **示例**：
    ```http
    POST /api/users HTTP/1.1
    Host: example.com
    Content-Type: application/json
    Content-Length: 56

    {
      "name": "John Doe",
      "email": "john@example.com",
      "age": 30
    }
    ```



### `text/plain`, `application/xml`, `image/png` 等

*   **描述**：也可以发送纯文本、XML 或甚至直接发送一个完整的图片或文档字节流。`Content-Type` 字段相应地进行设置即可。



## 如何查看请求体？

作为开发者，我们经常需要查看请求体来调试程序。

1.  **浏览器开发者工具**：
    
    *   打开 Chrome 或 Firefox 的“开发者工具”（F12）。
    *   切换到 **“Network”（网络）** 标签页。
    *   在网页上执行一个操作（如点击提交按钮）。
    *   在网络请求列表中点击该请求。
*   选择 **“Payload”（负载）** 或 **“Request”** 标签页，即可看到 **“Request Body”**。
    
2.  **命令行工具（如 cURL）**：
    *   你可以用 cURL 命令直接构造带请求体的请求：
    ```bash
    # 发送 JSON 数据
    curl -X POST -H "Content-Type: application/json" -d '{"key":"value"}' https://api.example.com/endpoint

    # 发送表单数据
    curl -X POST -d "username=john&password=123" https://example.com/login
    ```



## 总结

| 特性           | 描述                                                         |
| :------------- | :----------------------------------------------------------- |
| **作用**       | 承载客户端发送给服务器的实际数据。                           |
| **位置**       | HTTP 请求消息的最后一部分，在头部之后。                      |
| **存在条件**   | 通常在 `POST`, `PUT`, `PATCH` 等请求中存在。                 |
| **关键头字段** | `Content-Type`：定义请求体的格式（如 `application/json`）。<br>`Content-Length`：定义请求体的字节大小。 |
| **常见格式**   | `application/x-www-form-urlencoded`（表单）、`multipart/form-data`（文件上传）、`application/json`（API 数据交换）。 |

