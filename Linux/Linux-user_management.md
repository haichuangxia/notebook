新增用户常用adduser命令，省时省事。
# 用户列表查询
## /etc/passwd文件查询用户
passwd文件记录了各个用户相关信息，因此可以通过打印该文件来查询用户列表。

``` shell
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
```

passwd文件中每行标识一个用户账号，每行使用冒号(**:**) 分隔字段，一共有7个字段：


| mail | x | 8 | 8 | mail | /var/mai |/usr/sbin/nologin|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 登录名 | 加密后的密码，为保密，输出的时候使用x表示 | 数字用户id（uuid） | 组id（gid） | 用户名和注释 | 用户的主目录(home) | 用户命令解释器，如/bin/bash，设置使用哪种shell |

## 通过`ls /home`查看用户

通过该命令可以查看有哪些用户创建了单独的用户目录，通常新建的用户会创建子集的home目录。因此通过查看这个home目录就可以看有多少用户了

# 新增用户-adduser与useradd

新增用户可以使用两个命令：
1. adduser：创建用户，并在/home目录下生成对应的用户文件夹，新用户可以直接进行登陆
2. useradd：创建用户，但是默认不会创建用户文件夹，新用户需要手动配置之后才可以登陆

为了省事，直接用adduser进行操作

# 用户sudo权限设置
使用sudo将一个用户增加到sudo组中，使其可以使用sudo命令

## 使用visudo命令设置

使用该命令可以直接的编辑sudoers文件而不用手动的更改权限再编辑。注意：该命令使用的是nano编辑器。编辑完成之后之后ctl+o暂存更改，再输出enter确认更改，最后使用CTRL+x退出。

## 编辑/etc/sudoers
### 1. 文件授权并编辑
```
chmod 777 /etc/sudoers # 默认情况下，该文件是不能写的，因此需要赋予权限
vim /etc/soduers
```

### 2. 新增安全策略

在文件中添加相应用户的权限，sudoers的安全策略为：

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



## 安全策略

用户的安全策略如下：

| root                              | ALL                   | =(ALL)                               | ALL                      |
| --------------------------------- | --------------------- | ------------------------------------ | ------------------------ |
| 用户或组名，%表示组，无则表示用户 | 主机,ip地址或者主机名 | 目标用户，以谁的身份执行命令，如root | 需要执行的命令的绝对路径 |

``` shell
qing ALL=（ALL）NOPASSWD:  ALL
# 使用NOPASSWD命令sudo免密码
```



# REFERENCE

1. [linux详解sudoers](https://www.cnblogs.com/jing99/p/9323080.html)
2. [Linux sudo和sudoers详解！](https://juejin.cn/post/6992609027234463758)