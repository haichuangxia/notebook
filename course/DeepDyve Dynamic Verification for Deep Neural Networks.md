# DeepDyve: Dynamic Verification for Deep Neural Networks

传统的都是针对恶意输入的防御手段，这个方案是解决DNN系统本身出现的故障（如参数或者计算出现错误）。



这个方案并不是优先提高错误覆盖率，而是优先覆盖能够造成较大危害的错误。---只关注那些会引起误分类的错误。

## 摘要

| 概念              | 作用                                                         |
| ----------------- | ------------------------------------------------------------ |
| checker DNN model | 比原始DNN更简单，小巧的预训练的神经网络，用于动态验证。<br />其能够和原始的DNN在大多数相同情况下输出相同结果，只是准确度较低。 |
| task DNN model    | 原始的处理任务的DNN                                          |
| 对抗样本攻击      | Adversarial example attacks [9, 28] try to fool DNN systems by crafting subtle malicious perturbations on inputs, |
| 注错攻击          | fault injection attack aims to break the system by injecting faults into the inter-<br/>nal system execution pipeline |



## SECTION２：前提

系统本身会出现的错误

### DNN under Faults

#### 对抗性示例攻击：通过对输入进行干扰来欺骗DNN系统

#### 注错攻击：故意输入故障数据

- 时钟故障攻击
- 电压故障攻击
- rowhammer攻击
- 环境扰动引起故障



注错攻击会显著降低DNN系统的分类性能





## SECTION3:Preview

- DeepDyve系统结构

  ![image-20221216004207132](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221216004208.png)



### Checker DNN的三个评价指标

![image-20221216004740317](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221216004741.png)

| 参数     | 含义                         |
| -------- | ---------------------------- |
| params   | 模型参数存储空间（MB）       |
| FLOP（） | 计算神经网络乘法和假发的数量 |
| O（s）   | storage overhead             |
| O（c）   | 计算量                       |

公式讲解：在公式下面一点



| 指标   | 含义           | 计算                                                         |
| ------ | -------------- | ------------------------------------------------------------ |
| $O(s)$ | 存储开销       | ![image-20221216162611072](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221216162611.png) |
| $O(c)$ | 计算开销       | ![image-20221216162715605](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221216162716.png) |
| *Cov*  | 差错覆盖率     | ![image-20221216164635341](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221216164636.png) |
| *WCov* | 加权的差错覆盖 | ![image-20221216164613044](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221216164613.png) |



### Design Stage--checker DNN的设计



![image-20221216010017693](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221216010018.png)

## SECTION4:CHECKER _DNN结构
### 生成Checker DNN Candidate

#### 结构压缩--主要是改变宽度

![image-20221216101834163](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221216101835.png)

参数α：width multiplier

#### 参数训练--知识蒸馏

1. 计算每个类别的概率

![image-20221216102555239](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221216102556.png)

P_i:

2. 减少知识蒸馏损失

   ![image-20221216102653110](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221216102653.png)

### Search Strategy
搜索策略基于**经验观察**，即两个模型的一致性与参数α有关，即：一致性是参数α的函数。consistency=f（α）；





参数α的取值：

![image-20221216104604951](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221216104605.png)

![image-20221216104320519](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221216104321.png)



## SECTION5：任务探索

1. 基于矩阵I，R，C实现任务简化

### 实现任务简化，生成一群任务--本质是聚类

一个矩阵

![image-20221216110806450](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221216110807.png)

![image-20221216105412331](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221216105413.png)

两个聚类原则--减少风向，减小开销

- coverage loss：合并风险矩阵中值较小的类别进行聚类
- Overhead saving：通过增加一致性，减少计算量



更新风险矩阵和C矩阵

在合并了两个类之后需要更新风险矩阵和计算矩阵C



####　分层集群方案－－算法１

![image-20221216111910637](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221216111911.png)



### 寻找最优任务

获取候选任务之后，通过进行注错来评估overhead savings和coverage loss。计算好之后就有类似表2的表：

![image-20221216101834163](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221216112230.png)

