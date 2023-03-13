python实现自动备份:
# 基本逻辑
该任务十分简单,主要的工作有:
1. 选择一个目录以保存备份文件
2. 将待备份的文件或者目录进行打包处理,并添加前后缀
3. 定期清理已经备份的文件

# 源代码
``` python
from pathlib import Path
import os
import tarfile
import datetime


def autobackup(source, target, delta):
    """
    auto backup dir
    :param source: the dir need to be backuped
    :param target: the dir where archives will be stored
    :param delta: delete the archives created delta dayes ago
    :return:
    """
    # if the dir doesn`t exist,one will be created`
    if not os.path.isdir(target):
        os.makedirs(target, exist_ok=True)  # creates dirs recursively,even if the dir exits, no error will be reposrted
    # auto backup the files
    date = datetime.date.today()
    filename = str(date) + '.tar.gz'
    tar_name = Path(target).joinpath(filename)
    # there is no command like touch,so we can use this mothod to create new file
    with tarfile.open(tar_name, "w:gz") as tar:
        tar.add(source)

    # auto cleanup the package
    datedelta = datetime.timedelta(days=delta)
    standard = date - datedelta
    for file in Path(target).glob('*.tar.gz'):
        past = datetime.date.fromisoformat(file.name.strip('.tar.gz'))
        if past < standard:
            os.remove(file)


sourdir = 'D:\Games\PCL'
tardir = 'D:\Mcbackups'
autobackup(sourdir, tardir, 2)

```