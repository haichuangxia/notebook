## 1. CNN的谱系

  - ![img](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimage.mamicode.com%2Finfo%2F202004%2F20200420235352407094.png&refer=http%3A%2F%2Fimage.mamicode.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1651633670&t=b08ef9884aefbba2d053608c219a77f8)

## 2. AlexNet
  ### 1. 基本结构
####	1. 基本网络结构

 

####	2. 细化的网络结构

​	![preview](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221018155826.jpg)

### 2. AlexNet基本技术

####		1. ReLu函数作为激活函数

- ReLu函数的基本图像

  ​	![img](https://qingbin.oss-cn-chengdu.aliyuncs.com/img/2022/20221018155851.jpg)

- ReLu函数基本特点

  ReLu函数是一个分段的线性函数,相对于sigmod函数,其主要特点有

  - 计算开销小:
  - 梯度饱和问题
  - 稀疏性

####		2. 局部响应归一化(Local Response Normalization)
#####				1. 基本作用

​			ReLu函数不像sigmod核tanh是有限区间函数,因此使用**ReLu**作为激活函数后需要进行**归一化**处理

#####				2. LRN的基本公式
####		3.重叠池化

​	重叠池化:**<font color=red>池化移动步长小于池化核的大小,相邻的感受野存在重叠部分</font>**,使用重叠池化的好处有:

- 减缓过拟合:重叠池化也是池化的一种方式,因此具有一般池化的降维,实现减少过拟合
- 提高精度:重叠池化的特征提取精度较高



### 3. 层次结构分析

   AlexNet有 **8**个带权层,前5个为卷积层,后三个为全连接层,将同一层分为两块,分别在两个GPU上运行
   ####	1. Input(224×224×3)

  输入层是彩色图,分为RGB共**3**个通道,每个通道为**224×224**大小的输入图

   ####	2.C1(2×48×11×11×3)

​	使用大小为**11×11×3**,步长为**4像素**的大卷积核,C1层一共是分为**2×48**个卷积核,对RGB三个通道进行卷积,平均每个通道使用32个卷积核进行卷积,每**48**个通道放在一块GPU上运算,实现并行计算,其相关的参数计算为

- padding的大小
  $$
  \because padding=\frac{sieze\_filter-1}{2}
  \\带入size\_filter=11得
  \\\therefore padding=5
  $$

- 特征映射的大小计算
  $$
  \because n_{out}=\lfloor \frac{n_{in}+2p-f}{s}+1\rfloor\\
  带入输入大小n_{in}=224,padding大小2p=3,卷积核大小f=11,步长s=4得:\\
  n_{out}=\frac {227-11}{4}+1\\
  =55\\
  \because C1层一共使用了2×48个卷积核\\
  \therefore C1层feature map=2×48×55×55
  \because 系统使用双GPU进行运算\\
  \therefore 每个GPU处理的数据为:\\
  55×55×48
  $$

- overlapping pooling/subsamping操作

  使用大小**3×3**,步长为**2**的**重叠池化**单元,<font color=red>步长小于池化单元的宽度</font>,则输出为
  $$
  n_{out}=\frac {n_{in}+2p-f}{s}+1 \\
  带入n_{in}=55,f=3,得:\\
  n_{out}=\frac{55+0-3}{2}+1 \\
  =27
  $$

- 局部响应归一化

 ####	3. C2层

- 输入的大小:2×48×27×27,一共两个GPU,每个GPU训练48个通道,每个通道27×27

- C2层卷积核

     - 规格:
       
        - 使用**128**个大小为**5×5**的卷积核,卷积的步长**s=1**
        
     - padding的大小
        $$
        padding=\frac{size\_filter-1}{2} \\
        带入size\_filter=5得:\\
        padding=2 \\
        \therefore C2层padding大小为2+2
        $$
        
     - 卷积特征映射输出计算
       $$
       \because n_{out}=\lfloor \frac{n_{in}+2p-f}{s}+1\rfloor\\
       带入n_{in}=27,padding大小p=2,卷积核大小f=5,移动步长s=1得:\\
       n_{out}=\lfloor \frac{27+2×2-5}{1}+1\rfloor\\
       n_{out}=27 \\
       \therefore 特征映射为27×27的矩阵\\
       \because C2层一共有128个卷积核
       \therefore C2层输出大小为27×27×128
       $$
       

- 重叠池化

     C2层使用大小为**3×3**,步长为**2**的重叠池化核.则池化操作后的输出为:
     $$
     \because n_{out}=\frac {n_{in}+2p-f}{s}+1 \\
     带入n_{in}=27,p=0,f=3,s=2得:\\
     n_{out}=\frac{27-3}{2}+1=13\\
     \therefore 每个池化核输出大小为13×13的矩阵 \\
     \because 一共有128个通道
     \therefore 池化后的输出大小为13×13×128
     $$

- 局部响应归一化LRN

####	4. C3层

​	不再分GPU来进行训练,而是对进行交叉,实现特征融合





