# try

## 基础描述

```python
try:
    # 可能引发异常的代码
    risky_operation()
except SomeException:
    # SomeException为枚举类
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

   ```python
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







# Python `except` 块可捕获的错误类型

在 Python 中，`except` 块可以捕获多种类型的错误。所有的错误类型都是 `BaseException` 类的子类。以下是主要的错误类型分类：

## 1. 错误类型层次结构

```
BaseException
 ├── SystemExit
 ├── KeyboardInterrupt
 ├── GeneratorExit
 └── Exception
      ├── StopIteration
      ├── StopAsyncIteration
      ├── ArithmeticError
      │    ├── FloatingPointError
      │    ├── OverflowError
      │    └── ZeroDivisionError
      ├── AssertionError
      ├── AttributeError
      ├── BufferError
      ├── EOFError
      ├── ImportError
      │    └── ModuleNotFoundError
      ├── LookupError
      │    ├── IndexError
      │    └── KeyError
      ├── MemoryError
      ├── NameError
      │    └── UnboundLocalError
      ├── OSError
      │    ├── BlockingIOError
      │    ├── ChildProcessError
      │    ├── ConnectionError
      │    │    ├── BrokenPipeError
      │    │    ├── ConnectionAbortedError
      │    │    ├── ConnectionRefusedError
      │    │    └── ConnectionResetError
      │    ├── FileExistsError
      │    ├── FileNotFoundError
      │    ├── InterruptedError
      │    ├── IsADirectoryError
      │    ├── NotADirectoryError
      │    ├── PermissionError
      │    ├── ProcessLookupError
      │    └── TimeoutError
      ├── ReferenceError
      ├── RuntimeError
      │    ├── NotImplementedError
      │    └── RecursionError
      ├── SyntaxError
      │    └── IndentationError
      ├── SystemError
      ├── TypeError
      ├── ValueError
      │    └── UnicodeError
      │         ├── UnicodeDecodeError
      │         ├── UnicodeEncodeError
      │         └── UnicodeTranslateError
      └── Warning
```

## 2. 常见错误类型及示例

### 基本错误类型

```python
try:
    # 可能出错的代码
    result = 10 / 0
except ZeroDivisionError:
    print("不能除以零！")

try:
    numbers = [1, 2, 3]
    print(numbers[5])
except IndexError:
    print("索引超出范围！")

try:
    person = {"name": "Alice"}
    print(person["age"])
except KeyError:
    print("字典中不存在该键！")

try:
    import non_existent_module
except ImportError:
    print("导入模块失败！")
```

### 类型和值错误

```python
try:
    "hello" + 5  # 字符串和整数不能直接相加
except TypeError as e:
    print(f"类型错误: {e}")

try:
    int("abc")  # 无法将字符串转换为整数
except ValueError as e:
    print(f"值错误: {e}")

try:
    x = undefined_variable  # 未定义的变量
except NameError as e:
    print(f"名称错误: {e}")
```

### 文件操作错误

```python
try:
    with open("non_existent_file.txt", "r") as f:
        content = f.read()
except FileNotFoundError:
    print("文件不存在！")

try:
    with open("/root/protected_file.txt", "w") as f:
        f.write("test")
except PermissionError:
    print("没有文件操作权限！")

try:
    with open("directory", "r") as f:  # 尝试读取目录
        content = f.read()
except IsADirectoryError:
    print("这是一个目录，不是文件！")
```

### 系统相关错误

```python
try:
    import sys
    sys.exit()  # 系统退出
except SystemExit:
    print("程序被退出")

try:
    # 模拟内存不足
    huge_list = [0] * (10**10)
except MemoryError:
    print("内存不足！")

try:
    # 无限递归导致栈溢出
    def recursive_func():
        recursive_func()
    recursive_func()
except RecursionError:
    print("递归深度过大！")
```

## 3. 异常捕获的多种方式

### 捕获特定异常

```python
try:
    # 可能出错的代码
    value = int(input("请输入数字: "))
    result = 10 / value
except ValueError:
    print("输入的不是有效数字！")
except ZeroDivisionError:
    print("不能除以零！")
```

### 捕获多个异常

```python
try:
    # 可能出错的代码
    data = {"key": "value"}
    index = int(input("请输入索引: "))
    print(data["nonexistent"])
    result = 10 / index
except (ValueError, ZeroDivisionError, KeyError) as e:
    print(f"发生错误: {type(e).__name__}: {e}")
```

### 捕获所有异常（不推荐）

```python
try:
    # 可能出错的代码
    risky_operation()
except Exception as e:  # 捕获所有继承自 Exception 的异常
    print(f"发生错误: {type(e).__name__}: {e}")
    # 记录日志或进行其他处理
```

### 捕获所有异常包括系统退出（谨慎使用）

```python
try:
    # 可能出错的代码
    import sys
    sys.exit(1)
except BaseException as e:  # 捕获所有异常，包括 SystemExit, KeyboardInterrupt
    print(f"捕获到基础异常: {type(e).__name__}")
```

## 4. 完整的异常处理结构

```python
try:
    # 主要代码逻辑
    filename = input("请输入文件名: ")
    with open(filename, "r") as file:
        data = file.read()
        number = int(data.strip())
        result = 100 / number
        print(f"结果是: {result}")
        
except FileNotFoundError:
    print("错误: 文件不存在")
except PermissionError:
    print("错误: 没有读取文件的权限")
except ValueError:
    print("错误: 文件内容不是有效数字")
except ZeroDivisionError:
    print("错误: 文件中的数字不能为零")
except Exception as e:
    print(f"未预期的错误: {type(e).__name__}: {e}")
else:
    print("操作成功完成！")  # 没有异常时执行
finally:
    print("清理工作...")  # 无论是否异常都会执行
```

## 5. 自定义异常

你也可以创建自己的异常类型：

```python
class CustomError(Exception):
    """自定义异常类"""
    def __init__(self, message, code):
        super().__init__(message)
        self.code = code

try:
    raise CustomError("自定义错误消息", 1001)
except CustomError as e:
    print(f"自定义错误: {e}, 错误码: {e.code}")
```

## 6. 最佳实践

1. **具体化异常**：尽量捕获具体的异常类型
2. **避免裸except**：不要使用空的 `except:`，至少要使用 `except Exception:`
3. **记录异常信息**：使用 `as e` 获取异常对象
4. **合理使用finally**：用于资源清理
5. **不要静默处理**：除非有明确理由，否则不要忽略异常

```python
# 不好的做法
try:
    risky_operation()
except:
    pass  # 静默忽略所有异常

# 好的做法
try:
    risky_operation()
except SpecificError as e:
    logger.error(f"处理特定错误: {e}")
    # 适当的错误处理或重新抛出
except Exception as e:
    logger.error(f"未预期错误: {e}")
    raise  # 重新抛出
```

掌握这些异常类型和处理方式，可以帮助你编写更健壮、可维护的 Python 代码。