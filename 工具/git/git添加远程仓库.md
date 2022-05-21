&emsp;&emsp;在本地的项目中可以添加远程的gitee或者github仓库，首先要配置本地与服务器的通信，其基本的步骤为：
``` mermaid
flowchart LR
config[配置用户信息]-->ssh[本地生成ssh公钥]-->addshh[服务器添加ssh公钥]-->test[测试连接]
```
&emsp;&emsp;在成功进行通信之后，则需要在本地添加远程仓库，其基本步骤为：
``` mermaid
flowchart LR
establish[服务器创建远程仓库]-->copy[复制远程仓库url]-->add[本地添加远程仓库]
```
### 1. 配置git用户信息
&emsp;&emsp;创建仓库之后需要对仓库或者对整体系统的所有仓库进行配置，有两种配置模式：

| 模式     | 说明                                 |
| -------- | ------------------------------------ |
| 单独设置 | 对单个repository进行配置             |
| 全局配置 | 对系统的所有的repository进行默认配置 |

&emsp;&emsp;使用`git config`命令配置 **开发者用户**和**邮箱**。
#### 1. 查看配置信息
&emsp;&emsp;使用`git config --list`命令可以全部配置信息。使用`git config \<key>`命令可以查看指定的配置信息，如`git config user.name`查看用户名。
#### 2. 设置配置信息
&emsp;&emsp;如果没有配置用户信息，则需要进行配置。一般来说，一定要配置的用户信息有两个：**用户名**和**邮箱**，配置的命令为：
```
git config use.name "youname"
git config user.email example@gmail.com
```
### 2. 与github通信
#### 1. 本地生成ssh密钥
&emsp;&emsp;本地与GitHub通信通常使用ssh协议进行通信，当然也可以使用https协议。使用ssh协议进行通信则需要在本地生成公钥，然后将公钥添加到github中，以此实现身份认证。生成公钥密文需要使用**ssh-keygen**工具。使用rsa生成公钥的命令为：` ssh-keygen -t rsa -C 'youremail'`，之后按照提示按3个`enter`就行，懒得配置其他东西。

&emsp;&emsp;之后使用`cd ~/.ssh`找到生成的公钥id_rsa.pub文件，使用`vim id_rsa.pub`命令查看该文件，复制文件内容。
#### 2. 服务器添加公钥
&emsp;&emsp;需要在github或者gitee添加本地生成的密钥，一般在github.setting中添加公钥。
#### 3. 测试与服务器的连接
&emsp;&emsp;使用` ssh -T git@gitee.com`命令或者`ssh -T git@github.com`命令测试gitee或者github的连接。
### 3. 本地添加远程仓库
#### 1. github创建仓库
&emsp;&emsp;创建仓库之后，复制仓库的链接。
#### 2. 本地添加远程仓库
&emsp;&emsp;本地使用`git remote`命令进行远程仓库的管理。主要的操作有：

| 命令                                    | 用法                         |
| --------------------------------------- | ---------------------------- |
| `git remote --verbose`                  | 详细查看当前的远程仓库的信息 |
| `git remote add [] <name> <url>`        | 添加远程仓库                 |
| `git remote rename <oldname> <newname>` | 更改远程分支的名称           |
| `git remote remove <name>`              | 删除远程分支                 |

&emsp;&emsp;主要使用`git remote add [] <name> <url>`命令添加远程仓库。

### 4. 官方文档
1. [`git remote`命令官方文档](https://git-scm.com/docs/git-remote)
2. [服务器上的git-生成ssh公钥](https://git-scm.com/book/zh/v2/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-%E7%94%9F%E6%88%90-SSH-%E5%85%AC%E9%92%A5)