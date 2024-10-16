# SilSys Development Kit Live ISO
This is a proof of concept of building a Linux Distribution targeted for developing games, homebrew software and debugging
in a wide range of paltform, especially the vintage or decade old Video Game Consoles. SSDK, or SilSys Development Kit is a 
Debian-based live distribution that includes development kit, weird compilers and interpreters and other interesting programs. This can 
be run using bare-metal (your actual laptop/PC) and Virtualization.

This distribution includes open source Software Development Kits (SDK) intended for creating homebrew softwares for any of 
the PlayStation, Sega and Nintendo systems. This allows you to create applications, games, and more (homebrew) to run on 
consoles. These SDKs does not mean to work perfectly without any issues, so the user needs to know not only the basics of 
Linux troubleshooting but have a background with programming language, mostly C based.

But unfortunately, even if there are toolchains for each platform, there are missing libraries and third party software 
dependencies on some of the platform because the development is immature, issues on building it, or sometimes there is no 
open source library exist, so developing games is impossible unless you provide them. Some of the platform have complete set 
of development kit to create full functional games, notably for the NES, GBA and others. And because most of the software 
for development, patchers, editors, utilities, graphics, etc. are released only for Windows systems, installing free and open 
source alternative softwares can be painful to us as it requires you to build that software from source, configure it’s 
variables and compile them.

# Download
You can download packed file containing ISO and documentation, or just documentation here:

### Download only SSDK documentation

**Google Drive** | https://drive.google.com/file/d/10uLYB5o7rKGByWG8KUSKZzvlcV8Vs86J/view

**Academia** | https://www.academia.edu/124710314/SSDK_0_1_Documentation

### Packed ISO and documentation

**Google Drive** | https://drive.google.com/file/d/10kzPzPqTEFTEnfmWGGRaPwCNZLcPMVqI/view?usp=sharing
> [!NOTE]
> This ISO alone is over 10GB as many tools and utilities related to game development is packed in this distribution, plus the standard Debian applications is also included. For slow internet speed, use your preferred download manager like Persepolis Download Manager to handle large file without interruption. If you are using metered internet service and don't want to waste every kilobyte you have, just wait for my upcoming release of compiled SDK and utilities just for you.

Instead of running this in your actual machine or virtual machine, you can just use the `filesystem.squashfs` inside the distribution ISO within your existing installed Linux distribution through the help of chroot command. But there are two choices; read-write mode by copying the entire filesystem, or read-only by just mounting the filesystem as the fastest method.


## 1st Choice: Copy squashfs contents (very slow process depending on storage device, can support Read-write mode)
If you prefer to use the filesystem as read-write, then this method is for you. This method actually uncompress the `filesystem.squashfs`, creating an entire copy of system into your target directory. To do this:
1. Make sure that your distribution already has `squashfs-tools` installed. If you don't have one, install it. I'm sure you already know how to do it.
2. Mount the ISO. In any file manager in your linux distro, right click the ISO file and select **_Disk Image Mounter_**.
3. Click the sblive at the mounted devices. Go to `Live` folder, and look for `filesystem.squashfs`.
4. We need to copy the squashfs file into empty directory. You may choose what directory, but I suggest it should be in mnt directory. To copy it; first, open your terminal and elevate into root. Then execute this one-by-one:
```
mkdir -v mnt/ssdk
cp /path/to/filesystem.squashfs /mnt/ssdk
cd /mnt/ssdk
```
where `mkdir -v ssdk` creates a directory within `mnt` directory, and in `cp`, you should change the `/path/to/filesystem.squashfs` into your directory path where `filesystem.squashfs` is located. This copies the squashfs file into `/mnt/ssdk` directory.

5. Extract the entire system by:
```
unsquashfs filesystem.squashfs
```
and that's it. While you're waiting, boil your water and make your favorite coffee.


## 2nd Choice: Mount squashfs (Fastest way but it is Read-only mode)
You can mount squashfs of this distribution without copying this whole filesystem contents as the real size of the filesystem against the compressed squashfs is over **40GB**! But the only disadvantage is that this is just a read-only, meaning you cannot modify the contents of the filesystem, nor can launch GUI apps. To do this:

1. Mount the ISO. In any file manager in your linux distro, right click the ISO file and select **_Disk Image Mounter_**.
2. Click the sblive at the mounted devices. Go to `Live` folder, and look for `filesystem.squashfs`.
3. Now, open the terminal by right clicking the area, navigating through three dot icon, file or options depending on what file manager you are using then elevate into root. If you are not in a directory where `filesystem.squashfs` resides, just do `cd`. Usually the mounted ISO file is located in `/media/username` where `username` is your actual user's account.

4. Create a folder into `mnt` directory, any name you may like as we will put the contents of the `filesystem.squashfs` into that folder, even **dick** or **vagina** will do. To do this, execute:
```
mkdir -v foldername
```
where `-v` outputs the result of the created folder, and `foldername` is your chosen folder name.


5. Now mount the `filesystem.squashfs` into `foldername`. Just execute:
```
mount -v -o loop -t squashfs filesystem.squashfs /mnt/foldername
```
where `-v` output results, `-o` is an option with escalted privileges, and `t` is a filesystem type. To see if the `filesystem.squashfs` is actually mounted on `/mnt/foldername`, navigate it using your file manager.


Anyway, the mounted `filesystem.squashfs` can be chrooted to use SSDK environment and all of the tools easily.

## Chroot into SDK
Now, you want to setup SSDK environment in order to execute commands without symlinking or setting up `PATH` variables to directories where the toolchains and utilities resides. This step requires to mount virtual filesystem that is part of Linux kernel and entering SSDK environment so that you can do anything you want without affecting your main host distribution.

1. As root, create an environment variable `SSDK` as our shortcut instead of typing full directory name which can consume much time. To do that, execute
```
export SSDK=/mnt/foldername
```
where `foldername` is the name of your folder that you did in [Mount squashfs](#mount-squashfs) setion. To check if it takes effect, either look for the long-list
```
env
```
command, or just
```
echo $SSDK
```
> [!CAUTION]
> IF YOU DID NOT CHECK THE SSDK VARIABLE AND EXECUTE THESE REMAINING STEPS, IT MAY TAMPER YOUR SYSTEM AND CAN CAUSE SEVERE ISSUES

2. Now, mount the `/dev`  by executing:
```
mount -v --bind /dev $SSDK/dev
```
3. Mount the virtual filesystems by executing these following one by one:
```
mount -vt devpts devpts -o gid=5,mode=0620 $SSDK/dev/pts
mount -vt proc proc $SSDK/proc
mount -vt sysfs sysfs $SSDK/sys
mount -vt tmpfs tmpfs $SSDK/run
```

4. Then copy and execute this script as a whole:
```
if [ -h $SSDK/dev/shm ]; then
  install -v -d -m 1777 $SSDK$(realpath /dev/shm)
else
  mount -vt tmpfs -o nosuid,nodev tmpfs $SSDK/dev/shm
fi
```

5. Then, execute this to enter to Chroot environment:
```
chroot "$SSDK" /usr/bin/env -i                \
    HOME=/root                                \
    TERM="$TERM"                              \
    PS1='(SSDK virtual environment) \u:\w\$ ' \
    PATH=/usr/bin:/usr/sbin                   \
    /bin/bash --login
```
> [!NOTE]
> You don't need to manually add the environment variable that is stored in `.profile` if you just chroot the squashfs as it automatically adds them; unlike if you boot the ISO, it requires you to export the `.profile` into environment.

> [!NOTE]
> You can only execute text-based programs (binaries) in a chrooted environment as running a GUI-based program is impossible as the squashfs is readonly and Xserver needs to be read-write mode.

# Back to usual Host system
In order to use your host environment, `exit` into chroot environment. Also unmounting virtual filesystem and squashfs can be done automatically by the system when you perform shutdown, or unmount them by yourself if you want to remove them immediately.

# Contact Details
If you found this distribution useful and you are interested in this concept, you can message me at:

Facebook | https://www.facebook.com/NishizawaNobunacchi

X (Japan) | https://x.com/nishizawanobun

note (Japan) | https://note.com/unix_nishizawa

Google Mail | jamesemiliano74@gmail.com

GitHub | https://github.com/ColtonSilvaonKnoxKontor

Gitee (China) | https://gitee.com/ColtonSilvaonKnoxKontor
