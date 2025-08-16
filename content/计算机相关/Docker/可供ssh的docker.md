# 非必选-多平台兼容

可以使用QEMU来实现x86机器上运行arm容器。主要参考[multiarch/qemu-user-static](https://github.com/multiarch/qemu-user-static)
1. `docker run --rm --privileged multiarch/qemu-user-static --reset -p yes`

# 拉取镜像
1. `docker pull ubuntu`
2. 运行容器 `docker run --name iubuntu -t -i -d -p 3316:22 ubuntu`
3. 进入容器shell docker exec -t -i iubuntu /bin/bash
# 更新源地址
1. 更新 `apt-get update`
2. 先下载nano `apt install nano`
2. 更新/etc/apt/sources.list。注意这里使用的是debian bullseye, 更多的可以去[清华大学镜像源]([清华大学开源软件镜像站 | Tsinghua Open Source Mirror](https://mirrors.tuna.tsinghua.edu.cn/))看看
```
deb http://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free
# deb-src http://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free

deb http://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free
# deb-src http://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free

deb http://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free
# deb-src http://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free
```
3. 更新 `apt-get update`
# 开启sshd
1. apt-get install -y openssh-client
2. apt-get install -y openssh-server
3. 修改密码 `passwd root`
4. 允许密码登入 `nano /etc/ssh/sshd_config` 修改`PermitRootLogin yes`
5. 重启一下docker 容器
6. 启动sshd`service ssh start`

# 安装python
1. 安装依赖 `apt install curl build-essential libncursesw5-dev libssl-dev libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev libffi-dev zlib1g-dev ccache`
2. `curl -O https://mirrors.huaweicloud.com/python/3.8.10/Python-3.8.10.tar.xz`
3. `tar -xvf Python-3.8.10.tar.xz`
4. `cd Python-3.8.10`
5. `./configure --prefix=/usr/local/python3`
6. `make && make install`
7. 建立连接 `ln -s /usr/local/python3/bin/python3.8 /usr/bin/python && ln -s /usr/local/python3/bin/pip3.8 /usr/bin/pip`
8. 设定一下国内源
```
pip config set global.index-url https://mirrors.aliyun.com/pypi/simple 

pip config set install.trusted-host mirrors.aliyun.com
```