重点在于配置ssh的config, 解决步骤如下:
1. 不确定是否必要, 删除所有ssh-key, `ssh-add -D`
2. 重启ssh-agent: `taskkill /F /IM ssh-agent.exe /T`和`Start-Service ssh-agent`
3. 配置config
```
Host yundongGit

  HostName 182.92.229.114

  Port 7021

  User git

  IdentityFile C:\\Users\\zhangsan\\.ssh\\yundong_rsa
```
4. clone, 原来的命令为`git clone ssh://git@182.92.229.114:7021/mi/rokec.git`修改为``git clone ssh://git@yundongGit/mi/rokec.git`