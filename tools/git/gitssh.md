今天在push到远程仓库的时候,报错:
```
qing@QB:~/桌面/quic$ git push origin master 
Username for 'https://github.com': haichuangxia
Password for 'https://haichuangxia@github.com': 
remote: Support for password authentication was removed on August 13, 2021.
remote: Please see https://docs.github.com/en/get-started/getting-started-with-git/about-remote-repositories#cloning-with-https-urls for information on currently recommended modes of authentication.
fatal: Authentication failed for 'https://github.com/haichuangxia/quic.git/'
```
需要手动的输入用户名和密码,用户名是github的登录名,一般是完整的邮箱地址.密码并不是github的登录密码,而是github的personal access token.


这个还是挺麻烦的,还是使用ssh连接比较方便.

通过ssh看是否可以访问:
```
qing@QB:~$ ssh -T git@github.com
Hi haichuangxia! You've successfully authenticated, but GitHub does not provide shell access.
```

报错无法使用进行访问,因此手动将原来的https访问换成ssh访问.

通过`git remote set-url <remote branch name like:origin> git@github.com:haichuangxia/XXX.git`
设置之后再进行push就可以了.

