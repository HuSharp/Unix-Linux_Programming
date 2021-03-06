### 基础知识概要

1、对于 Linux 来讲，也是类似的方法，你可以下载 rpm 或者 deb。这个就是 Linux 下面的安装包。为什么有两种呢？因为 Linux 现在常用的有两大体系，一个是 CentOS 体系，一个是 Ubuntu 体系，前者使用 rpm，后者使用 deb。

在 Linux 上面，没有双击安装这一说，因此想要安装，我们还得需要命令。CentOS 下面使用rpm -i jdk-XXX_linux-x64_bin.rpm进行安装，Ubuntu 下面使用dpkg -i jdk-XXX_linux-x64_bin.deb。其中 -i 就是 install 的意思。

Linux 也有自己的软件管家，CentOS 下面是 yum，Ubuntu 下面是 apt-get。
安装：你可以用yum install java-11-openjdk.x86_64和apt-get install openjdk-9-jdk来进行安装。

卸载：我们可以使用yum erase java-11-openjdk.x86_64和apt-get purge openjdk-9-jdk。


Linux 允许我们配置从哪里下载这些软件的，地点就在配置文件里面。
对于 CentOS 来讲，配置文件在/etc/yum.repos.d/CentOS-Base.repo里。
对于 Ubuntu 来讲，配置文件在/etc/apt/sources.list里。

对应 Linux 里面会放的更散一点。例如，主执行文件会放在 /usr/bin 或者 /usr/sbin 下面，其他的库文件会放在 /var 下面，配置文件会放在 /etc 下面。


如何下载呢？Linux 上面有一个工具 wget，后面加上链接，就能从网上下载了。
Linux 下面默认会有 tar 程序。如果是解压缩 zip 包，就需要另行安装。如果是 tar.gz 这种格式的，通过 tar xvzf jdk-XXX_linux-x64_bin.tar.gz 就可以解压缩了。

在 Linux 也是一样的，通过 tar 解压缩之后，也需要配置环境变量，可以通过 export 命令来配置。



```
export JAVA_HOME=/root/jdk-XXX_linux-x64

export PATH=$JAVA_HOME/bin:$PATH
```



export 命令仅在当前命令行的会话中管用，一旦退出重新登录进来，就不管用了，有没有一个地方可以像 Windows 里面可以配置永远管用呢？
在当前用户的默认工作目录，例如 /root 或者 /home/cliu8 下面，有一个.bashrc 文件，这个文件是以点开头的，这个文件默认看不到，需要 ls -la 才能看到，a 就是 all。每次登录的时候，这个文件都会运行，因而把它放在这里。这样登录进来就会自动执行。当然也可以通过 source .bashrc 手动执行。



**vim**
如果文件有内容，就会显示出来。移动光标的位置，通过上下左右键就行。如果想要编辑，就把光标移动到相应的位置，输入i，意思是 insert。进入编辑模式，可以插入、删除字符，这些都和 notepad 很像。要想保存编辑的文本，我们使用esc键退出编辑模式，然后输入“:”，然后在“:”后面输入命令w，意思是 write，这样就可以保存文本，冒号后面输入q，意思是 quit，这样就会退出 vim。如果编辑了，还没保存，不想要了，可以输入q!。

通过 vim .bashrc，将 export 的两行加入后，输入:wq，写入并且退出，这样就编辑好了。

-------------------

Linux 运行程序的第二种方式，后台运行。

这个时候，我们往往使用nohup命令。这个命令的意思是 no hang up（不挂起），也就是说，当前交互命令行退出的时候，程序还要在。当然这个时候，程序不能霸占交互命令行，而是应该在后台运行。最后加一个 &，就表示后台运行。




od指令会读取所给予的文件的内容，并将其内容以八进制字码呈现出来。







### fork

对于 fork 系统调用的返回值，如果当前进程是子进程，就返回  0；如果当前进程是父进程，就返回子进程的进程号。这样首先在返回值这里就有了一个区分，然后通过 if-else  语句判断，如果是父进程，还接着做原来应该做的事情；如果是子进程，需要请求另一个系统调用execve来执行另一个程序，这个时候，子进程和父进程就彻底分道扬镳了，也即产生了一个分支（fork）了。





### 文件操作相关

对于文件的操作，下面这六个系统调用是最重要的：

- 对于已经有的文件，可以使用open打开这个文件，close关闭这个文件；
- 对于没有的文件，可以使用creat创建文件；
- 打开文件以后，可以使用lseek跳到文件的某个位置；
- 可以对文件的内容进行读写，读的系统调用是read，写是write。



Linux 里有一个特点，那就是**一切皆文件**。

- 启动一个进程，需要一个程序文件，这是一个**二进制文件**。
- 启动的时候，要加载一些配置文件，例如 yml、properties 等，这是文本文件；启动之后会打印一些日志，如果写到硬盘上，也是**文本文件**。
- 但是如果我想把日志打印到交互控制台上，在命令行上唰唰地打印出来，这其实也是一个文件，是标准输出**stdout 文件**。
- 这个进程的输出可以作为另一个进程的输入，这种方式称为**管道**，管道也是一个文件。
- 进程可以通过网络和其他进程进行通信，建立的**Socket**，也是一个文件。
- 进程需要访问外部设备，**设备**也是一个文件。
- 文件都被存储在文件夹里面，其实**文件夹**也是一个文件。
- 进程运行起来，要想看到进程运行的情况，会在 /proc 下面有对应的**进程号**，还是一系列文件。



### 进程间通信

先就是发个消息，不需要一段很长的数据，这种方式称为**消息队列**（Message Queue）。由于一个公司内的多个项目组沟通时，这个消息队列是在内核里的，我们可以通过msgget创建一个新的队列，msgsnd将消息发送到消息队列，而消息接收方可以使用msgrcv从队列中取消息。

当两个项目组需要交互的信息比较大的时候，可以使用**共享内存**的方式，也即两个项目组共享一个会议室（这样数据就不需要拷贝来拷贝去）。大家都到这个会议室来，就可以完成沟通了。这时候，我们可以通过shmget创建一个共享内存块，通过shmat将共享内存映射到自己的内存空间，然后就可以读写了。





### man

  对于man，将各类命令分为9个板块：

| 板块 | 含义                                 |
| ---- | ------------------------------------ |
| 1    | 可执行文件或者shell命令              |
| 2    | 系统调用（内核提供的函数）           |
| 3    | 库函数                               |
| 4    | 特别的文件（通常在/dev）             |
| 5    | 文件格式，如/etc/passwd              |
| 6    | 游戏                                 |
| 7    | 杂项（包括宏包）                     |
| 8    | 系统管理员命令（通常给root用户使用） |
| 9    | 内核例程（非标准例程）               |

对于像open,kill这种既有命令,又有系统调用的来说,man open则显示的是open(1),也就是从最前面的section开始,如果想查看open系统调用的话,就得man 2 open



























