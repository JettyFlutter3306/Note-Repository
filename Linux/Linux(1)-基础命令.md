# Linux(1)

# 一. 简介

## 1.1 Why Linux

Linux 适合作为服务器操作系统，方便部署开发的服务器软件。

## 1.2 常见的Linux

Linux 不是一个具体的操作系统，而是一类操作系统的总称。具体版本称为发行版，常见的版本有：

- Red Hat：目前被 IBM 收购，收费版，目前全球最大的 Linux 供应商。
- Cent OS：Red Hat推出的免费版。
- Ubantu：界面比较友好。

## 1.3 安装

去 VMWare 官网下载虚拟机软件，然后再下载一个 Cent OS 的 ISO 镜像文件，按照教程一步一步安装即可，或者直接去阿里云、腾讯云购置一台轻量级应用服务器，直接免去安装。

# 二. 目录结构

## 2.1 常见目录

Linux 系统目录有常见的如下结构：

- **/**：根目录（Unix 和 Linux 中没有盘符的概念）一个硬盘一个根。
- **/bin**：系统的常用命令目录。包括控制台命令、系统可执行文件、系统的核心二进制文件等。
- **/etc**：发布目录。保存系统中所有的核心内容，要求控制权限高，建议不要随便读写。
- **/usr**：用户目录。相当于 Windows 系统中的 program files 目录。常用于安装系统所有用户共用的软件、资源的。
- **/root** 或 **~**：root 根用户的用户目录，相当于 windows 系统中的 *c:/users/administrator* 目录。
- **/home**：保存其他用户主目录的目录。如：*/home/Frank*。
- **/var**：系统运行过程的数据目录。

## 2.2 路径术语

1. 全路径：

从 filesystem 根目录开始寻找文件(绝对路径)，以 **/** 开头。如：找 usr下 local 中的内容。写法为 **/usr/local**。

2. 相对路径：

从当前资料路径开始寻找，找到其他资源路径的过程。找当前资源的子目录直接写子目录名称，找上一级文件写法为 **../**。

3. 特殊路径：

**/** 表示磁盘根，**~** 表示 **/root**(root 用户的根目录)，root 文件夹的根目录。

# 三. 基础命令

## 3.1 操作文件

- **pwd**：print working directory ，输出当前工作目录，光标所有位置的目录(绝对路径)。
- **cd**：change directory，切换目录，`.` 表示当前目录，`..` 表示上一级目录(父目录)。

```shell
cd ./a  # 切换到当前a目录
cd ../temp  # 切换到上级目录的temp目录
cd ~  # 回到root目录
```

- **ls**：List，列表目录中的内容。默认显示当前目录下的文件列表。

```shell
ls -l  # 展示目录下所有文件及目录的权限和修改日期
```

- **clear**：清空屏幕。
- **touch**：创建空白文件。

```shell
touch HellowWorld.java  # 创建HelloWorld.java文件
```

- **cat**：查看文件的全部内容，一次性显示文件中所有内容。

```shell
cat /etc/profile  # 查看环境变量配置
cat demo.txt  # 查看demo.txt
```

- **more**：多屏显示文件内容，显示后，使用**空格**显示下一屏，**回车**显示下一行，**q** 退出分屏显示，**ctrl + c** 退出命令。

```shell
more demo.txt
```

- **head**：显示文件的前多少行，**默认显示前 10 行**。`head –number filename` 显示文件中的前多少行。

```shell
head -5 words.txt  # 显示前5行
```

- **tail**：显示文件末尾多少行，**默认显示末尾 10 行**。`tail –number filename` 显示文件中末尾多少行。

```shell
tail -3 words.txt  # 显示后三行
```

- **mkdir**：make directory，创建目录。

```shell
mkdir temp  # 创建temp文件夹
```

- **cp**：copy，复制文件。

```shell
# 复制demo.txt到/usr/local并重命名为demo1.txt
# 不加重命名默认就是原来的名称
cp ~/demo.txt /usr/local/demo1.txt
```

- **rm**：remove。删除命令，谨慎使用。可能让你删库跑路(doge)。

```shell
# 删除文件默认有一个是否删除的提示
rm a.txt
# 参数 -f 不提示命令
rm -f a.txt
# 参数 -r 表示删除目录
rm -rf temp
# /* 表示全部目录，谨慎使用，小心进去
rm -rf /*
```

- **mv**：move，移动或重命名，相当于剪切或重命名。

```shell
mv ~/a.txt /usr/local/temp/b.txt  # 移动到/usr/local/temp，并重命名
mv a.txt b.java  # 重命名为b.java
```

- **vi/vim**：启动 Linux 中的文本编辑器，vim 是 vi 增强命令，不代表所有的 Linux 都支持。vim 会对文件一些行作高亮显示。

```shell
vim Hello.java
```

**编辑模式：**

| 参数 | 描述                         |
| ---- | ---------------------------- |
| a    | 追加方式进行编辑模式         |
| i    | 插入方式进行编辑模式         |
| o    | 在光标所在位置之下，新增一行 |
| O    | 在光标所在位置之上，新增一行 |

**命令模式：**

| 参数      | 描述                |
| --------- | ------------------- |
| dd        | 删除指定行，默认1行 |
| :w        | write保存           |
| :q        | quit退出vi编辑器    |
| :wq       | 保存并退出          |
| :q!       | 强制退出不保存      |
| :set nu   | 显示行号            |
| /keywords | 搜索                |
| G         | 光标跳转到文件尾    |
| gg        | 光标跳转到文件头    |

## 3.2 重启关机

- **reboot**：用来重启 Linux 系统和 Windows 系统中的 restart 一样，但是重启必须是 root 用户才有权限。
- **halt**：用来关闭正在运行Linux操作系统 (关机)。

## 3.3 时间设置

使用 `date` 命令可以查看当前的时间或修改时间：

```shell
data -s '20200101 10:00:00'
```

## 3.4 网络配置

- **启用网络**：

```shell
nmcli c up ens33
```

这条命令可以启动网卡，但是只对当次使用有效。

```shell
vim /etc/sysconfig/network-scripts/ifcfg-ens33
```

直接修改配置文件可以永久有效。找到 BOOTPROTO 和 NAME 这两项进行修改：

```
BOOTPROTO=dhcp
NAME=ens33
```

- **NAT 模式**：

在 VMWare 中默认使用的 NAT 模式。NAT模式：Network Address Translation，网络地址转换，允许一个整体机构以一个公用 IP 地址出现在Internet上，顾名思义，它是一种把内部私有网络地址（IP地址）通过 NAT 转发成合法公有网络 IP 地址的技术。

优点：可以节省IP资源 (不会占用局域网的Ip地址，因为是新分配的网段)。

缺点：由于网段不同，局域网内其他设备无法访问虚拟机中的操作系统。

## 3.5 文件压缩

### 3.5.1 tar.gz格式

- **tar.gz 文件格式**：

tar.gz 是 Linux 中的一种文件压缩格式。使用 tar 命令实现对文件的压缩与解压处理。

- **tar 命令的相关参数**：

| 参数 | 描述                                                         |
| ---- | ------------------------------------------------------------ |
| -c   | 创建压缩文件                                                 |
| -C   | 指定解压文件存放的位置                                       |
| -x   | 解压                                                         |
| -t   | 查看内容                                                     |
| -z   | 有gzip属性                                                   |
| -v   | 显示所有过程                                                 |
| -f   | 使用压缩或解压缩文件的名字，这个参数是最后一个参数，后面只能接文件名 |

- **创建 tar.gz 压缩文件**：

```shell
tar -czvf a.tar.gz demo.txt
```

- **解压 tar.gz 文件**：

```shell
tar -zxvf a.tar.gz  # 解压文件
tar -zxvf a.tar.gz -C /usr/local/temp  # 指定目录解压缩
```

### 3.5.2 zip格式

- **zip 格式**：如果压缩文件格式为 zip，那么需要使用 `unzip` 命令解压 zip 压缩包。
- **unzip 相关参数：**

| 参数 | 描述                         |
| ---- | ---------------------------- |
| -n   | 解压缩时不要覆盖原有的文件   |
| -d   | 指定文件解压后所要存储的目录 |
| -v   | 执行时显示详细信息           |

- **解压 zip 文件**：

```shell
unzip a.zip  # 解压单个文件
unzip a.zip -d /usr/local/temp  # 解压到指定的目录
```

# 四. 基础软件

## 4.1 安装JDK

1. 下载 Linux 对应版本的 JDK：

查看 Linux 操作系统的位数：

```shell
getconf LONG_BIT
```

2. 下载压缩的 JDK：jdk-11.0.16.1_linux-x64_bin.tar.gz。
3. 解压缩文件：

```shell
tar -xvf jdk-11.0.16.1_linux-x64_bin.tar.gz -C /usr/local/Java
```

在 */usr/local* 目录下创建 Java 目录，然后解压这个 JDK 到该目录下。

4. 配置环境变量：

进入 JDK 安装目录，输入 `pwd` 获取文件路径，然后编辑环境变量文件：

```shell
vim /etc/profile
```

添加以下几行代码：

```shell
export JAVA_HOME=/usr/local/Java/jdk-11.0.16.1
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin
```

使环境变量生效：

```shell
source /etc/profile
```

5. 检查是否生效：

```shell
java -version
```

## 4.2 安装Tomcat

1. 去官网下载 Linux 对应版本的 Tomcat：*apache-tomcat-8.5.79.tar.gz*。
2. 解压缩文件：

```shell
tar -xvf apache-tomcat-8.5.79.tar.gz -C /usr/local/Tomcat
```

在 */usr/local* 目录下创建 Tomcat 目录，并把压缩文件解压到该目录。

3. 配置环境变量：

```shell
export CATALINA_BASE=/usr/local/tomcat8/apache-tomcat-8.5.79
export PATH=$CATALINA_BASE/bin:$PATH
```

4. 使环境变量生效：

```shell
source /etc/profile
```

5. 启动 Tomcat：

配置了环境变量之后直接在根目录输入以下命令启动 Tomcat：

```shell
startup.sh
./startup.sh  # 或者直接去Tomcat的bin目录下执行
```

关闭 Tomcat 也是同理：

```shell
shutdown.sh
./ shutdown.sh  # 去Tomcat的bin目录下执行
```

6. 访问 Tomcat：

在本机可以直接访问 Tomcat，使用 `curl` 命令即可，发送一个 HTTP 请求：

```shell
curl http://127.0.0.1:8080
```

收到回应那么说明成功了。

假如使用云服务器部署 Tomcat，那么需要配置防火墙规则，以阿里云为例，需要配置防火墙规则，点击添加规则，选择**全部 TCP/UDP** ，配置限制 IP 来源为：**0.0.0.0/0**，广播 IP 地址表示任何地址。最后在浏览器输入云服务地址加上 8080 端口号访问即可。

## 4.3 安装MySQL

1. 下载 MySQL：

```shell
wget https://repo.mysql.com//mysql80-community-release-el8-1.noarch.rpm
```

2. 使用 rpm 安装 MySQL：

```shell
rpm -ivh mysql80-community-release-el8-1.noarch.rpm
```

3. 使用 yum 安装 MySQL 服务：

```shell
yum install mysql-server
```

4. 检查是否已经设置为开机启动 MySQL：

```shell
systemctl list-unit-files|grep mysqld
```

5. 设置开机启动：

```shell
systemctl enable mysqld.service
```

6. 启动 MySQL 服务：

```shell
systemctl start mysqld.service
```

7. 测试是否安装成功：

```shell
mysql -u root -h 127.0.0.1 -p
```

回车之后输入密码，第一次安装默认没有密码，直接回车即可。	

想要关闭 mysql 使用 `service` 命令即可：

```shell
service mysql stop
```