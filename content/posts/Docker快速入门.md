---
title: "Docker快速入门"
date: "2024-05-17T11:31:36+08:00"
draft: false
tags: ["Docker", "虚拟化", "Linux"]
categories: ["教程"]
summary: "随着对大语言模型的深度使用，我们现在需要使用一些更加复杂的工具。这些工具并不是可以支持原生的Windows运行环境。为了能够在Windows上方便地使用这些软件或框架集，我们需要安装一套功能非常强大的运行环境，这个运行环境叫做Docker。"
---

# Docker快速入门

更多AIGC教程，访问UP「码钉泥」的B站
[https://space.bilibili.com/650927704/](https://space.bilibili.com/650927704/)

## 1. 了解Docker

随着对大语言模型的深度使用，我们现在需要使用一些更加复杂的工具。这些工具并不是可以支持原生的Windows运行环境。为了能够在Windows上方便地使用这些软件或框架集，我们需要安装一套功能非常强大的运行环境，这个运行环境叫做Docker。

### 什么是Docker

那么什么是Docker呢？你可以查到很多与它相关的技术资料，但对于刚刚入门的朋友来说，可能会显得有些晦涩难懂。

简单来说，Docker是一种虚拟化技术，也就是在当前的操作系统下创建一个相对独立的特定操作系统环境，用来运行我们所需要的软件。Docker的虚拟化介于虚拟机和沙盒之间，是一种非常高效的虚拟化方式。

![Image](/images/Docker%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8/Untitled.png)

### Docker的优势

每个运行的Docker容器都捆绑了这个容器所运行的软件以及最低程度所需求的系统环境。举个例子，如果我需要运行一个Docker中的文本编辑器，那么Docker不会给它提供超出文本编辑器之外的系统组件，例如处理音频、视频、网络通信等这一类的组件。因此，我们的Docker容器通常非常精简，运行效率极高。

如果你在Windows上运行一个Linux的虚拟机，则必须附带Linux系统的所有系统组件。即便我只想在这个虚拟机中运行一个简单的文本编辑器，它同样会加载音频、视频、网络通信等系统核心组件。

![Image](/images/Docker%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8/Untitled%201.png)

### Docker与Windows适配

Docker在Windows上的适配经历了三个阶段：第一个阶段是完全不适配Windows，因为Docker本身是构建在Linux系统上的一个虚拟化技术；第二阶段是有限的基于虚拟机的适配；第三阶段是基于WSL2（Windows Subsystem for Linux 2）虚拟化技术的适配。

![Image](/images/Docker%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8/Untitled%202.png)

目前WSL2已经可以支持以非常高的效率在Windows系统上虚拟Linux系统。配合Docker Desktop，可以使得我们非常方便地在Windows上运行Linux程序。

### Docker与LLM应用

目前很多大型语言模型的应用工具，例如RAG（Retrieval-Augmented Generation）、工作流、Agent等，都是在Linux环境下开发的，它们都不支持直接在Windows上安装和部署。因此，我们就需要采用Docker环境来运行这些程序。今天，我们就来带领大家学习如何安装Docker，并进行简单的管理操作。

## 2. 安装Docker

### 2.0 前置条件

首先要注意，想要运行 Docker Desktop，你的windows系统必须高于1904版本。

你可以进入 Windows设置→系统→关于，来查看当前的windows版本。例如我所使用的windows版本号为22h2。

![Image](/images/Docker%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8/Untitled%203.png)

不过请放心，一般来说只有超过5年以上从未更新过的电脑才会停留在1904那个比较古老的版本。

通常只有Windows专业版、企业版或工作站版才能够安装Hyper-V和WSL2。而这两个组件是在Windows上运行Docker Desktop的必备条件。

但是今天我们介绍的安装方法，同时支持Windows家庭版和专业版的安装。我们将会帮助大家绕过一部分Windows系统的限制。

### 2.1 安装 Hyper-V

首先我们需要运行一个批处理程序来启动Hyper-V组件。

通过将下方的代码复制到记事本中，并另存为`enable_hyper_v.cmd`，之后直接右击该脚本，通过“管理员模式”运行。

```powershell
pushd "%~dp0"

dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt

for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"

del hyper-v.txt

Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL
```

你也可以直接点击这里 [123pan.com](https://www.123pan.com/s/5cWiVv-wj8C.html)（提取码:leai）下载这个脚本。

如果你的电脑没有Hyper-V组件，系统将会从Windows Update服务器那里更新Windows的相关组件，这可能会花费一些时间。安装完成后，系统需要重启。

### 2.2 安装WSL升级包

然后你需要前往微软的官方网站下载WSL的离线升级包。

[https://learn.microsoft.com/zh-cn/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package](https://learn.microsoft.com/zh-cn/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package)

或者直接点击这里 [WSL离线升级包](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi) 下载升级包。

当然，你也可以前往我们提供的网盘进行下载。点击这里 [123pan.com](https://www.123pan.com/s/5cWiVv-wj8C.html)（提取码:leai）下载WSL的离线升级包。

双击安装。这里我们可能需要再次重启电脑。

更多关于WSL安装的帮助信息可以在这里找到：[安装 WSL | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/wsl/install)

### 2.3 安装Docker Desktop

完成以上两步后，无论你使用的是Windows家庭版还是专业版，你就基本上可以无痛安装Docker。

Docker的官方下载地址是：

[Docker Desktop官方下载](https://www.docker.com/products/docker-desktop/)

如果网速不佳，你也可以前往我们提供的网盘进行下载。

下面我们来演示Docker的安装过程。安装时默认选中使用基于WSL2的虚拟化，点击“下一步”就可以轻松完成Docker的安装。

![Image](/images/Docker%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8/cff0143b-1e5c-4a5c-a3fc-443d1ca25deb.png)

如果你只是偶尔使用基于Docker的某些应用程序，那么你可以关闭Docker的自动启动功能，选择在必要的时候手动启动。

## 3. Docker操作入门

### 3.1 Docker的基本概念

Docker与其他虚拟化技术最大的区别体现在它是基于应用程序的虚拟化。因此，Docker的官方仓库提供了大量的Docker应用程序包下载服务。每个程序包被称为镜像（Image）。通过一个镜像，我们可以创建一个或多个执行不同参数的应用程序。每个运行中的应用程序被称为容器（Container）。

这是Docker最重要的两个概念：镜像（Image）和容器（Container）。

我们可以下载别人提供的镜像，或者创建自己的镜像。对镜像设置好相关的参数后，启动容器，就实现了利用Docker运行这个应用程序。

还有另外一些概念，我们会在接下来的演示中为大家逐步介绍。

### 3.2 查找镜像：以Memos为例

下面我们来搭建两个Docker应用程序，以展示Docker的应用。第一个应用程序叫做Memos，它是一个小巧的电子备忘录程序，可以创建一个备忘录网站，供我们记录一些碎片化的信息。

![Image](/images/Docker%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8/Untitled%204.png)

通过Docker Hub，我们可以查询到Memos的相关信息。Docker Hub使用Tags标签来标注应用程序包的不同版本。这个参数通常以冒号+版本号（或者latest）的形式出现在应用程序包名称的最后。对于一些简单的应用，我们通常选择冒号latest来下载最新版本就可以了。

![Image](/images/Docker%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8/Untitled%205.png)

我们有两种方式下载Docker提供的镜像。第一种是通过图形界面搜索，点击选择相应的版本号就可以开始下载；另一个方法是采用命令行的模式输入`docker pull memos:latest`来进行下载。

![Image](/images/Docker%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8/Untitled%206.png)

通常命令行更适合服务器环境的配置。在日常使用中，我们使用图形界面进行下载就可以了。

下载完成后，我们可以在Images选项卡中看到我们已经下载好的镜像。Memos的镜像很小，只有数十兆。

选择镜像，点击“Run（运行）”，就可以通过这个镜像创建一个Memos应用程序容器。根据不同的需求，我们可以通过一个镜像创建很多个容器。

### 3.3 启动容器：以Memos为例

每个新建的容器会独立存储它运行过程中产生的新文件，例如我们输入的备忘录信息。然而程序本身仍然依赖于镜像中的文件进行运行，也就是说多个容器采用的是增量存储的方式来处理我们产生的数据。

换句话说，只要存在由某个镜像创建的容器，就必须保持这个镜像一直存储在你的系统中。

**3.3.1 存储机制**

接下来我们要配置Memos的一些系统参数，例如数据的存放位置。我们有两种选择：一种是让Docker自己创建一个封闭的虚拟磁盘用来存储Memos的数据；另一种是绑定本机的特定目录或特定文件。

![Image](/images/Docker%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8/Untitled%207.png)

虚拟磁盘在Docker中被称为Volume或者卷。绑定目录被称为Bind绑定或者挂载。这也是Docker中非常重要的概念之一。

这两种方式都会创造永久存储的数据，即使你删除容器和镜像，未来你也可以依靠这些数据重建你的Memos服务。区别在于虚拟磁盘无法直接读取或写入，但是它不存在权限问题；绑定目录或文件方便我们修改查看和修改相关数据，但可能存在权限问题。

![Image](/images/Docker%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8/Untitled%208.png)

这里我们选择绑定目录。我们在D盘新建一个Memos的数据存储目录，我们填写"**C:\Users\Adam\Memos**"。那么我们要绑定哪个目录呢？这里需要查阅Docker Hub上提供的相关信息。每个应用程序提供的存储位置都不相同。Memos的存储位置是"/var/opt/memos"。

![Image](/images/Docker%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8/Untitled%209.png)

**3.3.2 端口映射**

然后我们需要开放相应的端口。什么是端口呢？端口是计算机中的程序进行相互通信时设定的电子门牌号。通过特定的门牌号，我们就可以与指定的应用程序进行通讯。

Docker提供方便的端口号映射服务，可以将应用程序内的端口映射到当前操作系统的端口。这就好像是将大楼内部的门牌号转换成为街边的邮政信箱号码一样。

我们为Memos设定本地端口5555绑定容器内的端口5230。

![Image](/images/Docker%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8/Untitled%2010.png)

这意味着5555号邮政信箱对应的就是环球中心A座80楼80号办公室。

点击“启动”，我们就可以看到一个新的容器被创建了。我们来访问localhost的冒号5555，Memos已经成功运行了，让我们返回Docker的管理界面，点击“容器”可以查看Memos容器正在运行的状态。

这个界面打印的是容器的命令行界面。让我们打开刚才绑定的目录，可以看到Memos创建了一个新的文件，它是Memos的数据库文件。

### 3.4 启动容器：以ChatGPT Next Web为例

下面我们来使用Docker运行ChatGPT Next Web程序。这是一个在线版的大语言模型聊天程序。通过Next Web，我们可以很方便地在线或本地与大语言模型进行对话。

![Image](/images/Docker%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8/Untitled%2011.png)

我们在Docker Desktop顶部的搜索栏，输入yidadaa，下载chatGPTNextWeb的最新镜像。

我们打开chatGPTNextWeb的Github主页，找到Docker运行方式的相关参数介绍

[https://github.com/ChatGPTNextWeb/ChatGPT-Next-Web](https://github.com/ChatGPTNextWeb/ChatGPT-Next-Web)

在官方提供的范例中，预设了3000端口号，并且设置了两个环境变量，分别是OpenAI_API_Key，和nextWeb的访问密码（CODE）.

```powershell
docker pull yidadaa/chatgpt-next-web \
```

```powershell
docker run -d -p 3000:3000 \
-e OPENAI_API_KEY=sk-xxxx \
-e CODE=your-password \
yidadaa/chatgpt-next-web
```

我们按照官方的范例来创建一个容器。点击NextWeb镜像的运行按钮。

打开更多设置，输入容器名称nextWeb，映射端口号3000。在这里，我们不用设置目录绑定或者卷文件。添加一个新的系统变量，OpenAI_API_Key，添加另外一个系统变量CODE，点击Run，我们就启动了本地的nextWeb服务。

我们来访问一下这个本地的网站，在浏览器中输入localhost:3000。

如果你输入了正确的OpenAI_API_Key，并且你的本地网络可以直连到OpenAI的API服务器，那么你就可以通过这个界面，与OpenAI的大语言模型进行聊天。

## 结语

通过今天的教程，我们已经了解了如何在Windows上安装Docker Desktop，以及Docker的基本使用方法，在接下来的教程中，我们将会带领大家使用Docker搭建一些有趣的大语言模型应用。