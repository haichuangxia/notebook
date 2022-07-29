工作区（Working Directory）:电脑上的目录，就是你自己编辑的。通俗来讲，工作区就是已track但未add的文件。
版本库（Repository）：定义在`.git`隐藏目录中，该目录中重要的组成部分部分有：暂存区(stage or index),git自动创建的第一个分支master，以及指向master栈顶的指针head。
使用`git add`命令将文件从**工作区**添加到**暂存区**。
使用`git commit`命令将文件从**暂存区**提交到**当前分支**。