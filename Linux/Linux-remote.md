**xshell**，**xftp**，**putty**等都是常见的终端模拟工具，通过**ssh**，**sftp**等协议与远程服务器进行通信。putty还没用过，**xshell7**和**xftp7**都有弹窗，虽说可以使用工具可以去拦截，但是呢，使用商业公司的东西没啥安全感，再有就是**wsl**这个东西挺好玩的，也没有必要搞一大堆东西装着。

# ssh介绍
**SSH（Secure Shell）**是一种网络安全协议，通过**加密**和**认证机制**实现安全的访问和文件传输等业务。**SSH**通过在网络中建立**安全隧道**来实现**SSH Client**和**SSH Server**之间的连接，最常用的用途就是**远程登陆系统**。


## ssh的组成部件
**SSH**作为一种协议，最常用的开源实现方案是**openssh**，其包括的主要部件有：
1. 使用**SSH**，**scp**，**sftp**实现的远程操作
1. **ssh-add**，**ssh-keysign**，**ssh-keyscan**，**ssh-keygen**等密钥管理工具
1. **sshd**，**sftp-server**和**ssh-agent**等服务端工具

### scp
scp(Secure Copy):主要是进行主机之间的文件传输,无法进行文件的相关管理工作.适用于小规模的文件传输.

基本的语法是:`scp [source] [target]`:将`source`文件复制到`target`目录.

### sftp
sftp(SSH File Transfer Protocol)是ftp的改进版,是ssh的组成部分,提供文件的**访问**,**传输**和**管理**功能.
基本的语法是:`op [source] [target]`:将`source`文件通过`op`操作到`target`目录.

## ssh认证方式
ssh server对ssh client进行用户认证的方式有：
| 认证方式               | 详细                           |
| ---------------------- | ------------------------------ |
| password认证           | 通过用户名和密码进行登陆       |
| publickey认证          | 客户端通过公钥等进行认证       |
| password-publickey认证 | 需要同时满足密钥认证和密码认证 |
| all认证                | 只需要满足其中一种即可         |

# SSH远程登陆
## SSH连接远程主机命令
通过**SSH-Client**可以连接到运行了**SSH-Server**的远程机器上，其基本的使用方法为：
```
ssh user@remote -p port
```

命令的解释为：
| 格式   | 意义                           |
| ------ | ------------------------------ |
| ssh    | OpenSSH remote login client    |
| user   | 用户名                         |
| remote | 主机域名或者是IP地址           |
| port   | ssh server监听的端口，默认值22 |

## 使用密码认证方式进行认证
使用密码认证方式连接服务器，需要在服务器的`/etc/ssh/sshd_config`配置文件中，开启`PasswordAuthentication yes`,否则会显示：
```
$ssh zhangsan@10.10.10.10
zhangsan@10.10.10.10: Permission denied (publickey).
```
注意:**修改配置项之后需要重启ssh服务**

## 使用公钥认证方式进行认证
### 开启公钥认证
首先需要在服务器的`/etc/ssh/sshd_config`配置文件中,开启使用公钥方式认证.需要开启以下配置项:
```
RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
```

修改配置项之后,需要重启**sshd**服务:`sudo service sshd restart`(以Ubuntu下命令为例)

### 生成密钥
使用一般的密码认证方式，一则次次输入密码很麻烦，二则也不方便。使用公钥认证方式更方便，同时也比较安全。使用**ssh-keygen**工具即可生成SSH密钥。

SSH-keygen参数

| 选项 | 含义                                                                                                                    |
| ---- | ----------------------------------------------------------------------------------------------------------------------- |
| -b   | <位数>指定创建密钥的位数（长度                                                                                          |
| -f   | <文件名>指定用来保存密钥的路径文件名（自动添加.pub 等后缀）                                                             |
| -N   | <新密语>提供一个新密语                                                                                                  |
| -P   | <密语>提供（旧）密语                                                                                                    |
| -C   | <注释>添加注释                                                                                                          |
| -F   | <主机名>搜索known_hosts文件中指定的主机名，列出发现的任何事件                                                           |
| -l   | 显示公钥文件的指纹数据                                                                                                  |
| -t   | <类型>指定密钥创建的类型，SSH-v1可选的值为rsa1，SSH-v2可选的值为rsa或dsa-r<主机名>显示指定主机公钥文件的SSHFP指纹资源记 |


注意:
- 密钥设置密码之后,需要输入密码才能使用密钥文件

### 添加公钥到远程主机
可以手动方式,也可以使用`ssh-copy-id`工具自动将公钥添加到服务器的认证文件中
#### 手动添加
1. 复制本地路径下的公钥文件(.pub)内容,默认是`~/.ssh/xxx.pub`
2. 以管理员权限编辑服务器的` ~/.ssh/authorized_keys`
3. 将`xxx.pub`的内容追加到` ~/.ssh/authorized_keys`

#### 使用ssh-copy-id工具添加
**ssh-copy-id**是一个自动向服务器添加公钥的脚本工具,通过该工具向服务器添加公钥的步骤为:
1. 确保当前可以使用密码或者密钥登陆:检查服务器`/etc/ssh/sshd_config`中的配置项,否则会出现拒绝连接的情况
2. 使用`ssh-copy-id -i x/x.pub user@host`的命令添加公钥到服务器



#### 更新密钥

当之前已经与一个主机建立连接，则后面要想使用ssh-copy-id将公钥复制过去，就会报错：

``` shell
/usr/bin/ssh-copy-id: ERROR: @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
ERROR: @    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
ERROR: @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
ERROR: IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
ERROR: Someone could be eavesdropping on you right now (man-in-the-middle attack)!
ERROR: It is also possible that a host key has just been changed.
ERROR: The fingerprint for the ECDSA key sent by the remote host is
ERROR: SHA256:ca4JSnnfFP6lCyf+EY0fMMXAFiVVuDQEthevukwoMMQ.
ERROR: Please contact your system administrator.
ERROR: Add correct host key in /home/qing/.ssh/known_hosts to get rid of this message.
ERROR: Offending ECDSA key in /home/qing/.ssh/known_hosts:5
ERROR:   remove with:
```

此时可以使用命令将之前已有的记录删除。，如：

``` shell
 ssh-keygen -f "known_hosts" -R "127.0.0.1"
```



### 使用公钥方式连接远程服务器
使用`ssh user@host`命令即可使用公钥认证方式连接远程服务器

### 配置别名
每次都使用`ssh user@host`毕竟还是有些麻烦,ip地址懒得记,也懒得输.在`~/.ssh/config`中为host配置别名可以解决这个问题.配置项如下:
```
Host ALIAS
    HostName REMOTE
    User USER_NAME
    Port PORT
```

# SSH文件操作
在**SSH**中,主要通过**SCP**和**SFTP**组件进行文件的相关操作.两种工具的选择为:
| 工具 | 用途                                         |
| ---- | -------------------------------------------- |
| scp  | 简单的文件上传下载,不能涉及文件的管理工作    |
| sftp | 多个文件的传输,可以进行文件的管理,如创建目录 |

需要连续上传下载多个文件或者需要文件管理就用**sftp**,个别文件的传输就用**scp**
## scp文件操作
 scp — OpenSSH secure file copy.

常用的一些参数有:
| 参数 | NAME        | 含义                   |
| ---- | ----------- | ---------------------- |
| -r   | Recursively | 对一个目录进行递归操作 |

实例:`scp -r ~/testDirectory user@host:/home/hello/target`递归的将本地文件上传到远程主机

## sftp文件操作
使用`ssh user@host`命令连接到主机之后就会进入**命令模式**,此时就可以使用内置命令进行文件的相关操作.

常见的文件管理命令都可以在命令模式下使用.
### 常用的内置命令
| 命令  | 含义                     |
| ----- | ------------------------ |
| mkdir | 在远程主机创建文件夹     |
| rmdir | 删除远程目录             |
| put   | 上传本地文件到远程目录   |
| get   | 从远程主机下载文件到本地 |
| cd    | 切换目录                 |

### 操作示例
1. 连接远程主机(可以使用别名):`sftp alias`
2. 使用命令进行操作:`put hello.txt [dir]`:上传hello.txt到远程主机的`dir`路径下


# REFERENCE
1. [什么是SSH](https://info.support.huawei.com/info-finder/encyclopedia/zh/SSH.html)
2. [openssh的基本使用方法是什么](https://www.yisu.com/zixun/549092.html)
3. [sftp和scp的比较](https://help.aliyun.com/noticelist/articleid/6563956.html)