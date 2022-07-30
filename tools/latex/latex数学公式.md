## align环境
常见的环境是使用`=`进行对齐,但是这样对左边长右边短的不是很友好.`align`和`align*`使用`&`进行对齐.

`align`环境是数学公式常用的基本环境.其有两种环境:`align`默认给公式进行编号,`align*`默认无编号.
### 环境特点
### 基本语法

使用`&`进行对齐

$$
\begin{align*}
  & X(0) = x(0)W_{N}^{0\cdot0} + x(1)W_{N}^{0\cdot1} + \cdots + x(N-1)W_{N}^{0\cdot(N-1)}\\
  & X(1) = x(0)W_{N}^{1\cdot0} + x(1)W_{N}^{1\cdot1} + \cdots + x(N-1)W_{N}^{1\cdot(N-1)} \\
  & \cdots \\
  & X(N-1) = x(0)W_{N}^{(N-1)\cdot0} + x(1)W_{N}^{(N-1)\cdot1} + \cdots + x(N-1)W_{N}^{(N-1)\cdot(N-1)} \\
\end{align*}
$$