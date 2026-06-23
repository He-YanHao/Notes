# unittest

标准库中的模块unittest提供了代码测试工具介绍

## 测试案例

```python
import unittest
#包含unittest模块
from test import NumAdd
#导入函数
class NamesTestCase(unittest.TestCase):
#定义类
    def test_feature_a(self):
    #
        jieguo = NumAdd(2,5)
        #
        self.assertEqual(jieguo, 7)
		#
unittest.main()
```





`unittest` 框架要求所有测试用例必须是 `unittest.TestCase` 的子类，因为：

1. **测试发现机制**：
   - 框架通过查找 `unittest.TestCase` 的子类来自动发现测试
   - 类中所有以 `test_` 开头的方法会被识别为测试用例
2. **继承测试功能**：
   - 通过继承获得所有断言方法 (`assertEqual`, `assertTrue` 等)
   - 获得测试生命周期方法 (`setUp`, `tearDown`)





## `unittest.TestCase` 类详解

```python
import unittest

class MyTestCase(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        """类级别设置 - 在所有测试前执行一次"""
        cls.shared_resource = create_resource()
    
    def setUp(self):
        """测试级别设置 - 在每个测试方法前执行"""
        self.test_data = {"key": "value"}
    
    def test_feature_a(self):
        """测试方法1 - 必须以'test_'开头"""
        self.assertEqual(1 + 1, 2)
    
    def test_feature_b(self):
        """测试方法2"""
        self.assertIn("key", self.test_data)
    
    def tearDown(self):
        """测试级别清理 - 在每个测试方法后执行"""
        self.test_data.clear()
    
    @classmethod
    def tearDownClass(cls):
        """类级别清理 - 在所有测试后执行一次"""
        cls.shared_resource.close()
```

