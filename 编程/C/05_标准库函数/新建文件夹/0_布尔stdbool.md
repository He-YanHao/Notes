在 C99 标准之前，C 语言中通常使用整数类型（如 `int`）来表示布尔值。例如，`0` 表示假，非零值（通常是 `1`）表示真。这种方式虽然可行，但缺乏直观性和类型安全性。为了解决这个问题，C99 标准引入了 `stdbool.h` 头文件，定义了布尔类型和相关宏。

`<stdbool.h>` 是 C 语言中的一个标准头文件，定义了布尔类型及其相关的常量。它使得 C 语言的布尔类型（`bool`）变得更加明确和可用，避免了使用整数（如 0 或 1）来表示布尔值的传统做法。

`stdbool.h` 头文件定义了以下内容：

-   `bool`：布尔类型，用于声明布尔变量。
-   `true`：表示真值的宏，通常定义为 `1`。
-   `false`：表示假值的宏，通常定义为 `0`。
-   `__bool_true_false_are_defined`：一个宏，用于指示 `true` 和 `false` 是否已定义。

这些宏的定义如下：

```
#define bool _Bool
#define true 1
#define false 0
#define __bool_true_false_are_defined 1
```

bool 是 _Bool 类型的别名。

_Bool 是 C99 标准中引入的原生类型，表示一个布尔值。_Bool 类型只能保存 0 或 1，因此适合用来存储逻辑值。

```
#include <stdbool.h>

_Bool is_valid = 1;  // 也可以直接使用 _Bool 类型
```

要在 C 程序中使用布尔类型和相关宏，首先需要引入 `<stdbool.h>` 头文件：

```
#include <stdbool.h>

bool is_raining = true;
bool is_sunny = false;
```