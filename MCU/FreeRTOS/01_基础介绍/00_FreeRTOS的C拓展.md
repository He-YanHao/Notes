# FreeRTOS的C拓展

## C语言链表函数

**单链表：**节点本身必须包含一个节点指针，用于指向后一个节点，除了这个节点指针是必须有的之外，节点本身还可以携带一些私有信息。

链表结构体：

```
struct node {
    struct node *next;        //指向链表的下一个节点，下一个链表的指针
    char data1;               //单个的数据，
    unsigned char array[];    //数组，
    unsigned long *prt;       //指针数据，
    struct userstruct data2;  //自定义结构体类型数据，
}
```

一般会内嵌在一个数据结构中，采用下面这种写法：

```
/*节点定义*/
struct node
{
    struct node *next;            //指向链表的下一个节点
}
//
struct userstruct
{
    //在结构体中，内嵌一个结点指针，通过这个节点将数据挂载在链表
    struct node *next;
    //各式各样的要储存的数据
}
```

k

```c
struct xLIST_ITEM
{
TickType_t xItemValue;                 /* 辅助值，用于帮助节点做顺序排列 */
struct xLIST_ITEM * pxNext;            /* 指向链表下一个节点 */
struct xLIST_ITEM * pxPrevious;        /* 指向链表前一个节点 */
void * pvOwner;                        /* 指向拥有该节点的内核对象，通常是 TCB */
void * pvContainer;                    /* 指向该节点所在的链表 */
};
typedef struct xLIST_ITEM ListItem_t;  /* 节点数据类型重定义 */
```

## const

```c
const char * const pcName = 'A';
```

- 第一个const表示指针指向的地方为只读，不可修改。
- char为指针的类型。
- 第二个const表示指针指向的地方存放的变量为只读，不可修改。
- pcName为指向只读变量的只读指针名。

## 函数指针

**声明函数指针的标准格式：**

```c
// 1. 声明函数指针类型
typedef 返回类型 (*指针类型名称)(参数列表);

// 2. 声明函数指针变量
返回类型 (*指针变量名)(参数列表) = 函数地址;
```

**无参数无返回值：**

```c
typedef void (*SimpleFuncPtr)(void);

void sayHello() {
    printf("Hello!\n");
}

int main() {
    SimpleFuncPtr func = sayHello;  // 赋值
    func();                         // 调用：输出 "Hello!"
    return 0;
}
```

**带参数有返回值的函数指针的用法案例：**


```c
typedef int (*MathFuncPtr)(int, int);
//表示函数指针类型名称为MathFuncPtr，参数为两个int类型，返回值也为int类型。
int add(int a, int b) { return a + b; }//申请加函数
int sub(int a, int b) { return a - b; }//申请减函数
//函数
int main() {
    MathFuncPtr operations[] = {add, sub};  // 函数指针数组
    //使用MathFuncPtr申请了operations[0]和operations[1]，并将operations[0]指向add，operations[1]指向sub。
    printf("3+5=%d\n", operations[0](3,5)); // 输出 8
    printf("7-2=%d\n", operations[1](7,2)); // 输出 5
    接下来可以使用operations[0]代替add()，operations[1]代替sub()。
    return 0;
}
```

**复杂用法案例：**


```c
typedef void (*FileCallback)(FILE*, const char*);
//表示函数指针类型名称为FileCallback，参数为FILE*和const char*，无返回值。
void writeData(FILE* fp, const char* text) {
    fprintf(fp, "%s\n", text);
}

void readData(FILE* fp, const char* path) {
    char buffer[256];
    while(fgets(buffer, sizeof(buffer), fp) {
        printf("Read: %s", buffer);
    }
}

int main() {
    FileCallback fileOps[] = {writeData, readData};
    
    FILE* fp = fopen("data.txt", "w");
    fileOps[0](fp, "Function pointer demo");  // 写入文件
    fclose(fp);
    
    fp = fopen("data.txt", "r");
    fileOps[1](fp, NULL);  // 读取文件
    fclose(fp);
    return 0;
}
```



