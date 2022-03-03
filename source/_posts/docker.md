---
title: "Docker 学习笔记"
date: 2022-02-14 17:31:40
tags: ["docker"]
---


## 啥是 Docker
作为 IT 从业人员，想必你对干净的软件系统有着强迫症般的追求。干净往往意味着安全和放心。Docker 是这类强迫症患者的福音。我有一种总是喜欢装新系统的冲动。新系统意味着可以从头再来，意味着你不用再忍受之前那个老系统上不知道怎么折腾出来的某个问题（比如，你的浏览器首页被流氓软件劫持了）。新生儿总比那昏昏欲睡的老者更能让人感觉到希望。然而装系统是一件很费时间的事情。将一个干净的系统打理至符合你各种工作需求的状态，这也是很让人头疼的事。

我想要确保软件系统是干净，同时又希望软件系统一装好，我就能拿它来干活。Docker 就是能呼应你内心呼声的工具。

<!-- more -->

## 安装 Docker
详见：https://www.runoob.com/docker/ubuntu-docker-install.html

安装完 Docker 后，用 `sudo docker info` 这行命令检测 Docker 是否已安装成功。这行命令打印出来的信息如下：

+ 所有的容器和镜像的数量
+ Docker 的基本配置
+ Docker 使用的执行驱动和存储驱动
+ ...

## 镜像管理

先来看下本地上是否已经存在镜像。`sudo docker images` 会列出本地已存在的镜像列表。执行完命令后，在我的电脑上打印出了以下信息：

``` bash
REPOSITORY        TAG         IMAGE ID       CREATED        SIZE
jenkins/jenkins   lts-jdk11   619aabbe0502   4 months ago   441MB
```
一共出现了 5 个字段的信息，分别是镜像所在的镜像仓库，镜像的标签，镜像ID，镜像创建时间和镜像大小。

这个镜像是从仓库下载下来的。镜像保存在仓库中，而仓库存在于Registry中。默认的 Registry 是由 Docker 公司运营的公共Registry服务，即Docker Hub。

镜像存放在镜像仓库里，在一个镜像仓库里，存在多个不同版本的镜像。不同版本的镜像通过标签来区分。如在 ubuntu 仓库里，存在 `ubuntu:14.04` 与 `ubuntu:latest` 这两个 ubuntu 镜像，它们的标签分别是 14.04 和 latest。在不提供标签的情况，Docker 会指定默认标签 latest。`ubuntu` 和 `ubuntu:latest` 是指同一个版本。

从上面打印的信息中可以看到，目前本地只有一个 jenkins 镜像，其标签是 lts-jdk11。完整的解释是：这个镜像名是 jenkins/jennkins:lts-jdk11，这个镜像所属仓库的仓库名是jenkins，这个 jenkins 仓库的所有者是 jenkins 这个用户。

这个镜像是在之前通过 `sudo docker pull jenkins/jenkins:lts-jdk11` 这行命令下载下来的。`docker pull` 命令用于从镜像仓库中下载镜像。由于在镜像仓库中存在多个版本的镜像，我们一般还需要指定镜像的标签（如lts-jdk11)来下载特定的版本。必须要提供完整的镜像名称才能下载指定的镜像。

现在可以下几个ubuntu镜像来练练手。

``` bash
sudo docker pull ubuntu:12.04
sudo docker pull ubuntu:precise  # 注意此时的下载过程
sudo docker pull ubuntu
```
下载完后，执行 `sudo docker images` 看下镜像列表：

``` bash
REPOSITORY        TAG         IMAGE ID       CREATED        SIZE
ubuntu            latest      ba6acccedd29   2 months ago   72.8MB
jenkins/jenkins   lts-jdk11   619aabbe0502   4 months ago   441MB
ubuntu            12.04       5b117edd0b76   4 years ago    104MB
ubuntu            precise     5b117edd0b76   4 years ago    104MB
```
仔细观察上面的列表，可以发现，`ubuntu:12.04` 和 `ubuntu:precise` 这两个不同标签的 ubuntu 镜像的 IMAGE ID 是一样。这说明，这两个镜像其实是同一个镜像。这是因为一个镜像可以同时有多个标签。

回顾刚才 3 个镜像的下载过程，我们会发现在下载 `ubuntu:precise` 时的日志是不一样的：

``` bash
root@Swan:~# docker pull ubuntu:12.04  # 下载 ubuntu:12.04 的命令，下面是日志
12.04: Pulling from library/ubuntu
d8868e50ac4c: Pull complete
83251ac64627: Pull complete
589bba2f1b36: Pull complete
d62ecaceda39: Pull complete
6d93b41cfc6b: Pull complete
Digest: sha256:18305429afa14ea462f810146ba44d4363ae76e4c8dfc38288cf73aa07485005
Status: Downloaded newer image for ubuntu:12.04
docker.io/library/ubuntu:12.04

root@Swan:~# sudo docker pull ubuntu:precise  # 下载 ubuntu:precise 的命令，下面是日志
precise: Pulling from library/ubuntu
Digest: sha256:18305429afa14ea462f810146ba44d4363ae76e4c8dfc38288cf73aa07485005
Status: Downloaded newer image for ubuntu:precise
docker.io/library/ubuntu:precise
```
在下载 `ubuntu:12.04` 时，有个 pull 的过程，直到 pull complete。在 12.04 下载完毕后，下载 precise 时，几乎是瞬间就完成了下载。此时其实没有下载，而是复用了之前的下载。
同一个镜像版本可以有多个标签，上面的12.04 和 precise 其实是同一个 Ubuntu 发布版的版本号和代号。这就像一个人有姓名和花名一样。

至此，我们使用了两个仓库的镜像：jenkins/jenkins 和 ubuntu。我们会发现它们仓库名不一样。

这是因为 Docker Hub 中有两种类型的仓库：用户仓库（user repository）和顶层仓库（top-level repository），它们的命名规则不一样。

用户仓库的命名由用户名和仓库名两部分组成，所以可以发现，jenkins/jenkins 是一个用户仓库，用户名和仓库名都是 jenkins。而顶层仓库只包含仓库名部分，如 ubuntu 仓库。

用户仓库的镜像都是由 Docker 用户创建的，而顶层仓库则是由 Docker 内部的人来管理的。

前面讨论了如何查看镜像和下载镜像以及镜像仓库/镜像标签等概念。目前所涉及的镜像都是别人提供好的，下面开始学习如何创建自己的镜像。

### Dockerfile 文件及其指令

构建镜像的方式有两种：
+ 使用 `docker commit` 命令
+ 使用 `docker built` 命令和 Dockerfile 文件

在这里只介绍第二种构建方式，第一种方式可自行了解。

这里是一个 Dockerfile 文件示例。基于这个文件，我们便能构建一个镜像。

``` bash
# Dockerfile 文件的内容
FROM ubuntu
MAINTAINER Jialiang Liu "liu.jialiang@foxmail.com"
RUN apt update && apt install -y nginx
RUN echo 'Hi, I am in your container' > /usr/share/nginx/html/index.html
EXPOSE 80
```

镜像的构建需要一个构建上下文（构建环境），所谓的构建环境，其实就是指 Dockerfile 文件所在的文件夹。新建一个 static_web 目录，将上面的内容复制到名为 Dockerfile 的文件中。Docker 会在构建镜像时将构建上下文和该上下文中的文件和目录上传到 Docker 守护进程。

确保终端中里显示的路径是在 `static_web` 目录下，即我们是处于构建上下文中，然后执行 `sudo docker build -t="toad/static_web" .` 来构建镜像。`docker build` 是 Docker 构建镜像的命令，`-t` 用于指定镜像的仓库名(toad)和镜像名(static_web)。命令中最后的 `.` 表示在当前目录下查找 Dockerfile 文件。

执行 `sudo docker build --help` 可以查看这个命令的用法：基于位于 PATH/URL 的 Dockerfile 文件构建一个镜像。

``` bash
Usage:  docker build [OPTIONS] PATH | URL | -

Build an image from a Dockerfile
```

`docker build` 的其它用法如下：

``` bash
# 从当前目录下（PATH）查找 Dockerfile，并以此文件构建一个 toad/static_web:v1 镜像
sudo docker build -t="toad/static_web:v1" .

# 从 github 仓库中(URL)查找 Dockerfile，并以此文件构建一个 toad/static_web:v1 镜像
sudo docker build -t="toad/static_web:v1" git@github.com:toad/docker-static_web
```

Dockerfile 文件是由 Dockerfile 指令编写的。所有的 Dockerfile 指令必须是大写字母。熟悉每个指令的用法，才能写好 Dockerfile 文件，进而创建一个镜像。在上面的示例中，使用了 `FROM` `MAINTAINER` `RUN` `EXPOSE` 等指令，下面来系统地学习这些指令。

#### FROM 指令

FROM 指令用于指定当前要构建镜像的父镜像（基础镜像）。一般情况下，构建新镜像都是基于已存在的镜像。`FROM ubuntu` 表示使用最新的 ubuntu 镜像来构建镜像。`FROM ubuntu:14.04` 表示使用 14.04 这个版本的 ubuntu 镜像来构建新镜像。

#### MAINTAINER 指令

MAINTAIN 指令用于指定镜像的创建者的一些信息，如姓名和电子邮件。

#### RUN 指令

RUN 指令指定一些在构建镜像时要执行的命令。这里的命令跟你在 linux 终端下输入的命令没啥区别。命令的写法有两种格式：
+ shell 终端格式
+ 数组格式

shell 终端格式是指，你在 shell 终端中输入什么命令，在 RUN 时就输入什么命令。这里的命令将交给命令包装器 /bin/bash -c 来执行。

数组格式是指使用一个数组来指定要运行的命令和传递给该命令的每个参数。

上文中 `RUN apt install -y nginx` 是 shell 终端格式的写法，同样可以写成数组格式的写法：`RUN [apt", "install", "-y", "nginx"]`

#### CMD 指令

CMD 指令指定一些在生成容器后要执行的命令。虽然这是一个在构建镜像时就写好的指令，但是在整个构建镜像的过程中并未使用它。RUN 指令和 CMD 指令都是写在 Dockerfile 文件中，用于指定要执行的命令。RUN 指令指定的命令构建镜像时运行，CMD 指令指定的命令在启动容器时运行。CMD 指令同样支持两种命令的写法。

在 `docker run` 启动容器时指定的命令会覆盖 CMD 指令指定的命令。

#### ENTRYPOINT 指令
ENTRYPOINT 指令指定一些在生成容器后要执行的命令。与 CMD 指令不同的是，ENTRYPOINT 指定的命令不会被 `docker run` 所覆盖，它还会接收 `docker run` 中指定的命令，将这些命令作为自己的参数。ENTRYPOINT 指定的命令总会被执行。假设有个 Dockerfile 的部分内容如下：

``` bash
ENTRYPOINT ["python"]
CMD ['start_test1.py']
```

执行 `sudo docker run -it ubuntu` 生成一个容器后，该容器会执行 `python start_test1.py`。ENTRYPOINT 和 CMD 中的命令拼接在一起执行。

执行 `sudo docker run -it ubuntu 'start_test2.py'` 生成一个容器后，该容器会执行 `python start_test2.py`。ENTRYPOINT 和 `docker run` 中的命令拼接在一起执行，CMD 中的命令被 `Dokcer run` 覆盖，`docker run` 中的命令作为 ENTRYPOINT 指定命令的参数。

在执行 `docker run` 时，可以指定 `--entrypoint` 标志覆盖 ENTRYPOINT 指令。

#### WORKDIR 指令

WORKDIR 指令用来在从镜像创建一个新容器时，在容器内部设置一个工作目录，ENTRYPOINT 和 / 或 CMD 指定的程序会在这个目录下执行。

我们可以使用该指令为 Dockerfile 中后续的一系列指令设置工作目录，也可以为最终的容器设置工作目录。

``` bash
FROM ubuntu
MAINTAINER Jialiang Liu "liu.jialiang@foxmail.com"
RUN ["apt", "update"]
WORKDIR /opt
RUN mkdir test
WORKDIR /opt/test
CMD pwd
```
解释一下这里的 Dockerfile 指令。这个镜像基于 ubuntu 基础镜像构建，然后执行 `apt update` 检查更新。接下来，将工作目录设置为/opt，在这个目录下执行新建 test 文件夹的命令。随后将新建的 /opt/test 目录作为工作目录，在 CMD 指令未被 `docker run` 覆盖时，通过 pwd 命令打印出当前工作目录/opt/test。

这样一来，基于这个镜像的构建的所有容器都会在 /opt 目录下新建一个 test 文件夹，一进入容器的终端，终端显示的工作目录就是 `root@36423953e055:/opt/test#`。

假设基于此 Dockerfile 文件构建的镜像名为 `test/workdir`。执行 `sudo docker run test/workdir` 后会生成一个容器，由于 `docker run` 未指定要运行的命令，会执行 CMD 指定的命令，将 `/opt/test` 打印出来。执行 `sudo docker run -it test/workdir /bin/bash` 后会生成一个容器，并进入容器的终端，由于 WORKDIR 指定了容器的最终工作目录为 /opt/test，终端的工作目录不是用户主目录，而是 WORKDIR 指定的 /opt/test。

在执行 `docker run` 时，可以指定 `-w` 标志覆盖 WORKDIR 指令。如 `sudo docker run -it -w /var/log ubuntu pwd`。`-w` 指定了容器的工作目录位/var/log，`pwd`会打印出 `/var/log`。

#### ENV 指令

ENV 指令用来在镜像构建过程中设置环境变量。执行命令时有一个命令查找的过程，如果在当前目录下未找到该命令，就会去环境变量中查找该命令。在 ubuntu 终端中，可以通过 `echo $PATH` 去看当前所有的环境变量。在执行 RUN 指令时，会将 ENV 指定的变量加到命令的查找范围中。

``` bash
ENV RVM_PATH /home/rvm/
RUN gem install unicorn
```

EVN 定义了一个环境变量 `RVM_PATH`，其值时 `/home/rvm/`。通过 RUN 执行 `gem install unicorn` 时，会去 `/home/rvm/` 目录查找 gem 文件是否存在。

ENV 定义的变量可以被其它指令引用。

``` bash
ENV TARGET_DIR /opt/app
WORKDIR $TARGET_DIR
```
EVN 定义了一个环境变量 `TARGET_DIR`，其值时 `/opt/app`。WORKDIR 指令引用了这个变量，容器的工作目录变为 `/opt/app`。

在容器中，可以使用 env 命令去查看容器的环境变量。

在使用 `docker run` 时可以指定 `-e` 标志来传递环境变量。这些变量只在运行时有效。

``` bash
sudo docker run -it -e "WEB_PORT=8080" ubuntu env
```

#### USER 指令

USER 指令会指定该镜像会以什么样的用户去运行。在不指定 USER 的情况下，默认用户为 root。我们可以指定用户名或UID以及组或GID，甚至是两者的组合。

``` bash
USER user
USER user:group
USER uid
USER uid:gid
USER user:gid
USER uid:group
```

#### VOLUME 指令

VOLUME 指令用来向基于镜像创建的容器添加卷。

假设存在一个下载文件的容器。我们知道，容器与宿主机之间是隔离的，我们无法从宿主机直接访问到容器里的文件，也就是说，我们无法直接观看下载到容器里的视频。而且容器一旦被删除了，容器内所有下载的文件也会消失。卷就是用于处理这些问题的技术，它是容器里的一个目录，是一个将宿主机中的目录挂载到容器中的挂载点，是容器与宿主机间数据访问的桥梁。通过卷，我们可以将容器下载的文件保存到宿主机中，也可以让容器访问宿主机中的文件。

在官方仓库里，有一个 `linuxserver/qbittorrent` 镜像仓库。其 `docker run` 命令中 有两行是与卷相关的参数:

``` bash
 -v /path/to/appdata/config:/config
 -v /path/to/downloads:/downloads 
 ```
 
 `/path/to/downloads` 表示你宿主机的下载目录，`/downloads` 表示容器中的下载目录。
 当我们在宿主机访问 `/path/to/downloads` 目录时，其实也是访问容器的 `/downloads` 目录。
 
 在 Dockerfile 文件中，加入一行 `VOLUME ["/downloads"]` 即可创建容器的卷，通过 `docker run` 指定 `-v` 标志即可将宿主机的某个目录挂载到容器的卷上。创建多个卷的方式是这样：`VOLUME ["/config", "/downloads"]`

#### ADD 指令 和 COPY 指令

ADD 指令用来将本地或者网络上的文件和目录复制到镜像中.下面是一些例子：

``` bash
# ADD 使用格式：ADD 源文件/目录，目标文件/目录

# 将构建目录下的 software.lic 复制到容器中的 /opt/application/ 目录下
ADD software.lic /opt/application/software.lic  

# 将构建目录下的所有文件复制到容器中的 /opt/application/ 目录下
ADD . /opt/application/

# 文件源可以使用 URL 的格式，ADD 同样支持 exec 数组格式的写法。
ADD ["http://wordpress.org/latest.zip", "/root/wordpress.zip"]

# ADD 指令还能解压打包文件到镜像中的指定目录，目标目录不存在，会自动创建
ADD /downloads/latest.tar.gz /var/www/wordpress/
```

COPY 指令用来将构建环境下的文件和目录复制到镜像中。构建环境是指 Dockerfile 文件所在的目录。COPY 只能复制 Dockerfile 目录下的文件或子目录，这是它与 ADD 的最大区别。


## 容器管理

### 容器是啥

在面向对象编程的语言中，对象是类的实例，类是成千上万个对象的生产工厂。容器与镜像的关系与之相似。在创建一个镜像后，可以基于这个镜像创建许多容器。基于同一个镜像创建出来的容器肯定是一模一样的，容器创建完可以根据需要给容器增加需要的软件，此时，容器便不再是同个模样了。

容器的生命周期包含以下几个阶段：创建、启动、停止和销毁。

### docker create

在命令行中，输入 `sudo docker create --help` 可查看 `create` 命令的信息。下面列出了部分信息。

``` bash
Usage:  docker create [OPTIONS] IMAGE [COMMAND] [ARG...]

Create a new container
```
从打印出来的信息中，可以很清楚看到命令使用的格式和其功能。`create` 基于 `IMAGE`（镜像）创建一个新的容器，其他参数都是可选的。

下面是一个创建容器的示例。

``` bash
eg：sudo docker create -it ubuntu /bin/bash
```

下面解释一下这行命令。

`docker create` 表示我们将要创建一个容器，`-it` 这里是两个参数的连体写法，也可写成 `-i -t`。

还是通过 `sudo docker create --help` 去查看 `-i` 的信息：`-i, --interactive Keep STDIN open even if not attached
`。意思是 `-i` 标志保证容器中 STDIN 是开启的，尽管我们没有附着到容器。其中 `--interactive`  `-i` 标志的完整写法。STDIN 即标准输入，用通俗的话来说，就是我们的键盘，标准输入不仅仅只有键盘。有个这个`-i`标志，在处于容器时，容器才能接收我们键盘打的字。

`-t` 在 `sudo docker create --help` 中的解释是：`-t, --tty Allocate a pseudo-TTY`。翻译过来就是：分配一个伪终端。终端是基本的接收键盘输入，输出打印信息的程序。`-i` 让键盘可输入，因此，我们能向终端输入信息，然后等待程序的响应，最终，终端能将机器返回的信息打印到屏幕上，至此一个完整的交互便完成了。`-it` 标志一般都是同时出现的，只有才能完成交互。当然，在创建容器，这两个标志是可选的，这意味着，并不是所有的容器，我们都是需要与之交互的。

`ubuntu` 是一个镜像的名称。镜像可以是镜像仓库里现有的，也可以是我们自己创建的。这里存在一个镜像查找的过程。首先，Docker 会检查本地是否已存在这个镜像，如果本地没有这个镜像，就会去网上下载官方提供的镜像。如果还是没有，就会报错。

`/bin/bash` 表明我们在容器启动后要打开 `bash` 这个终端程序。当启动容器后，我们就处于 `bash` 这个终端程序的界面上。这就像我们 Linux 系统上的打开终端一样，没啥区别，只是这个终端是在容器内的。注意，`create` 命令并不会启动容器，在创建完容器后，bash 终端不会运行，因为容器还没运行呢。

`docker ps` 命令用于查看已存在的容器信息。常见用法如下：

``` bash
sudo docker ps  # 用于查看正在运行的容器
sudo docker ps -a  # 用于查看所有的容器，包括没有允许的容器
sudo docker ps -aq  # 查看所有容器的ID
sudo docker ps -l  # 查看最后一个运行的容器
sudo docker ps -n x  # 查看最后 x 个容器
```

 `docker create` 只有创建容器的功能，并不能启动容器。`create`后，容器未运行，我们得用 `sudo docker ps -a` 来查看容器信息。
 
 ``` bash
CONTAINER ID  IMAGE     COMMAND        CREATED          STATUS   PORTS  NAMES
6bae632dc76e  ubuntu    "/bin/bash"    34 seconds ago   Created         laughing_stonebraker    
 ```
这里有 7 个字段的信息，分别是：容器标识、创建该容器的镜像、容器最后执行的命令、创建时间、容器状态，容器对外的端口号和容器名称。其中 `Names` 的字段下，我们这个容器的名字很奇怪，不知其意义是什么。这是因为我们在创建容器时，并没有给这个容器命名，这个名字是 Docker 随机取的，当然就没有上下文的语义了。在创建容器时，最好给它具有标识意义的名字。`--name` 这个标志便可给容器命名。

``` bash
sudo docker create -it --name=learn_docker_create ubuntu /bin/bash  # 给容器取名为：learn_docker_create
```

### docker run
在命令行中，输入 `sudo docker run --help` 可查看 `run` 命令的信息。下面列出了部分信息。

``` bash
Usage:  docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

Run a command in a new container
```

与 `create` 的解释不同，`run` 的解释是：在一个新容器里运行一个命令。它的功能比 `create` 强大：它在创建一个新容器后，还能启动并进入该容器运行某些命令。与 `create` 的例子很相似，`run` 的例子如下：

``` bash
sudo docker run -it --name=learn_docker_run ubuntu /bin/bash
```

这行命令的解释如下：创建一个基于 ubuntu 镜像的，可与之交互的，名字为 `learn_docker_run` 的容器，容器创建完毕后，启动容器，运行容器中的 `/bin/bash` 命令。假设你现在是在 ubuntu 宿主机上运行这行命令，会发现终端的用户名和主机已经发生了变化。此时你的终端已经不是 ubuntu 的了，而是容器的终端。

前面提到，并不是所有的容器，我们都是需要与之交互的。我们可以创建一个在后台长期运行而不与之交互的守护式容器。我们要用 `-d` 标志来实现。

``` bash
sudo docker run --name=daemon_dave -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

这里添加了个 `-d` 标志创建了一个基于 ubuntu 镜像的守护式容器，由于并不需要与容器交互，只需要容器处于运行状态，这里并没有 `-it` 标志。这个容器在后台启动运行后只做一件事：每隔一秒中打印一次 `hello world`。

执行完这行命令后，并没有跟之前一样进入容器并打开容器的 bash 终端，只是返回一个容器ID。此时，容器在后台运行。

容器此时在后台做什么呢？ `docker logs` 命令可以获取容器的日志。

``` bash
sudo docker logs daemon_dave  # 查看刚才创建的容器 daemon_dave 在后台的运行日志。
sudo docker logs -f daemon_dave  # 动态查看刚才创建的容器 daemon_dave 在后台的运行日志。
sudo docker logs -ft daemon_dave  # 动态查看刚才创建的容器 daemon_dave 在后台的运行日志，并加上打印日志时的时间。
sudo docker logs --tail 10 daemon_dave  # 查看日志的最后10行内容。
sudo docker logs --tail 0 -f daemon_dave  # 不读取整个文件，动态查看最新的日志。
```

回到对 `docker run` 的讨论。容器在运行过程中会因为某些原因导致停止运行，比如：宿主机关机。在宿主机开机后，我们想要让容器自动启动，要怎么做？在创建这个容器时，加上 `--restart=always` 即可。

``` bash
# 不管容器因为何种原因导致停止运行，都会尝试自动重启
docker run --restart=always --name=daemon_dave -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"

# 容器最多自动重启 5 次
docker run --restart=on-failure:5 --name=daemon_dave -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

`--restart` 是一种自动重启机制，用于处理容器异常停止的情况。那么，正常情况下，容器的启动与停止是怎么操作的呢？

正常情况下，用 `docker stop` 停止容器，用 `docker start` 或 `docker restart` 启动容器。

``` bash
sudo docker stop daemon_dave  # 停止名为 daemon_dave 的容器。
sudo docker start daemon_dave  # 启动名为 daemon_dave 的容器。
```

Docker 重新启动时，会沿用返回容器的名称，表明容器已经启动。将上面的 daemon_dave 停止运行再次启动后，依然会每隔 1 秒打印日志。除了 `docker logs` 方式查看日志外，我们也可以用 `docker attach` 命令附着到运行中的容器上。 

``` bash
sudo docker attach daemon_dave  
```
当我们执行完这条命令后，`hello world` 会以每秒 1 次的频率被打印出来。如果，我们此时按 ctrl + c，会导致容器停止运行。`docker attach` 无法对停止的容器起作用。

在前面的示例中，我们创建过名为 `learn_docker_run` 的容器。

``` bash
sudo docker run -it --name=learn_docker_run ubuntu /bin/bash
```

我们通过 `sudo docker ps -a` 来看下其状态，发现其状态是 `Exited (130) About a minute ago ` 的停止运行状态。我们将其 `sudo docker start learn_docker_run` 后，终端打印出容器名称：`learn_docker_run`。此时终端仍然是宿主机的终端，我们如果要去容器的终端，需要 `attach` 到容器上。执行完 `sudo docker attach learn_docker_run` 后，将会附着到容器上，此时终端变成容器的终端，会发现终端的用户名和主机名发生了变化。

至此，我们来总结下 `create`、`run`、`start`、`attach` 命令。

``` bash
sudo docker create -it --name=test_docker_command ubuntu /bin/bash  # 创建一个名为 test_docker_command 的容器
sudo docker start test_docker_command  # 启动这个容器
sudo docke attach test_docker_command  # 附着到这个容器，然后进入容器的终端

# 上面这三条命令等价于下面这一条命令：
sudo docker run -it --name=test_docker_command ubuntu /bin/bash  # 创建并启动容器，然后进入容器的终端

```

通过 `docker attach` 进入容器后会沿用 `docker run` 命令时指定的参数来运行。`docker run` 如果是创建一个 bash 终端，`attach` 进入容器后就会进入这个终端，如果是创建一个打印日志的进程，`attach`进入容器后就会打印日志。我们新创建一个容器来验证这个说法。

通过 `sudo docker run -it --name=test_docker_command ubuntu /bin/bash` 运行这个容器后，我们新打开宿主机的终端，在宿主机终端下通过 `sudo docker attach test_docker_command` 附着到容器后，执行 `ps  -ef` 命令会发现两个终端窗口的内容是同步打印的，而且 `/bin/bash` 所占用的进程 ID 是同一个。这说明了 `attach` 命令并不是在容器中新开了一个进程，而是复用容器已有的进程。这是需要注意的 `attach` 的特性，这是与 `docker exec` 最大的区别。

下面开始学习 `docker exec`。与 `docker attach` 一样，`docker exec` 也是一个从宿主机进入容器的命令，但是它会在容器中新开一个进程。还是用上面的 `test_docker_command` 容器来验证。我们在宿主机再开一个终端，注意此时已经有三个终端，有两个终端是容器的，一个是宿主机的。我们执行以下命令，从宿主机的终端进入容器的终端。

``` bash
sudo docker exec -it test_docker_command /bin/bash
```
执行完命令后，我们已经处于容器的 bash 终端了，在这个终端下，执行 `ps -ef` 会打印如下的信息：

``` bash
UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 10:48 pts/0    00:00:00 /bin/bash
root          18       0  0 11:07 pts/1    00:00:00 /bin/bash
root          27      18  0 11:07 pts/1    00:00:00 ps -ef
```

看到没，此时容器出现了两个 bash 进程，PID 为 18 的进程是由 `docker exec` 创建的。在第三个终端下，打印 `ps -ef` 时没有出现日志同步出现的情况，这也说明了第三个终端是一个新的进程，与前面两个终端（对于宿主机来说，是两个进程；对于容器来说，是同一个进程）不一样。

`docker exec` 的应用场景一般是在一个守护式容器中新开辟一个进程来执行其他操作。下面是一个示例：

``` bash
# 创建一个在后台每隔 1 秒打印日志的容器
sudo docker run -d --name=learn_docker_exec ubuntu /bin/sh -c  "while true; do echo hello world; sleep 1; done"

# 进入这个容器并新建一个 bash 终端进程，通过这个终端，我们可以在这个守护式容器内做一些其他工作。
sudo docker exec -it learn_docker_exec /bin/bash
```

至此，容器的创建、启动和停止都讨论到了，还剩下一个销毁的操作。`docker rm` 为 Docker 提供了删除容器的操作。

``` bash
sudo docker rm 容器名/容器ID  # 根据容器名/容器ID删除处于停止状态的容器
sudo docker rm -f 容器名/容器ID  # 根据容器名/容器ID强制删除容器，包括运行中的容器
sudo docker rm `sudo docker ps -aq` 删除所有处于停止状态的容器
```

## 网络管理
每一个容器都是一个独立的、相互隔离的系统，可以类比为机房里的每一个实体服务器，不同的实体机器间互不干扰（容器是软件层面上的互不干扰）。每一个服务器都是一个孤岛，少了网线来连接彼此，服务器便是单机的，毫无意义。容器因其相互隔离的特性而存在，但是少了容器之间的通信，容器能发挥的作用也是有限的。凡事都不可极端，孤岛是不存在的。Docker 提供了网络管理的机制，用于将各个容器连接起来。

电脑间通过 Internet 相互通信，容器间也可以通过网络来连接到一起。使用 `sudo docker network create app` 这行命令就可以创建一个名为 app 的网络。所有基于这个网络创建的容器可以互相通信。

使用 `sudo docker network --help` 查看其用法:

``` bash
Manage networks

Commands:
  connect     Connect a container to a network
  create      Create a network
  disconnect  Disconnect a container from a network
  inspect     Display detailed information on one or more networks
  ls          List networks
  prune       Remove all unused networks
  rm          Remove one or more networks
```
Docker 可以对网络进行增删查的操作。

``` bash
sudo docker network create app  # 创建一个名为 app 的网络
sudo docker network ls  # 查看所有的网络列表
sudo docker network inspect app  # 查看 app 网络的详细信息
sudo docker network rm app  # 移除名为 app 的网络
sudo docker network prune  # 移除所有未被使用的网络
```
创建一个网络后，怎样基于这个网络来创建一个容器呢？
``` bash
sudo docker run -it --network=app --name=test1 ubuntu /bin/bash
```
这里使用 `--network` 参数来给容器指定一个网络。

再新开一个终端，输入以下命令：
``` bash
sudo docker run -it --network=app --name=test2 ubuntu /bin/bash
```
然后在 test2 容器终端里输入：`sudo apt update && apt install iputils-ping`
最后执行 ping 命令 `ping test1`，查看是否能够正常访问容器 test1。

然后新开一个宿主机的终端，执行 `sudo docker run -it --name=unreachable ubuntu /bin/bash` 新建一个非 app 网络的容器，然后看这个容器是否能够访问容器test1。容器 reachable 是无法访问容器 test1 的。