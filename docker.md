# Mint19配置docker(也适用Ubuntu18.04)
（[中文参考](https://yeasy.gitbooks.io/docker_practice/install/mirror.html)
[英文参考](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce)）
## docker安装
```
sudo apt update
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository deb "[arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
sudo update

sudo systemctl enable docker
sudo systemctl start docker

sudo usermod -aG docker $USER
```
## docker镜像加速
在/etc/docker/daemon.json中写入
```
{
  "registry-mirrors": [
    "https://registry.docker-cn.com"
  ]
}
```

然后启动服务
```
sudo systemctl daemon-reload
sudo systemctl restart docker
```
## 镜像
### 获取、列出和下载
`docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]`<br>
默认地址是Docker Hub  仓库名为：&lt;用户名&gt;/&lt;软件名&gt; 对于Docker Hub，若不给出用户名，则默认为library,即官方镜像<br>
`docker image ls`<br>
列出镜像，参数有-f -a -q -format等<br>
`docker image rm [选项] <镜像1> [<镜像2> ...]`<br>
&lt;镜像&gt;可以是`<仓库名>:<标签>`，也可以是镜像ID，其中ID可以是短ID，即ID前几个字符，能用来区分其他镜像即可<br>
批量删除镜像
```
docker image rm $(docker image ls -q ubuntu) #删除所有仓库名为ubuntu的镜像
docker image rm $(docker image ls -q 0f before=mongo:3.2) #删除所有在mongo3.2之前的镜像
```
### 制作镜像
####使用commit制作镜像
方法是进入容器，执行你想做的操作，最后将容器通过`docker commit [选项] <容器> [<仓库名>[:<标签>]]`保存为镜像。但这种方法并不能记录你做了什么，所以不推荐。<br>
####使用Dockerfile定制镜像
即将所有的命令写入一个名为Dockerfile的文件中，每一条命令构建一层  

- FROM命令  
指定基础镜像
- RUN命令  
两种格式：**(由于一个命令一层，Union FS层数有限，所有应用&&连接shell指令，支持行尾添加\来换行，以及用#来注释，每一层构建的最后一定要清理无关文件，防止镜像臃肿)**  
`RUN shell命令`   
`RUN ["可执行文件", "参数1", "参数2"]`










