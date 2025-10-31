# new和delete

## new

使用`new`来申请内存。

```c++
// 分配单个对象
int* p = new int;       // 分配一个未初始化的 int
int* p2 = new int(42);  // 分配并初始化为 42

// 分配数组
int* arr = new int[10]; // 分配 10 个 int 的数组
```

会直接返回对应类型的指针，无需转换。



## delete

若要释放掉这部分内存需要`delete 内存名`