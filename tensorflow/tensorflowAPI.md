### 1. 图像处理API
  #### 1. ImageDataGenerator
   - 作用
     
      利用图像增强,解决样本集过少的问题,提高模型的泛化能力
## 2. 模型构建API
  ### 1. keras.module
   #### 1. Sequantial
-    基本功能
    堆叠式的网络,每层顺序连接

#### 2. summary()
#### 3. compile()-构建模型
- loss-定义损失函数

- optimizer-定义优化器,动态调整学习步长

- metrics-定义性能度量

#### 4. fit_generator()-训练模型
- epochs:定义训练的轮次
- verbose:是否记录日志,返回history对象
#### 5.save()-保存参数
- path:保存到一个指定的路径,文件后缀名为h5
## 2. keras.layer
   - Con2D()-2维的卷积层
   - MaxPooling()-最大池化层
   - Flatten()-将矩阵拉伸为向量
      将多维的数据一维化,常常用在卷积层核全连接层之间实现过度,不会影响batchsize的大小
        - dense()-全连接网络层