# LaTeX

在 Markdown 文档中编写数学公式主要通过 **LaTeX** 语法实现。下面我将为你详细介绍常用的写法，从基础到高级，并附上示例。

## 核心概念：两种公式环境

1.  **行内公式 (Inline)**：公式嵌入在文本行中。
    *   **语法**：使用一个美元符号 `$ ... $` 包裹。
    *   **示例**：
        
        ```markdown
        勾股定理的公式为 $a^2 + b^2 = c^2$，这是一个著名的数学定理。
        ```
*   **渲染效果**：勾股定理的公式为 $a^2 + b^2 = c^2$，这是一个著名的数学定理。
    
2.  **块公式 (Block) / 显示公式 (Display)**：公式独立成行，居中显示。
    *   **语法**：使用两个美元符号 `$$ ... $$` 包裹（或 `\[ ... \]`）。
    *   **示例**：
        ```markdown
        二次方程的求根公式如下：
        $$
        x = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}
        $$
        它有两个解。
        ```
    *   **渲染效果**：
        二次方程的求根公式如下：
        $$
        x = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}
        $$
        它有两个解。

---

## 常用语法与符号

### 上标和下标
*   **上标 (Superscript)**：使用 `^`
    *   `$x^2$` → $x^2$
    *   `$e^{x+y}$` → $e^{x+y}$ (注意用花括号包裹复杂内容)
*   **下标 (Subscript)**：使用 `_`
    *   `$a_n$` → $a_n$
    *   `$x_{n+1}$` → $x_{n+1}$

### 分式
*   **语法**：`\frac{分子}{分母}`
*   **示例**：
    *   `$\frac{1}{2}$` → $\frac{1}{2}$ (行内)
    *   `$$\frac{1}{2}$$` → $$\frac{1}{2}$$ (块级)

### 根式
*   **平方根**：`\sqrt{...}`
    *   `$\sqrt{2}$` → $\sqrt{2}$
    *   `$\sqrt[3]{8}$` → $\sqrt[3]{8}$ (n次根号)

### 希腊字母
*   直接使用反斜杠加字母的英文名。
*   **小写**：
    *   `$\alpha, \beta, \gamma, \pi, \omega$` → $\alpha, \beta, \gamma, \pi, \omega$
*   **大写**：
    *   `$\Gamma, \Pi, \Omega, \Delta$` → $\Gamma, \Pi, \Omega, \Delta$

#### 希腊字母表

| 大写 | LaTeX 代码 | 小写 | LaTeX 代码 | 英文名称 | 中文名称 |
| :--- | :--------- | :--- | :--------- | :------- | :------- |
| A    | `A`        | α    | `\alpha`   | Alpha    | 阿尔法   |
| B    | `B`        | β    | `\beta`    | Beta     | 贝塔     |
| Γ    | `\Gamma`   | γ    | `\gamma`   | Gamma    | 伽玛     |
| Δ    | `\Delta`   | δ    | `\delta`   | Delta    | 德尔塔   |
| E    | `E`        | ε    | `\epsilon` | Epsilon  | 伊普西隆 |
| Z    | `Z`        | ζ    | `\zeta`    | Zeta     | 泽塔     |
| H    | `H`        | η    | `\eta`     | Eta      | 伊塔     |
| Θ    | `\Theta`   | θ    | `\theta`   | Theta    | 西塔     |
| I    | `I`        | ι    | `\iota`    | Iota     | 约塔     |
| K    | `K`        | κ    | `\kappa`   | Kappa    | 卡帕     |
| Λ    | `\Lambda`  | λ    | `\lambda`  | Lambda   | 拉姆达   |
| M    | `M`        | μ    | `\mu`      | Mu       | 缪       |
| N    | `N`        | ν    | `\nu`      | Nu       | 纽       |
| Ξ    | `\Xi`      | ξ    | `\xi`      | Xi       | 克西     |
| O    | `O`        | ο    | `o`        | Omicron  | 奥米克戎 |
| Π    | `\Pi`      | π    | `\pi`      | Pi       | 派       |
| P    | `P`        | ρ    | `\rho`     | Rho      | 柔       |
| Σ    | `\Sigma`   | σ    | `\sigma`   | Sigma    | 西格玛   |
| T    | `T`        | τ    | `\tau`     | Tau      | 陶       |
| Υ    | `\Upsilon` | υ    | `\upsilon` | Upsilon  | 宇普西隆 |
| Φ    | `\Phi`     | φ    | `\phi`     | Phi      | 斐       |
| X    | `X`        | χ    | `\chi`     | Chi      | 希       |
| Ψ    | `\Psi`     | ψ    | `\psi`     | Psy      | 普西     |
| Ω    | `\Omega`   | ω    | `\omega`   | Omega    | 奥米伽   |

### 常用运算符

*   **求和**：`\sum`
    *   `$\sum_{i=1}^{n} i$` → $\sum_{i=1}^{n} i$
*   **积分**：`\int`
    *   `$\int_{a}^{b} x^2 dx$` → $\int_{a}^{b} x^2 dx$
*   **极限**：`\lim`
    *   `$\lim_{x \to \infty} f(x)$` → $\lim_{x \to \infty} f(x)$
*   **乘积**：`\prod`
    *   `$\prod_{i=1}^{n} i$` → $\prod_{i=1}^{n} i$

### 大型括号
*   使用 `\left` 和 `\right` 让括号大小自动匹配内容高度。
*   **示例**：
    *   `$(\frac{1}{x})$` → $(\frac{1}{x})$ (不好看)
    *   `$\left( \frac{1}{x} \right)$` → $\left( \frac{1}{x} \right)$ (好看)
*   也可以手动指定大小：`\big, \Big, \bigg, \Bigg`
    *   `$\Big( \bigg( \Bigg( $`

### 矩阵与行列式
*   **语法**：使用 `\begin{matrix} ... \end{matrix}` 环境。
*   用 `&` 分隔元素，用 `\\` 换行。
*   **示例**：
    ```latex
    $$
    \begin{matrix}
    1 & 2 \\
    3 & 4 \\
    \end{matrix}
    \quad
    \begin{bmatrix}  % 带中括号的矩阵
    1 & 2 \\
    3 & 4 \\
    \end{bmatrix}
    \quad
    \begin{vmatrix}  % 行列式
    1 & 2 \\
    3 & 4 \\
    \end{vmatrix}
    $$
    ```
*   **渲染效果**：
    $$
    \begin{matrix}
    1 & 2 \\
    3 & 4 \\
    \end{matrix}
    \quad
    \begin{bmatrix}
    1 & 2 \\
    3 & 4 \\
    \end{bmatrix}
    \quad
    \begin{vmatrix}
    1 & 2 \\
    3 & 4 \\
    \end{vmatrix}
    $$

### 多行公式对齐
*   **语法**：使用 `\begin{align} ... \end{align}` 环境。
*   用 `&` 指定对齐位置（通常是等号），用 `\\` 换行。
*   **示例**：
    ```latex
    $$
    \begin{align}
    f(x) &= (x+1)^2 \\
         &= x^2 + 2x + 1
    \end{align}
    $$
    ```
*   **渲染效果**：
    $$
    \begin{align}
    f(x) &= (x+1)^2 \\
         &= x^2 + 2x + 1
    \end{align}
    $$



## 注意事项与平台兼容性

1.  **渲染引擎**：Markdown 本身不渲染公式，需要特定的渲染器或平台支持。
    *   **完美支持**：几乎所有专业的数学、学术平台（如 arXiv, Overleaf），以及许多现代笔记软件（如 **Obsidian**, **Typora**, **VSCode with Markdown Preview Enhanced**）和网站（如 **Stack Overflow**, **GitHub**（部分支持））。
    *   **需要插件**：在一些静态网站生成器中（如 **Hexo**, **Hugo**），需要安装额外的 MathJax 或 KaTeX 插件。
    *   **不支持**：一些极简的 Markdown 解析器可能不支持。

2.  **转义字符**：因为 `\`, `$`, `_`, `^` 等在 LaTeX 和 Markdown 中都有特殊含义，有时需要转义。例如，想文本显示 `$` 符号，需要写 `\$`。

3.  **空格**：LaTeX 会忽略大多数空格。需要手动添加空格时，可以使用 `\,` (小空格), `\:` (中空格), `\;` (大空格), `\quad` (更大空格)。

