# 垂直布局QVBoxLayout

## 基本介绍

- 控件按 **垂直方向（从上到下）** 依次排列。
- 适用于需要纵向排列的场景，如登录窗口、设置面板等。

## 成员函数

```cpp
QVBoxLayout *layout = new QVBoxLayout(centralWidget);//垂直布局
```



**添加弹性空间**

```cpp
layout->addStretch(1);
```

参数为弹性因子，

**添加固定大小空白**（单位：像素）

```cpp
layout->addSpacing(100);
```



### **示例代码**

```cpp
#include <QVBoxLayout>
#include <QPushButton>
#include <QLineEdit>

// 创建垂直布局
QVBoxLayout *layout = new QVBoxLayout;

// 添加控件（按顺序从上到下排列）
layout->addWidget(new QLabel("用户名:"));
layout->addWidget(new QLineEdit);
layout->addWidget(new QLabel("密码:"));
layout->addWidget(new QLineEdit);
layout->addWidget(new QPushButton("登录"));

// 设置边距和间距
layout->setContentsMargins(10, 10, 10, 10); // 左、上、右、下边距
layout->setSpacing(5); // 控件之间的间距
```

---

## 