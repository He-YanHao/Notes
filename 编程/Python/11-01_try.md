# try

## 基础描述

```python
try:
    # 可能引发异常的代码
    risky_operation()
except SomeException:
    # 处理特定类型的异常
    handle_error()
else:
    # 没有异常时执行的代码
    safe_operation()
finally:
    # 无论是否异常都会执行的代码（清理资源）
    cleanup()
```

### 关键组件解析

1. **try 块**

   - 包含可能出错的代码
   - 当发生异常时，立即跳转到对应的 `except` 块

2. **except 块**

   - 捕获并处理特定异常

   - 可指定多种异常类型：

     python

     ```python
     except (ValueError, TypeError) as e:
         print(f"输入错误: {e}")
     ```

   - 使用 `as e` 可获取异常对象

3. **else 块**（可选）

   - 仅当 `try` 块**未发生异常**时执行
   - 适合放置依赖 `try` 块成功执行的代码

4. **finally 块**（可选）

   - **无论是否发生异常**都会执行
   - 常用于资源清理（如关闭文件、释放锁）

   python

   ```python
   file = open("data.txt")
   try:
       process(file)
   finally:
       file.close()  # 确保文件一定被关闭
   ```

### 使用场景示例

1. **处理除零错误**

   python

   ```
   try:
       result = 10 / int(input("输入除数: "))
   except ZeroDivisionError:
       print("错误：除数不能为零")
   except ValueError:
       print("错误：请输入有效数字")
   else:
       print(f"结果是: {result}")
   ```

2. **文件操作安全处理**

   python

   ```
   try:
       with open("data.txt") as f:
           content = f.read()
   except FileNotFoundError:
       print("文件不存在")
   except PermissionError:
       print("没有文件访问权限")
   ```

3. **API 调用异常处理**

   python

   ```
   import requests
   
   try:
       response = requests.get("https://api.example.com/data", timeout=5)
       response.raise_for_status()  # 检查HTTP错误
   except requests.Timeout:
       print("请求超时")
   except requests.HTTPError as err:
       print(f"HTTP错误: {err}")
   except Exception as e:  # 捕获所有其他异常
       print(f"未知错误: {e}")
   ```

### 异常处理最佳实践

1. **精确捕获异常**

   python

   ```
   # 避免笼统捕获
   try:
       # ...
   except Exception:  # 不推荐（太宽泛）
       pass
   
   # 推荐做法
   try:
       # ...
   except (SpecificError1, SpecificError2) as e:
       logger.error(f"业务异常: {e}")
   ```

2. **保持 try 块精简**

   python

   ```
   # 不推荐（包含过多安全代码）
   try:
       a = get_data()
       b = process(a)
       save(b)
   
   # 推荐（聚焦真正危险的操作）
   try:
       b = process(a)  # 只有可能出错的操作放这里
   except ProcessError:
       handle_error()
   else:
       save(b)        # 安全操作放else块
   ```

3. **异常处理不是流程控制**

   - 避免用异常处理代替条件判断：

     python

     ```
     # 错误用法（异常应处理意外情况）
     try:
         value = my_dict[key]
     except KeyError:
         value = default
     
     # 正确用法（用get方法处理默认值）
     value = my_dict.get(key, default)
     ```

### 异常类型体系

Python 内置异常继承关系（部分）：

text

```
BaseException
 ├── SystemExit
 ├── KeyboardInterrupt
 └── Exception
      ├── ValueError
      ├── TypeError
      ├── FileNotFoundError
      └── RuntimeError
```

- 可通过 `except Exception` 捕获所有非系统退出异常
- 自定义异常应继承 `Exception` 类

### 总结

`try` 关键字是 Python 异常处理的核心，它：

- 保护程序免受意外错误影响
- 分离正常逻辑和错误处理代码
- 确保资源被正确释放（通过 finally）
- 提高代码健壮性和可维护性

合理使用异常处理能让你的代码更专业、更可靠，特别是在处理外部资源（文件/网络/用户输入）时至关重要。