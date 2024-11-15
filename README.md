
# 背景


本地开发了一个SpringBoot项目，想通过Docker部署起来，我本地是Window10系统，由于某些原因不能虚拟化并且未安装Docker\-Desktop，所以我在想有没有办法本地不需要虚拟化也不需要安装Docker\-Desktop来实现支持Docker命令远程连接到我自己的服务器上。经过搜索以及大佬的指点发现了一个办法。那就是通过Docker客户端远程连接服务器的Docker服务端。


# 实现


## Docker客户端远程访问服务端


### 查看Docker服务端版本



```
docker version

```

[![](https://img2024.cnblogs.com/blog/1575518/202411/1575518-20241114172035306-987859966.png)](https://img2024.cnblogs.com/blog/1575518/202411/1575518-20241114172035306-987859966.png)


获取到Docker服务端版本为24\.0\.7。


### Docker服务端允许远程访问


修改docker.service开放远程访问。



```
# 编辑
vim /lib/systemd/system/docker.service

```

找到该文件中的



```
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock

```

注释或删除改行，替换为如下命令



```
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock

```

替换之后保存退出，然后重启Docker服务



```
systemctl daemon-reload && systemctl restart docker

```

在你本地通过浏览器访问`http://{服务器IP}:2375/version`，当看到页面显示一串JSON时表示已开放远程访问。


### 下载对应版本客户端


在Windows访问[https://download.docker.com/win/static/stable/x86\_64/](https://github.com)下载跟服务端版本一致的客户端压缩包。
[![](https://img2024.cnblogs.com/blog/1575518/202411/1575518-20241114172051197-1215757657.png)](https://img2024.cnblogs.com/blog/1575518/202411/1575518-20241114172051197-1215757657.png)


下载之后解压到指定文件夹，比如我放在`D:\\tools`下。在`D:\\tools`下会多出来一个名字为`docker`的文件夹，里面有如下图**docker.exe、dockerd.exe、docker\-proxy.exe**三个文件（docker\-compose.exe不用管，后面会讲）。


[![](https://img2024.cnblogs.com/blog/1575518/202411/1575518-20241114172106666-725117089.png)](https://img2024.cnblogs.com/blog/1575518/202411/1575518-20241114172106666-725117089.png)


### 配置环境变量


在Windows的系统环境变量中添加一个环境变量`DOCKER_HOST`，值配置为`tcp://{IP}:2375`，这个`IP`替换为Docker所在服务器的IP(例如我的`tcp://192.168.169.180:2375`)


然后再添加一个环境变量`DockerClient`，值配置为`D:\\tools\\docker`，也就是刚刚解压的目录，并且在Path中添加该变量(`%DockerClient%`)，配置该环境变量后可以在任何位置访问`docker.exe`可执行文件。


[![](https://img2024.cnblogs.com/blog/1575518/202411/1575518-20241114172119570-196497538.png)](https://img2024.cnblogs.com/blog/1575518/202411/1575518-20241114172119570-196497538.png)


### 验证


打开CMD,在任意文件夹下执行`docker ps`查看是否显示服务器上的容器。


## Docker\-Compose实现同样功能


### 查看服务端docker\-compose版本



```
docker-compose version

# Docker Compose version v2.29.2

```

### 下载相同版本的docker\-compose


访问[https://github.com/docker/compose/releases](https://github.com):[FlowerCloud机场](https://hanlianfangzhi.com)下载对应版本的docker\-compose。
[![](https://img2024.cnblogs.com/blog/1575518/202411/1575518-20241114172131237-1957205935.png)](https://img2024.cnblogs.com/blog/1575518/202411/1575518-20241114172131237-1957205935.png)


下载后存放到docker客户端所在的目录`D:\\tools\\docker`下，就是在上面看到的`docker-compose.exe`(文件名称是自己改的，下载下来就是上图的名称)。


### 验证


运行命令查看是否生效。



```
docker-compose ps

```


```
注意：运行docker-compose命令所在的文件夹的名称需要注意，不能随便乱取名。我的情况是需要跟服务器上的当前文件夹名称保持一致。我的服务器上docker-compose.yml放在/usr/looveh/tw-feedback下，所以在Windows下执行docker-compose命令时当前目录的名称需要为tw-feedback，否则查询不到容器。

```

\_\_EOF\_\_

![](https://github.com/lvbok/p/18546439.html)Looveh 本文链接：[https://github.com/lvbok/p/18546439\.html](https://github.com)关于博主：评论和私信会在第一时间回复。或者[直接私信](https://github.com)我。版权声明：本博客所有文章除特别声明外，均采用 [BY\-NC\-SA](https://github.com "BY-NC-SA") 许可协议。转载请注明出处！声援博主：如果您觉得文章对您有帮助，可以点击文章右下角**【[推荐](javascript:void(0);)】**一下。您的鼓励是博主的最大动力！
