新增用户常用adduser命令，省时省事。
# 用户列表查询
## /etc/passwd文件查询用户
passwd文件记录了各个用户相关信息，因此可以通过打印该文件来查询用户列表
# 新增用户-adduser与useradd
新增用户可以使用两个命令：
1. adduser：创建用户，并在/home目录下生成对应的用户文件夹，新用户可以直接进行登陆
2. useradd：创建用户，但是默认不会创建用户文件夹，新用户需要手动配置之后才可以登陆

为了省事，直接用adduser进行操作

# 用户sudo权限设置
## 1. 切换到root用户
su root #设置soduer需要root权限

## 2. 编辑/etc/sudoers
### 1. 文件授权并编辑
```
chmod 777 /etc/sudoers # 默认情况下，该文件是不能写的，因此需要赋予权限
vim /etc/soduers
```

### 2. 新增安全策略

在文件中添加相应用户的权限，sudoers的安全策略为：
| root                              | ALL                   | =(ALL)                               | ALL                      |
| --------------------------------- | --------------------- | ------------------------------------ | ------------------------ |
| 用户或组名，%表示组，无则表示用户 | 主机,ip地址或者主机名 | 目标用户，以谁的身份执行命令，如root | 需要执行的命令的绝对路径 |

例如：`qing ALL=（ALL） ALL`

### 3. 修改/ect/soduers的权限

需要将文件的权限改回来，不然Linux系统会阻止使用sudo权限,报错如下：
```
games@VM-4-3-ubuntu:/home$ sudo
sudo: /etc/sudoers is world writable
sudo: no valid sudoers sources found, quitting
sudo: unable to initialize policy plugin
```

因此，需要使用将权限修改一下：
```
pkexec chmod 555 /etc/sudoers
pkexec chmod 555 /etc/sudoers.d/README
```


# REFERENCE
1. [linux详解sudoers](https://www.cnblogs.com/jing99/p/9323080.html)
2. [Linux sudo和sudoers详解！](https://juejin.cn/post/6992609027234463758)