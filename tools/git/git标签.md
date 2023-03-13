git可以给每个commit打上标签，可以用来标识不同的版本，有两种标签：
1. 轻量标签(lightweight)：光一个空标签
2. 附注标签(annotated)：带一个文字信息进行说明的标签
## 1. 创建标签
### 1. 创建轻量标签
主要使用`git tag`命令打标签，其基本用法有：
| 命令                        | 用法                     |
| --------------------------- | ------------------------ |
| `git tag <tag>`             | 在HEAD指针处添加标签     |
| `git tag <tag> <commit id>` | 在特定的commit处添加标签 |

### 2. 创建附注标签
只需要使用`-a`参数就可以创建带有附注的标签
| 命令                                          | 用法                           |
| --------------------------------------------- | ------------------------------ |
| `git tag -a <tag> -m <"message">`             | 在HEAD指针处添加带有附注的标签 |
| `git tag -a <tag> -m <"message"> <commit id>` | 在特定的commit处添加标签       |
## 2. 标签的查看和删除
### 1. 查看标签
使用`git show <object>`命令可以查看各个对象，例如tag或者commit对象。
### 2. 删除标签
使用`-d`参数删除标签，其语法如下
`git tag -d <tag>`

## Reference
1. [git打标签](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E6%89%93%E6%A0%87%E7%AD%BE)
2. [git tag refence](https://git-scm.com/docs/git-tag)