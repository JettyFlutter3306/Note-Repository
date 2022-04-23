# Git快速入门

# 一. 版本控制概要

## 1.1 git简介

Git 是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

Git 是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。

Git 与常用的版本控制工具 CVS, Subversion 等不同，它采用了分布式版本库的方式，不必服务器端软件支持。

![](asset/Git/image/git-logo.png)  



## 1.2 什么是版本控制

版本控制（Revision control）是一种在开发的过程中用于管理我们对文件、目录或工程等内容的修改历史，方便查看更改历史记录，备份以便恢复以前的版本的软件工程技术。

- 实现跨区域多人协同开发
- 追踪和记载一个或者多个文件的历史记录
- 组织和保护你的源代码和文档
- 统计工作量
- 并行开发、提高开发效率
- 跟踪记录整个软件的开发过程
- 减轻开发人员的负担，节省时间，同时降低人为错误

## 1.3 常用术语

**1) 仓库（Repository）**
受版本控制的所有文件修订历史的共享数据库

**2) 工作空间（Workspace)**
本地硬盘或Unix 用户帐户上编辑的文件副本

**3) 工作树/区（Working tree）**
工作区中包含了仓库的工作文件。您可以修改的内容和提交更改作为新的提交到仓库。

**4) 暂存区（Staging area）**
暂存区是工作区用来提交更改（commit）前可以暂存工作区的变化。

![](../../../Learning/markdown%E7%AC%94%E8%AE%B0/git/assets/1-1.png) 

**5) 索引（Index）**
索引是暂存区的另一种术语。

**6) 签入（Checkin）**
将新版本复制回仓库

**7) 签出（Checkout）**
从仓库中将文件的最新修订版本复制到工作空间

**8) 提交（Commit）**
对各自文件的工作副本做了更改，并将这些更改提交到仓库

**9) 冲突（Conflict）**
多人对同一文件的工作副本进行更改，并将这些更改提交到仓库

**10) 合并（Merge）**
将某分支上的更改联接到此主干或同为主干的另一个分支

**11) 分支（Branch）**
从主线上分离开的副本，默认分支叫master

**12) 锁（Lock）**
获得修改文件的专有权限。

**13) 头（HEAD）**
头是一个象征性的参考，最常用以指向当前选择的分支。

**14) 修订（Revision）**
表示代码的一个版本状态。Git通过用SHA1 hash算法表示的ID来标识不同的版本。

**15) 标记（Tags）**
标记指的是某个分支某个特定时间点的状态。通过标记，可以很方便的切换到标记时的状态。



## 1.4 常见的版本控制器

主流的版本控制器有如下这些：

- **Git**
- **SVN**（Subversion）
- **CVS**（Concurrent Versions System）
- **VSS**（Micorosoft Visual SourceSafe）
- **TFS**（Team Foundation Server）
- **Visual Studio Online**

版本控制产品非常的多（Perforce、Rational ClearCase、RCS（GNU Revision Control System）、Serena Dimention、SVK、BitKeeper、Monotone、Bazaar、Mercurial、SourceGear Vault），现在影响力最大且使用**最广泛的是Git与SVN**



## 1.5  版本控制分类

### 1.5.1 本地版本控制

记录文件每次的更新，可以对每个版本做一个快照，或是记录补丁文件，适合个人用，如RCS。

![img](asset/Git/image/本地版本控制.png) 

### 1.5.2 集中版本控制

所有的版本数据都保存在服务器上，协同开发者从服务器上同步更新或上传自己的修改

![img](asset/Git/image/集中版本控制.png) 

所有的版本数据都存在服务器上，用户的本地只有自己以前所同步的版本，如果不连网的话，用户就看不到历史版本，也无法切换版本验证问题，或在不同分支工作。而且，所有数据都保存在单一的服务器上，有很大的风险这个服务器会损坏，这样就会丢失所有的数据，当然可以定期备份。代表产品：SVN、CVS、VSS

### 1.5.3 分布式版本控制

所有版本信息仓库全部同步到本地的每个用户，这样就可以在本地查看所有版本历史，可以离线在本地提交，只需在连网时push到相应的服务器或其他用户那里。由于每个用户那里保存的都是所有的版本数据，只要有一个用户的设备没有问题就可以恢复所有的数据，但这增加了本地存储空间的占用。

![img](asset/Git/image/分布式版本控制.png) 



## 1.6 Git与SVN最主要区别

**SVN是集中式版本控制系统**，版本库是集中放在中央服务器的，而工作的时候，用的都是自己的电脑，所以首先要从中央服务器得到最新的版本，然后工作，完成工作后，需要把自己做完的活推送到中央服务器。集中式版本控制系统是必须联网才能工作，对网络带宽要求较高。

**Git是分布式版本控制系统**，没有中央服务器，每个人的电脑就是一个完整的版本库，工作的时候不需要联网了，因为版本都在自己电脑上。协同的方法是这样的：比如说自己在电脑上改了文件A，其他人也在电脑上改了文件A，这时，你们两之间只需把各自的修改推送给对方，就可以互相看到对方的修改了。

**Git 与 SVN 区别点：**

- **Git 是分布式的，SVN 不是**：这是 Git 和其它非分布式的版本控制系统，例如 SVN，CVS 等，最核心的区别。
- **Git 把内容按元数据方式存储，而 SVN 是按文件：**所有的资源控制系统都是把文件的元信息隐藏在一个类似 .svn、.cvs 等的文件夹里。
- **Git 分支和 SVN 的分支不同：**分支在 SVN 中一点都不特别，其实它就是版本库中的另外一个目录。
- **Git 没有一个全局的版本号，而 SVN 有：**目前为止这是跟 SVN 相比 Git 缺少的最大的一个特征。
- **Git 的内容完整性要优于 SVN：**Git 的内容存储使用的是 SHA-1 哈希算法。这能确保代码内容的完整性，确保在遇到磁盘故障和网络问题时降低对版本库的破坏。

![img](asset/Git/image/git和svn区别.jpg) 

## 1.7 Git的工作流程

一般工作流程如下：

- 克隆 Git 资源作为工作目录。

- 在克隆的资源上添加或修改文件。

- 如果其他人修改了，你可以更新资源。

- 在提交前查看修改。

- 提交修改。

- 在修改完成后，如果发现错误，可以撤回提交并再次修改并提交。

  

**Git 的工作流程图：**

 ![img](asset/Git/image/git-process.png) 



# 二. Git安装和配置

Git是目前世界上最先进的分布式版本控制系统。

Git是免费、开源的

最初Git是为辅助 Linux 内核开发的，来替代 BitKeeper

**作者**：Linux和Git之父林纳斯·托沃兹（Linus Benedic Torvalds）1969年出生于芬兰

**优点：**

- 适合分布式开发，强调个体。
- 公共服务器压力和数据量都不会太大。
- 速度快、灵活。
- 任意两个开发者之间可以很容易的解决冲突。
- 离线工作。

**缺点：**

- 模式上比SVN更加复杂。
- 不符合常规思维。
- 代码保密性差，一旦开发者把整个库克隆下来就可以完全公开所有代码和版本信息。

**官网**： https://git-scm.com/

**源码：** https://github.com/git/git/



## 2.1 搭建Git工作环境

### 2.1.1 下载Git

打开 [git官网](https://git-scm.com/)，下载git对应操作系统的版本。

![image-20200401215051155](asset/Git/image/Git官网.png)

选择版本：

![image-20200401215141714](asset/Git/image/选择版本.png)

这里我选择下载**[64-bit Git for Windows Setup](https://github.com/git-for-windows/git/releases/download/v2.26.0.windows.1/Git-2.26.0-64-bit.exe) **

![image-20200401215928412](../../../Learning/markdown%E7%AC%94%E8%AE%B0/git/assets/image-20200401215928412.png) 

### 2.1.2 安装Git

![image-20200401220031350](asset/Git/image/安装Git.png) 

选择安装配置信息

![image-20200401220736748](asset/Git/image/选择配置信息.png) 

一直Next默认就好了，如果需要设置就要仔细读一下安装界面上的选项。



### 2.1.3 安装TortoiseGit

打开 [TortoiseGit官网](https://tortoisegit.org/download/)，下载TortoiseGit对应操作系统的版本。

下载红框框中的两个软件包

![image-20200401223221088](asset/Git/image/Tortois官网.png)



### 2.1.4 安装TortoiseGit

![image-20200401223408958](asset/Git/image/TortoiseGit安装.png) 

一路点击next按钮

![image-20200401223615103](asset/Git/image/tortoise安装1.png) 

点击finish按钮，一路点击下一步按钮，填写用户名和邮箱

![image-20200401224051805](asset/Git/image/tortoise-finished.png) 

### 2.1.5 安装TortoiseGit语言包

一路点击下一步

![image-20200401223704779](asset/Git/image/TortoiseGit语言包.png) 

**切记：重启电脑**

### 2.1.6 启动Git

安装成功后在开始菜单中会有Git项，菜单下有3个程序：

![img](asset/Git/image/启动Git.png) 

**Git Bash：**Unix与Linux风格的命令行，使用最多，推荐最多

![image-20200401221949695](asset/Git/image/GitBash.png) 

与DOS风格的命令有些区别，不习惯可以选择Git CMD

**Git CMD：**Windows风格的命令行

![image-20200401222008117](asset/Git/image/Git CMD.png) 

**Git GUI**：图形界面的Git，不建议初学者使用，尽量先熟悉常用命令

![image-20200401222023540](asset/Git/image/Git GUI.png) 



## 2.2 TortoiseGit配置

### 2.2.1 使用可视化工具：

在**任意文件夹**内空白处点击鼠标右键, 选择 --> TortoiseGit --> Settings

![image-20200401224835538](asset/Git/image/可视化工具.png) 



在**任意文件夹**内空白处点击鼠标右键,可以看到弹出菜单中已经变成中文，此处可以修改用户名和邮箱

![image-20200401225121513](asset/Git/image/image-20200401225121513.png) 



### 2.2.2 Git命令行配置 - git config

#### 2.2.2.1 查看配置 - git config -l

使用git config -l 可以查看现在的git环境详细配置

![长度 ](asset/Git/image/image-20200401231324250.png) 

查看不同级别的配置文件：

```shell
#查看系统config
git config --system --list
　　
#查看当前用户（global）配置
git config --global  --list
 
#查看当前仓库配置信息
git config --local  --list
```

#### 2.2.2.2 Git配置文件分类

**Git相关的配置文件有三个：**

- **system 系统级**

  /etc/gitconfig：包含了适用于系统所有用户和所有项目的值

  ![image-20200401233041415](asset/Git/image/image-20200401233041415.png) 

- **global 全局**

  ~/.gitconfig：只适用于当前登录用户的配置。(路径：C:\Users\Administrator\\.gitconfig) 

  ![image-20200401231900683](asset/Git/image/image-20200401231900683.png)  

- **local当前项目**

  位于git项目目录中的.git/config：适用于特定git项目的配置。(C:\gitProject) 

  ![image-20200401232825290](asset/Git/image/image-20200401232825290.png)

**注意**：对于同一配置项，三个配置文件的优先级是**system 系统级**<**global 全局**<**local当前项目**

#### 2.2.2.3 设置用户名与邮箱（用户标识，必要）

当你安装Git后首先要做的事情是设置你的用户名称和e-mail地址。这是非常重要的，因为每次Git提交都会使用该信息。它被永远的嵌入到了你的提交中：

```shell
$ git config --global user.name "justweb" #名称
$ git config --global user.email justweb@163.com  #邮箱
```

只需要做一次这个设置，如果你传递了--global 选项，因为Git将总是会使用该信息来处理你在系统中所做的一切操作。如果你希望在一个特定的项目中使用不同的名称或e-mail地址，你可以在该项目中运行该命令而不要--global选项。 总之--global为全局配置，不加为某个项目的特定配置。

![image-20200401234943270](asset/Git/image/image-20200401234943270.png)



#### 2.2.2.4 添加或删除配置项

- **添加配置项** 

  语法：

  ```shell
  git config [--local|--global|--system]  section.key value
  ```

  在student区域下**添加**一个名称为height 值为198 的配置项 （系统级）

  ```shell
  $ git config --system student.height 198
  ```

  ![image-20200401235719491](asset/Git/image/image-20200401235719491.png) 

- **删除配置项** 

  语法：

  ```shell
  git config [--local|--global|--system] --unset section.key
  ```

  在student区域下**删除**一个名称为height 值为198 的配置项 （系统级）

  ```shell
  $ git config --system --unset student.height
  ```

  ![image-20200401235905563](asset/Git/image/image-20200401235905563.png) 



# 三. Git理论基础

## 3.1 工作区域

Git本地有三个工作区域：工作目录（Working Directory）、暂存区(Stage/Index)、资源库(Repository或Git Directory)。如果在加上远程的git仓库(Remote Directory)就可以分为四个工作区域。文件在这四个区域之间的转换关系如下：

![img](asset/Git/image/63651-20170905201017069-171460014.png) 

- **Workspace**：工作区，就是你平时存放项目代码的地方
- **Index / Stage**：暂存区，用于临时存放你的改动，事实上它只是一个文件，保存即将提交到文件列表信息
- **Repository**：仓库区（或本地仓库），就是安全存放数据的位置，这里面有你提交到所有版本的数据。其中HEAD指向最新放入仓库的版本
- **Remote**：远程仓库，托管代码的服务器，可以简单的认为是你项目组中的一台电脑用于远程数据交换

本地的三个区域确切的说应该是git仓库中HEAD指向的版本

![img](asset/Git/image/63651-20170906230652788-1835188684.png)

- **Directory**：使用Git管理的一个目录，也就是一个仓库，包含我们的工作空间和Git的管理空间。
- **WorkSpace**：需要通过Git进行版本控制的目录和文件，这些目录和文件组成了工作空间。
- **.git**：存放Git管理信息的目录，初始化仓库的时候自动创建。
- **Index/Stage**：暂存区，或者叫待提交更新区，在提交进入repo之前，我们可以把所有的更新放在暂存区。
- **Local Repo**：本地仓库，一个存放在本地的版本库；HEAD会只是当前的开发分支（branch）。
- **Stash**：隐藏，是一个工作状态保存栈，用于保存/恢复WorkSpace中的临时状态。

## 3.2 工作流程

git的工作流程一般是这样的：

１、在工作目录中添加、修改文件；

２、将需要进行版本管理的文件放入暂存区域；

３、将暂存区域的文件提交到git仓库。

因此，git管理的文件有三种状态：已修改（modified）,已暂存（staged）,已提交(committed)

![img](asset/Git/image/63651-20170905201033647-1915833066.png)

# 四. Git操作

## 4.1 创建工作目录与常用指令

工作目录（WorkSpace)一般就是你希望Git帮助你管理的文件夹，可以是你项目的目录，也可以是一个空目录，建议不要有中文。

日常使用只要记住下图6个命令：

![img](asset/Git/image/63651-20170905212837976-775285128.png)

## 4.2 创建GIT仓库

创建本地仓库的方法有两种：一种是创建全新的仓库，另一种是克隆远程仓库。

![img](asset/Git/image/63651-20170905214339226-738603749.png)

### 4.2.1 创建全新仓库

#### 4.2.1.1 在GIT管理的项目的根目录执行：

```shell
# 在当前目录新建一个Git代码库
$ git init
```

**执行：**

![image-20200402002232554](asset/Git/image/image-20200402002232554.png) 

**结果：**

 ![image-20200402002302741](asset/Git/image/image-20200402002302741.png)

执行后可以看到，仅仅在项目目录多出了一个.git目录，关于版本等的所有信息都在这个目录里面。

#### 4.2.1.2 使用如下命令，可以把创建目录与仓库一起完成：

```shell
# 新建一个目录，将其初始化为Git代码库
$ git init [project-name]
```

 执行命令与运行结果：

![image-20200402002504946](asset/Git/image/image-20200402002504946.png)



#### 4.2.1.3 使用可视化工具

文件夹内空白处右键->Git GUI Here,点击`Create New Repository`

![image-20200402002719871](asset/Git/image/image-20200402002719871.png) 

输入仓库名称

![image-20200402002935782](asset/Git/image/image-20200402002935782.png) 

**概念：**

**版本库**：“.git”目录就是版本库，将来文件都需要保存到版本库中。

**工作目录**：包含“.git”目录的目录，也就是.git目录的上一级目录就是工作目录。只有工作目录中的文件才能保存到版本库中。



### 4.2.2 克隆远程仓库

 另一种方式是克隆远程目录，由于是将远程服务器上的仓库完全镜像一份至本地，而不是取某一个特定版本，所以用clone而不是checkout，语法格式如下：

```shell
# 克隆一个项目和它的整个代码历史(版本信息)
$ git clone [url]
```

**执行：**

比如我们要从克隆的远程仓库托管在github上，地址为：https://gitee.com/Lobidas_Daddy/data-structure.git，这是一个公开的项目

![image-20200402003619409](asset/Git/image/image-20200402003619409.png) 



进入Git Bash控制台输入 git clone命令

```shell
$ git clone https://github.com/yushanbo/gittest10.git
```

将远程仓库的代码克隆下来

![image-20200402003836393](asset/Git/image/image-20200402003836393.png) 



## 4.3 添加文件

### 4.3.1 添加文件过程

1. 在project1目录下创建一个justweb.txt文件

   ![image-20200402010051191](asset/Git/image/image-20200402010051191.png)

2. 添加justweb.txt到git版本库

   ![image-20200402010218450](asset/Git/image/image-20200402010218450.png)

3. 文本文件变为带“+”号的图标：

   ![image-20200402010320043](asset/Git/image/image-20200402010320043.png) 

4. 提交文件：在justweb.txt上再次点击右键选择“提交”，此时将文件保存至版本库中。

   ![image-20200402010430117](asset/Git/image/image-20200402010430117.png)

5. 一定要输入日志信息，然后点击提交

![image-20200402010504180](asset/Git/image/image-20200402010504180.png) 

6.提交完成

![image-20200402010558824](asset/Git/image/image-20200402010558824.png) 



### 4.3.2 更新文件过程

被版本库管理的文件发生了修改，修改完毕后需要将文件的修改提交到版本库。

1. 在 justweb.txt文件中添加一行“hello justweb”文本

   ![image-20200402011736036](asset/Git/image/image-20200402011736036.png) 

   

2. 提交更新，在justweb.txt文件上点击右键，然后选择“Git 提交”

   ![image-20200402011034831](asset/Git/image/image-20200402011034831.png)

3. 添加日志信息

   ![image-20200402011217201](asset/Git/image/image-20200402011217201.png) 

4. 提交数据

   ![image-20200402011255443](asset/Git/image/image-20200402011255443.png) 



### 4.3.3 修改文件过程

#### 4.3.3.1 查看修改历史

在开发过程中经常查看代码的修改历史：

- 查看某个版本是谁修改的
- 什么时间修改的
- 修改了哪些内容。



1. 在文件上右键 选择“显示日志”来查看文件的修改历史。

   ![image-20200402011513018](asset/Git/image/image-20200402011513018.png)

   

2. 查看日志信息

   ![image-20200402011642400](asset/Git/image/image-20200402011642400.png) 



#### 4.3.3.2 差异比较

文件内容修改后可以使用差异比较，对比一下修改了哪些内容

![image-20200402012459517](asset/Git/image/image-20200402012459517.png) 



两个版本的差异部分

![image-20200402012414538](asset/Git/image/image-20200402012414538.png)



#### 4.3.3.3 还原修改

文件修改后不想把修改的内容提交，使用还原功能还原到未修改之前的状态。

1. “还原”功能

   ![image-20200402013017966](asset/Git/image/image-20200402013017966.png)

   

2. 还原完成

   ![image-20200402013107488](asset/Git/image/image-20200402013107488.png)



4.3.4 删除文件过程

![image-20200402013517481](asset/Git/image/image-20200402013517481.png)



### 4.3.4 案例：将java工程提交到版本库

1. 将参考资料中的java工程AutoMD复制到工作目录中

   ![image-20200402015136720](asset/Git/image/image-20200402015136720.png)

2. 将工程添加到暂存区

   ![image-20200402015244088](asset/Git/image/image-20200402015244088.png)

   ![image-20200402015324388](asset/Git/image/image-20200402015324388.png) 



3. 添加完成

   ![image-20200402015350727](asset/Git/image/image-20200402015350727.png)

### 4.3.5 忽略文件或文件夹

在此工程中，并不是所有文件都需要保存到版本库中的

- “target”目录及目录下的文件就可以忽略。
- ".idea"目录可以忽略
- “crateMD.iml”这个文件可以忽略



在Git工作区的根目录下创建一个特殊的.gitignore文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件。如果使用TortoiseGit的话可以使用菜单项直接进行忽略。

1. 忽略操作

   ![image-20200402020006878](asset/Git/image/image-20200402020006878.png) 



2. 忽略界面

   ![image-20200402020043423](asset/Git/image/image-20200402020043423.png) 



### 4.3.6 忽略文件语法规范

- 空行或是以 # 开头的行即注释行将被忽略。
- 可以在前面添加正斜杠 / 来避免递归,下面的例子中可以很明白的看出来与下一条的区别。
- 可以在后面添加正斜杠 / 来忽略文件夹，例如 build/ 即忽略build文件夹。
- 可以使用 ! 来否定忽略，即比如在前面用了 *.apk ，然后使用 !a.apk ，则这个a.apk不会被忽略。
- *用来匹配零个或多个字符，如 *.[oa] 忽略所有以".o"或".a"结尾， *~ 忽略所有以 ~ 结尾的文件（这种文件通常被许多编辑器标记为临时文件）； [] 用来匹配括号内的任一字符，如 [abc] ，也可以在括号内加连接符，如 [0-9] 匹配0至9的数； ? 用来匹配单个字符。 



**忽略文件语法规范**

```shell
# 忽略 .a 文件
*.a

# 但否定忽略 lib.a, 尽管已经在前面忽略了 .a 文件
!lib.a

# 仅在当前目录下忽略 TODO 文件， 但不包括子目录下的 subdir/TODO
/TODO

# 忽略 build/ 文件夹下的所有文件
build/

# 忽略 doc/notes.txt, 不包括 doc/server/arch.txt
doc/*.txt

# 忽略所有的 .pdf 文件 在 doc/ directory 下的
doc/**/*.pdf
```



