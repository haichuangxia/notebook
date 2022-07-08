## 流程图节点形状
&emsp;&emsp;使用不同的符号可以定义不同形状的节点,下表列出常见几种流程图的节点形状,详情查询官方文档:[REFERENCE-1.流程图节点形状](#reference)

<table>
<tr>
<td>样式</td>
<td>示例代码</td>
</tr>
<!-- 圆角矩形节点 -->
<tr>
<td>

 ``` mermaid
flowchart TD
start(圆角矩形节点)
```
</td>

<td>
<pre>
``` mermaid
flowchart TD
start(圆角矩形节点)
```
</pre>
</td>

<!-- 矩形节点 -->
<tr>
<td>

``` mermaid
flowchart LR
s[矩形节点]
```
</td>
<td>
<pre>
``` mermaid
flowchart TD
start(矩形节点)
```
</pre>
</td>
</tr>
<!-- 菱形节点 -->
<tr>
<td>

``` mermaid
flowchart LR
s{菱形节点}
```
</td>
<td>
<pre>
``` mermaid
flowchart TD
node{菱形节点}
```
</pre>
</td>
</tr>
<!-- 球场形节点 -->
<tr>
<td>

``` mermaid
flowchart LR
s([球场形节点])
```
</td>
<td>
<pre>
``` mermaid
flowchart TD
node([球场形节点])
```
</pre>
</td>
</tr>
<!-- 圆形节点 -->
<tr>
<td>

``` mermaid
flowchart LR
node((球场形节点))
```
</td>
<td>
<pre>
``` mermaid
flowchart TD
node((球场形节点))
```
</pre>
</td>
</tr>
</table>

## 关系形状

## REFERENCE
1. [mermaid节点形状](https://mermaid-js.github.io/mermaid/#/flowchart?id=node-shapes)
2. [节点之间的关系](https://mermaid-js.github.io/mermaid/#/flowchart?id=links-between-nodes)