# Mermaid

Mermaid 是一个基于 JavaScript 的图表绘制工具，它通过一种**类似 Markdown 的文本语法**来生成图表和可视化内容。它的核心理念是：**让文档中的图表也能像代码一样被创建、维护和版本控制。**

可以无缝集成到各种支持 Markdown 的平台（如 GitHub、GitLab、Notion、VS Code、Typora 等）和文档工具中。

## 图表类型

### 流程图 graph TD

*   **流程图**：用于表示算法、工作流或过程。
    
    ```mermaid
    graph TD
        A[开始] --> B{条件判断}
        B -- 是 --> C[执行操作]
        B -- 否 --> D[结束]
        C --> D
    ```



### 序列图 sequenceDiagram

*   **序列图**：用于展示对象之间消息传递的时间顺序，常用于软件设计。
    
    ```mermaid
    sequenceDiagram
        participant A as 用户
        participant B as 系统
        A->>B: 登录请求
        B->>A: 登录成功
    ```

### 甘特图 gantt

*   **甘特图**：用于项目管理和规划，显示任务的时间安排和进度。
    ```mermaid
    gantt
        title 项目开发流程
        dateFormat YYYY-MM-DD
        section 设计
        需求调研 :a1, 2024-01-01, 7d
        原型设计 :after a1, 5d
        section 开发
        前端开发 :2024-01-10, 10d
        后端开发 :2024-01-12, 14d
    ```

### 类图 classDiagram

*   **类图**：用于面向对象编程中，表示类的结构、属性、方法以及类之间的关系。
    ```mermaid
    classDiagram
        class Animal {
            +String name
            +eat()
        }
        class Dog {
            +bark()
        }
        Animal <|-- Dog
    ```

### 状态图 stateDiagram-v2

*   **状态图**：用于描述一个对象在其生命周期内所经历的状态序列，以及如何响应各种事件。
    ```mermaid
    stateDiagram-v2
        [*] --> 待机
        待机 --> 运行 : 启动
        运行 --> 待机 : 停止
        运行 --> 报错 : 发生异常
        报错 --> [*]
    ```

### 饼图 pie title

*   **饼图**：用于表示比例构成。
    ```mermaid
    pie title 一天的时间分配
        "睡觉" : 8
        "工作" : 10
        "吃饭" : 2
        "娱乐" : 4
    ```
