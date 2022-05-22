&emsp;&emsp;git可以创建不同的分支，每个分支其实就是一个指向时间线上不同节点的指针。<font color=red>HEAD</font>指针志向指向当前分支，如果当前分支是master，则HEAD指向master。
## 1. 创建和进入分支
### 1. 创建分支
创建分支实质就是在当前<font color=red>**commit**</font>的基础上创建一个新的指针。使用`git branch <newbrach>`创建一个新的分支。

之后使用`git branch`命令查看当前所有的分支，其消息为：
```
* master # *表示是当前的分支
  testing
  ```

`git log`命令的基本结构为：\<commit id,message>。可使用`git log --oneline --decorate`命令，查看当前HEAD指针所指向的分支。其消息结构为：
```
f30ab (HEAD -> master, testing) 最近一次提交
# 表示master和testing两个分支指针都指向f30ab这个提交对象
34ac2 第二次提交
98ca9 初始提交
```
### 2. 进入分支
使用`git checkout <branch>`命令可以进入不同的分支。也可以使用`git switch <branch>`命令切换到其他分支。
### 3. 创建并进入分支

`git checkout -b <branch>`命令可以创建并进入一个分支，相当于`git branch <branch>`+`git checkout <branch>`.

新版本也用`git swithc -c <branch>`命令来创建并切换到新分支。
## 2. 分支的操作
### 1. 删除分支
使用`git branch -d <branch>`可以删除一个分支。
### 2. 合并分支
使用`git merge <devbranch>`将<font color=red>devbranch</font>分支合并到**当前分支**。有一点注意：如果两个分支同名文件分别进行了改变，则进行合并时会造成冲突，则合并后的结果示例为：
```
# 当前分支master
<<<<<<< HEAD
add a new line in master.
=======
add another line in devbranch
>>>>>>> testing
```

其会同时显示maser分支和dev分支所作改变。这个时候需要手动的解决冲突，确定要留下哪些。