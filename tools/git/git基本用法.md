## 1. git基本用法
自己常用的git两个用途：

1：对自己创建项目进行版本控制

2：克隆其他人的项目
### 1. 自己项目版本控制
#### 1. 基本流程
&emsp;&emsp;对自己的项目进行版本的基本流程是：
- 使用git init命令初始化一个文件架作为一个需要git进行控制的repository
- 编辑文件，包括增删改文件和文件夹等
- 查看修改并提交
- 推送到远程

&emsp;&emsp;完整的过程为：
``` mermaid
flowchart LR
	start([初始化])-->edit[编辑文件]-->diff[查看修改]-->commit[提交]-->push[推送远程]
	clo(clone)-->edit
```
#### 2. 创建仓库
&emsp;&emsp;创建仓库是进行版本控制的第一步，告诉git哪个文件夹中的文件需要进行版本控制。他会创建一个空的git repository或者重新初始化一个已经存在的repository。有两种创建仓库的方式：
| 方式           | 命令             | 说明                                                 |
| -------------- | ---------------- | ---------------------------------------------------- |
| 当前目录初始化 | git init         | 在当前目录下初始化，将当前目录作为repository进行管理 |
| 指定目录初始化 | git init \<path> | 在指定的目录下进行初始化                             |
#### 3. git配置
&emsp;&emsp;创建仓库之后需要对仓库或者对整体系统的所有仓库进行配置，有两种配置模式：

| 模式     | 说明                                 |
| -------- | ------------------------------------ |
| 单独设置 | 对单个repository进行配置             |
| 全局配置 | 对系统的所有的repository进行默认配置 |

&emsp;&emsp;使用`git config`命令配置 **开发者用户**和**邮箱**。
##### 1. 查看配置信息
&emsp;&emsp;使用`git config --list`命令可以全部配置信息。使用`git config \<key>`命令可以查看指定的配置信息，如`git config user.name`查看用户名。
##### 2. 设置配置信息
&emsp;&emsp;一般来说，一定要配置的用户信息有两个：**用户名**和**邮箱**，配置的命令为：
```
git config use.name "youname"
git config user.email example@gmail.com
```
##### 3. 添加远程仓库
###### 1. 创建空仓库
可以使用gitee或者github创建一个空的远程仓库。
###### 2.
#### 3. 克隆仓库
&emsp;&emsp;使用git clone命令克隆仓库时需要注意，克隆的路径需要是一个空目录，即用来放repository的文件夹需要是空的。
| 方式           | 命令                     | 说明                 |
| -------------- | ------------------------ | -------------------- |
| 克隆到当前目录 | git clone \<rep>         | 克隆仓库到当前目录   |
| 克隆到指定目录 | git clone \<rep> \<path> | 克隆仓库到指定的路径 |