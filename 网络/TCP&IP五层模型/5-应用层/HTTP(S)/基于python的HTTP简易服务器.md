# 基于python的HTTP简易服务器

Python提供了一个非常简单的方式来搭建一个HTTP服务器，特别是通过`http.server`模块。



## 基本步骤

使用Python 3.x的`http.server`模块

Python自带的`http.server`模块可以快速创建一个HTTP服务器，用来处理基本的文件服务。



## 启动一个HTTP服务器

在你想要作为服务器根目录的文件夹下，打开命令行并运行以下命令：

```bash
python3 -m http.server 8000
```

这会启动一个HTTP服务器，监听8000端口。

可以通过浏览器访问 `http://localhost:8000` 来查看当前目录中的文件。



## 自定义端口

你可以指定不同的端口。例如，如果你想使用端口 `8080`，运行：

```bash
python3 -m http.server 8080
```



## 自定义请求处理

如果你需要对请求进行自定义处理（例如，处理特定的请求或修改响应），可以通过继承`http.server.BaseHTTPRequestHandler`类来实现。以下是一个简单的例子：

```python
from http.server import BaseHTTPRequestHandler, HTTPServer

class MyHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.send_header('Content-type', 'text/html')
        self.end_headers()
        self.wfile.write(b"Hello, this is a custom server response!")

def run(server_class=HTTPServer, handler_class=MyHandler, port=8000):
    server_address = ('', port)
    httpd = server_class(server_address, handler_class)
    print(f'Starting server on port {port}...')
    httpd.serve_forever()

if __name__ == '__main__':
    run()
```

这个例子中，我们自定义了一个`do_GET`方法来处理GET请求，并返回一个简单的HTML响应。



## 配置静态文件目录

如果你想为服务器提供静态文件（例如HTML、CSS、JS文件），你可以将其存储在当前工作目录下。服务器会自动提供该目录中的文件。

例如，将`index.html`放到当前目录下，你就可以通过 `http://localhost:8000/index.html` 访问它。



## 总结

通过Python的内建模块`http.server`，你可以快速搭建一个简易的HTTP服务器来进行开发、测试或学习。它非常适合快速原型开发和本地测试。如果你需要更复杂的功能，比如处理POST请求或路由，你可以通过扩展`BaseHTTPRequestHandler`来实现。