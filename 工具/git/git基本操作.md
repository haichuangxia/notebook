&emsp;&emsp;git的基本操作，
## 1. 初始化
&emsp;&emsp;使用git init初始化一个目录作为git管理的仓库。
&emsp;&emsp;创建仓库是进行版本控制的第一步，告诉git哪个文件夹中的文件需要进行版本控制。他会创建一个空的git repository或者重新初始化一个已经存在的repository。有两种创建仓库的方式：
| 方式           | 命令                | 说明                                                 |
| -------------- | ------------------- | ---------------------------------------------------- |
| 当前目录初始化 | `git init`          | 在当前目录下初始化，将当前目录作为repository进行管理 |
| 指定目录初始化 | ` git init <path> ` | 在指定的目录下进行初始化                             |

&emsp;&emsp;使用`git clone [option] url`命令可以克隆一个已有的仓库.

## 2. 添加修改
&emsp;&emsp;使用`git add`命令可以将文件添加到暂存区，被 **add**过的内容将被git追踪(track),其基本的用法为：
| 命令                       | 说明                                         |
| -------------------------- | -------------------------------------------- |
| `git add [file] [file]...` | 添加一个或多个文件到暂存区                   |
| `git add [dir]`            | 添加一个文件夹到暂存区，包括文件夹下所有文件 |
| `git add .`                | 添加所有文件                                 |

## 3. 查看修改
### 1. 查看文件状态（生命周期）
&emsp;&emsp;使用`git-status`命令可以查看文件或者文件的状态，主要是文件是否已经被add，在add之后是否有修改，是否commit。其消息构成为：
```
use "git add <file>..." to include in what will be committed
"examplefile1"
# 有新的文件未添加到暂存区，需要使用git add命令添加
no changes added to commit (use "git add" and/or "git commit -a")
# 是否有已经add到暂存区的已经commit，若有则使用git commit命令提交
```
&emsp;&emsp;git中有两种状态：已经追踪的(tracked)和未追踪的(untracked)。已经追踪的文件主要是指那些曾经被add过的内容，这些文件可能是已经修改，未被修改，已经暂存的。而未追踪的文件主要是指那些git不管的文件，通常在 **.gitignore**文件中定义。git中文件的生命周期(status)如图所示：
![git文件生命周期](../../picture/lifecycle.png)
其几个状态为：
- untracked:未被git管理的，git不追踪文件内容是否发生变化的，通常是定义在.gitignore文件中。
- unmodified:未发生更改的文件，即上次commit之后内容未发生改变。
- modified：上次commit之后又进行了修改，文件内容发生了变化。
- staged：文件被add，但是还没有commit，属于被暂存的状态。
- commited：文件被添加到版本库。
### 2. 查看文件内容变化
&emsp;&emsp;`git status`注重于文件的宏观状态，表明文件处于何生命周期，而要想具体查看某个文件内容发生了什么变化，则需要使用`git diff`命令。注意：`git diff`查看的是文件在上次`commit`之后内容发生了何种变化，未`commit`过的文件无法查看。
&emsp;&emsp;不过有一点值得注意：现在有各种git插件，使用`git diff`命令在命令行查看文件内容改动并不直观，不如直接用git插件直接就能看到，因此实际用途较少。
&emsp;&emsp;使用`git diff`命令查看文件变化，其基本消息格式为
```
- 这是之前commit的内容，-表示删除
+ 这是修改之后的内容，+表示新增
```
&emsp;&emsp;`git diff`命令的主要应用场景为：
| 命令                           | 场景                                                                                                        |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------- |
| `git diff`                     | **工作区**文件与最后一次**commit**(HEAD)的文件之间的差别比较                                                |
| `git diff --staged`            | **暂存区(已add，未commit)**文件与最后一次**commit**(HEAD)的文件之间的差别比较                               |
| `git diff HEAD`                | **工作目录(已track但未add文件)和暂存区(已add，未commit)**文件与最后一次**commit**(HEAD)的文件之间的差别比较 |
| `git diff <branch1> <branch2>` | 显示两个分支之间的差别                                                                                      |

&emsp;&emsp;查看差别之后可以使用`q`来退出`git diff log`
## 4. 撤销修改
&emsp;&emsp;撤销修改主要有`git checkout`和`git reset`命令，其主要区别为：
| 命令                     | 区别                                                                                     |
| ------------------------ | ---------------------------------------------------------------------------------------- |
| `git checkout -- <file>` | 撤销工作区的修改，清空工作区，也就是文件内容回发生变化，回退到上一个版本，相当于没有修改 |
| `git reset HEAD <file>`  | 清除暂存区，相当于没有add，文件的内容不发生变化                                          |

### 1. 撤销单个文件的修改
&emsp;&emsp;可以使用`git checkout -- <file>`命令来撤销所作的修改。有一点需要注意：使用该命令会撤销所有的修改，将文件还原到最近一次**commit**或者**add**时的状态(HEAD).且该命令不可恢复。
### 2. 撤销整个暂存区的修改
&emsp;&emsp;可以使用`git reset`命令回退版本，将文件从暂存区放回工作区，相当于撤销add操作。通常使用`git reset HEAD <file>`撤销文件的add，将其放回工作区。
### 3. 删除文件
&emsp;&emsp;有时候你在本地删除了某一个文件，但是这样就会造成版本库和工作区不同，这个时候就有两种处理方法
1. 在版本库中删除该文件，使版本库与工作区保持一致
2. 从版本库中恢复该文件，使工作区与版本库保持一致

&emsp;&emsp;第一种方式就可以使用`git rm`命令在版本库中删除该文件。
## 5. 提交
### 1. 提交记录
&emsp;&emsp;使用`git log`命令可以查看提交历史。
### 2. 提交
&emsp;&emsp;有两种提交方式：
| 方式                        | 命令                                    |
| --------------------------- | --------------------------------------- |
| 常规提交，先add，后commit   | `git commit [option file] -m "message"` |
| 懒人提交，不用add，一步到位 | `git commit -a`                         |

### 3. 推送远程
&emsp;&emsp;对于远程仓库的提交，则使用`git push`命令进行推送，其常用的用法为：
| 命令                            | 用法                                                          |
| ------------------------------- | ------------------------------------------------------------- |
| `git push <alias> <branch>`     | 将当前版本推送到远程仓库                                      |
| `git push -u <remote> <branch>` | 添加参数u，表示关联，后续可以直接`git push`进行推送，简化命令 |  |
## Reference
1. [git init Reference](https://git-scm.com/docs/git-init)
2. [git clone Reference](https://git-scm.com/docs/git-clone)
3. [git add reference](https://git-scm.com/docs/git-add)
4. [git status reference](https://git-scm.com/docs/git-status)
5. [git记录仓库变化-git add && git status && git diff](https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository)