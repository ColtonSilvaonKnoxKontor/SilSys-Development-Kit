# SilSys 开发套件 Live ISO
这是一个概念验证，旨在构建一个Linux发行版，用于在各种平台上开发游戏、自制软件和调试，特别是老式或十年前的视频游戏机。SSDK或SilSys Development Kit是一个基于Debian的实时发行版，其中包括开发工具包、奇怪的编译器和解释器以及其他有趣的程序。这可以使用裸机（您的实际笔记本电脑/PC）和虚拟化来运行。

此发行版包括开源软件开发工具包（SDK），旨在为任何PlayStation、Sega和Nintendo系统创建自制软件。这允许您创建应用程序、游戏等（自制程序）以在游戏机上运行。这些SDK并不意味着可以完美地工作而不会出现任何问题，因此用户不仅需要了解Linux故障排除的基础知识，还需要具备编程语言（主要是基于C的编程语言）的背景知识。

但不幸的是，即使每个平台都有工具链，但由于开发不成熟、构建问题或有时不存在开源库，一些平台上也缺少库和第三方软件依赖关系，因此除非你提供，否则开发游戏是不可能的。一些平台有一套完整的开发工具包来创建全功能游戏，特别是为NES、GBA和其他游戏。由于大多数用于开发、补丁、编辑器、实用程序、图形等的软件仅针对Windows系统发布，因此安装免费和开源的替代软件对我们来说可能很痛苦，因为它要求您从源代码构建该软件，配置其变量并编译它们。

> [!NOTE]
> 这个Linux发行版的默认语言是英语，它只支持日语输入系统。请安装支持简体中文的mozc

# 下载
您可以下载包含ISO和文档的打包文件，或者在这些西方网站上下载文档：

### 仅下载SSDK文档

**谷歌云端硬盘** | https://drive.google.com/file/d/10uLYB5o7rKGByWG8KUSKZzvlcV8Vs86J/view

**学术界**  |https://www.academia.edu/124710314/SSDK_0_1_Documentation

### 打包的ISO和文件

**谷歌云端硬盘** | https://drive.google.com/file/d/10kzPzPqTEFTEnfmWGGRaPwCNZLcPMVqI/view?usp=sharing

如果您的互联网服务提供商阻止了对西方服务（谷歌、学术界或任何）的任何访问，请使用您首选的VPN

> [!NOTE]
> 仅此ISO就超过10GB，因为此发行版中包含了许多与游戏开发相关的工具和实用程序，此外还包括标准的Debian应用程序。对于较慢的互联网速度，请使用您喜欢的下载管理器，如Persepolis download manager，来处理大文件而不会中断。如果您正在使用计量互联网服务，并且不想浪费您拥有的每千字节，请等待我即将发布的专门为您编译的SDK和实用程序。

您可以通过chroot命令的帮助，在现有安装的Linux发行版中使用发行版ISO中的`filesystem.squashfs`，而不是在实际的机器或虚拟机中运行它。但有两种选择；Read-Write模式，复制整个文件系统，或Read-only模式，以最快的方式挂载文件系统。

## 第一选择：复制squashfs内容（根据存储设备的不同，过程非常缓慢，可以支持[读写]模式）
如果您更喜欢将文件系统用作读写，那么这种方法适合您。此方法实际上解压缩了`filesystem.squashfs`，将系统的整个副本创建到目标目录中。为此，请执行以下操作：

1.确保您的发行版已经安装了`squashfs-tools`。如果你没有，就安装它。我相信你已经知道怎么做了。

2.安装ISO。在linux发行版的任何文件管理器中，右键单击ISO文件并选择**_Disk Image Mounter_**。

3.单击已安装设备上的sblive。转到“Live”文件夹，查找 `filesystem.squashfs`。

4.我们需要将squashfs文件复制到空目录中。您可以选择哪个目录，但我建议它应该在mnt目录中。复制它；首先，打开终端并提升到根。然后逐一执行：

```
mkdir -v mnt/ssdk
cp /path/to/filesystem.squashfs /mnt/ssdk
cd /mnt/ssdk
```
其中`mkdir-v ssdk`在`mnt`目录中创建一个目录，在`cp`中，您应该将`/path/to/filesystem.squashfs`更改为`filesystem.suashfs`所在的目录路径。这会将squashfs文件复制到`/mnt/ssdk`目录中。

5.通过以下方式提取整个系统：
```
unsquashfs filesystem.squashfs
```
就是这样。等的时候，把水烧开，煮你最喜欢的咖啡。

## ##第二种选择：挂载squashfs（最快的方式，但它是Read-only模式）
您可以挂载此发行版的squashfs，而无需复制整个文件系统内容，因为文件系统相对于压缩squashfs的实际大小超过**40GB**！但唯一的缺点是这只是一个Read-only，这意味着您不能修改文件系统的内容，也不能启动GUI应用程序。为此，请执行以下操作：

1.安装ISO。在linux发行版的任何文件管理器中，右键单击ISO文件并选择**_Disk Image Mounter_**。

2.单击已安装设备上的sblive。转到`Live`文件夹，查找`filesystem.squashfs`。

3.现在，通过右键单击该区域打开终端，根据您使用的文件管理器浏览三点图标、文件或选项，然后提升到根目录。如果你不在`filesystem.squashfs`所在的目录中，只需执行`cd`即可。通常，挂载的ISO文件位于`/media/username`中，其中`username`是您实际用户的帐户。

4.在`mnt`目录中创建一个文件夹，您可以选择任何名称，因为我们会将`filesystem.squashfs`的内容放入该文件夹，即使是**dick**或**vagina**也可以。为此，请执行：
```
mkdir -v foldername
```
其中`-v`输出创建文件夹的结果，`foldername`是您选择的文件夹名称。


5.现在将`filesystem.squashfs`挂载到`foldername`中。只需执行：
```
mount-v-o loop-t squashfs文件系统.ssquashfs/mnt/foldername
```
其中`-v`输出结果，`-o`是具有escalted权限的选项，`t`是文件系统类型。要查看`filesystem.squashfs`是否实际挂载在`/mnt/foldername`上，请使用文件管理器进行导航。


无论如何，挂载的`filesystem.squashfs`可以通过chroot轻松使用SSDK环境和所有工具。

## Chroot进入SDK
现在，您需要设置SSDK环境，以便在没有符号链接或将“PATH”变量设置到工具链和实用程序所在的目录的情况下执行命令。此步骤需要挂载作为Linux内核一部分的虚拟文件系统并进入SSDK环境，以便您可以在不影响主主机分布的情况下做任何事情。

1.作为root用户，创建一个环境变量“SSDK”作为我们的快捷方式，而不是键入完整的目录名，这会消耗很多时间。为此，请执行
```
export SSDK=/mnt/foldername
```
其中`foldername`是您在 [Mountsquashfs](#mountsquashfs)设置中创建的文件夹的名称。要检查它是否生效，请查看长列表
```
env
```
命令，或者只是
```
echo $SSDK
```
> [!CAUTION]
> 如果您没有检查SSDK变量并执行这些剩余步骤，它可能会篡改您的系统并导致严重问题

2.现在，通过执行以下命令挂载“/dev”：
```
mount -v --bind /dev $SSDK/dev
```

3.通过逐一执行以下操作来挂载虚拟文件系统：
```
mount -vt devpts devpts -o gid=5,mode=0620 $SSDK/dev/pts
mount -vt proc proc $SSDK/proc
mount -vt sysfs sysfs $SSDK/sys
mount -vt tmpfs tmpfs $SSDK/run
```

4.然后将此脚本作为一个整体复制并执行：
```
if [ -h $SSDK/dev/shm ]; then
  install -v -d -m 1777 $SSDK$(realpath /dev/shm)
else
  mount -vt tmpfs -o nosuid,nodev tmpfs $SSDK/dev/shm
fi
```

5.然后，执行以下操作进入Chroot环境：
```
chroot "$SSDK" /usr/bin/env -i                \
    HOME=/root                                \
    TERM="$TERM"                              \
    PS1='(SSDK virtual environment) \u:\w\$ ' \
    PATH=/usr/bin:/usr/sbin                   \
    /bin/bash --login
```

> [!Note]
> 如果你只是在squashfs自动添加的同时chroot它，你就不需要手动添加存储在`.profile `中的环境变量；与启动ISO不同，它要求您将`.profile`导出到环境中。

> [!Note]
> 您只能在chrooted环境中执行基于文本的程序（二进制文件），因为无法运行基于GUI的程序，因为squashfs是read-only的，Xserver需要处于read-write模式。

# 回到通常的主机系统
要使用您的主机环境，请“退出”到chroot环境。此外，当您执行关机操作时，系统可以自动卸载虚拟文件系统和squashfs，或者如果您想立即删除它们，可以自己卸载它们。

# 联系方式
如果你觉得这个发行版很有用，并且对这个概念感兴趣，你可以给我发邮件：

Facebook | https://www.facebook.com/NishizawaNobunacchi

X（日本）| https://x.com/nishizawanobun

note（日本）| https://note.com/unix_nishizawa

Google Mail | jamesemiliano74@gmail.com

GitHub | https://github.com/ColtonSilvaonKnoxKontor

Gitee（中国）| https://gitee.com/ColtonSilvaonKnoxKontor
