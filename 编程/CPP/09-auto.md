# auto

## 基本

可以自动推导数据类型

## 尾置返回类型

```cpp
auto getFirstElement(const Container& cont) -> decltype(*cont.begin())
{
    return *cont.begin();
}
```

在这个里面

- `auto`代表返回值待定，通过`->`引入返回类型声明。

- `decltype(expression)`：推导表达式的类型
- `*cont.begin()`：关键表达式
  - `cont.begin()`：获取容器的起始迭代器
  - `*`：解引用迭代器（获取迭代器指向的元素）