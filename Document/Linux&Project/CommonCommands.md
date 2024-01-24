## 常用命令
接着我们来看一下Linux系统中一些比较常用的命令

### 文件操作
文件是最基本的内容 我们可以使用ls命令列出当前目录中所有的文件 参数-a表示包含所有的隐藏文件 -l表示列出详细信息:

```shell
                        test@ubuntu-server:~$ ls -al
                        total 44
                        drwxr-xr-x 4 test test 4096 Jan 24 08:55 .
                        drwxr-xr-x 4 root root 4096 Jan 24 04:24 ..
                        -rw------- 1 test test 2124 Jan 24 04:29 .bash_history
                        -rw-r--r-- 1 test test  220 Feb 25  2020 .bash_logout
                        -rw-r--r-- 1 test test 3771 Feb 25  2020 .bashrc
                        drwx------ 2 test test 4096 Jan 21 15:48 .cache
                        drwx------ 3 test test 4096 Jan 23 14:49 .config
                        -rw-r--r-- 1 test test  807 Feb 25  2020 .profile
                        -rw------- 1 test test   34 Jan 24 04:17 .python_history
                        -rw-r--r-- 1 test test    0 Jan 21 15:52 .sudo_as_admin_successful
                        -rw------- 1 test test 7201 Jan 24 08:55 .viminfo
```

可以看到当前目录下的整个文件列表 那么这些信息各种代表什么意思呢 尤其是最前面那一栏类似于drwxr-xr-x的字符串

它表示文件的属性 其中第1个字符表示此文件的类型:
`-`表示普通文件 `l`为链接文件 `d`表示目录(文件夹) `c`表示字符设备, `b`表示块设备 还有`p`有名管道, `f`堆栈文件, `s`套接字等, 这些一般都是用于进程之间通信使用的

第2-4个字符表示文件的拥有者(User)对该文件的权限 第5-7个字符表示文件所属用户组(Group)内用户对该文件的权限 最后8-10个字符表示其他用户(Other)对该文件的权限
其中`r`为读权限, `w`为写权限, `x`为执行权限 为了方便记忆 直接记UGO就行了

比如`drwxr-xr-x`就表示这是一个目录 文件的拥有者可以在目录中读, 写和执行 而同一用户组的其他用户只能读和执行 其他用户也是一样

第二栏数据可以看到是一列数字 它表示文件创建的链接文件(快捷方式)数量 一般只有1表示只有当前文件 我们也可以尝试创建一个链接文件:

```shell
                        test@ubuntu-server:~$ ln .bash_logout kk
```
创建后 会生成一个名为kk的文件 我们对此文件的操作相当于直接操作.bash_logout 跟Windows中的快捷方式比较类似 了解一下即可 再次执行ll命令 可以看到.bash_logout的链接数变成了2

第三栏数据为该文件或是目录的拥有者

第四栏数据表示所属的组

第五栏数据表示文件大 以字节为单位

第六栏数据为文件的最后一次修改时间

最后一栏就是文件名称了 就不多说了 再次提及..表示上级目录 .表示当前目录 最前面有一个.开头的文件为隐藏文件 可以看到上级目录(也就是/home目录)所有者为root
并且非root用户无法进行写操作 只能执行读操作 而当前目录以及目录下所有文件则属于test用户 test用户可以随意进行修改

在了解了Linux的文件查看之后再去看Windows的文件管理 会觉得Windows的太拉了:

<img src="https://image.itbaima.cn/markdown/2023/03/06/Bi1IrRQwhvGAHPq.jpg"/>

那么 如果我们希望对文件的属性进行修改 比如我们现在希望将某个文件的写权限给关闭 可以使用`chmod`命令来进行文件属性修改
我们先创建一个test文件 使用`touch`命令来创建文件 使用`mkdir`命令来创建目录

```shell
                        test@ubuntu-server:~$ touch test
                        test@ubuntu-server:~$ ll test
                        -rw-rw-r-- 1 test test 0 Jan 24 09:32 test
```

可以看到文件创建之后的默认权限为可读可写 接着我们来将其修改为只读 chmod的使用方法如下:
- chmod (u/g/o/a)(+/-)(r/w/x)文件名称

我们可以从ugo中选择或是直接a表示所有 +和-表示添加和删除权限 最后rwx不用我说了吧

```shell
                        test@ubuntu-server:~$ chmod a-w test
                        test@ubuntu-server:~$ ll test
                        -r--r--r-- 1 test test 0 Jan 24 09:32 test
```

除了这种方式之外 我们也可以使用数字来代替 比如现在我要给前两个添加读权限 那么:

约定: r=4, w=2, x=1 需要什么权限就让对应权限的数字相加 一个数字表示一个rwx的权限状态 比如我们想修改为`-rw-rw-r--` 那么对应的数字就是`664` 对应的命令为:

```shell
                        test@ubuntu-server:~$ chmod 664 test
                        test@ubuntu-server:~$ ll test
                        -rw-rw-r-- 1 test test 0 Jan 24 09:32 test
```

如果我们想修改文件的拥有者或是所属组 可以使用`chown`和`chgrp`命令:

```shell
                        test@ubuntu-server:~$ sudo chown root test 
                        test@ubuntu-server:~$ ls -l
                        total 0
                        -rw-rw-r-- 1 root test 0 Jan 24 10:43 test
                        test@ubuntu-server:~$ sudo chgrp root test 
                        test@ubuntu-server:~$ ls -l
                        total 0
                        -rw-rw-r-- 1 root root 0 Jan 24 10:43 test
```

再次操作该文件 会发现没权限:

```shell
                        test@ubuntu-server:~$ chmod 777 test 
                        chmod: changing permissions of 'test': Operation not permitted
```

接着我们来看文件的复制, 移动和删除 这里我们先创建一个新的目录并进入到此目录用于操作:

```shell
                        test@ubuntu-server:~$ mkdir study
                        test@ubuntu-server:~$ cd study
                        test@ubuntu-server:~/study$
```

首先我们演示文件的复制操作 文件的复制使用`cp`命令 比如现在我们想把上一级目录中的test文件复制到当前目录中:

```shell
                        test@ubuntu-server:~/study$ cp ../test test
                        test@ubuntu-server:~/study$ ls
                        test
```

那么如果我们想要将一整个目录进行复制呢? 我们需要添加一个`-r`参数表示将目录中的文件递归复制:

```shell
                        test@ubuntu-server:~/study$ cd ~
                        test@ubuntu-server:~$ cp -r study study_copied
                        test@ubuntu-server:~$ ls -l
                        total 8
                        drwxrwxr-x 2 test test 4096 Jan 24 10:16 study
                        drwxrwxr-x 2 test test 4096 Jan 24 10:20 study_copied
                        -rw-rw-r-- 1 test test    0 Jan 24 09:32 test
```

可以看到我们的整个目录中所有的文件也一起被复制了

接着我们来看看移动操作 相当于是直接将一个文件转移到另一个目录中了 我们再创建一个目录用于文件的移动 并将test文件移动到此目录中 我们使用`mv`命令进行文件的移动:

```shell
                        test@ubuntu-server:~$ mkdir study2
                        test@ubuntu-server:~$ mv test study2
                        test@ubuntu-server:~$ ls
                        study  study2  study_copied
                        test@ubuntu-server:~$ cd study2
                        test@ubuntu-server:~/study2$ ls
                        test
```

现在我们想要移动个目录到另一个目录中 比如我们想将study目录移动到study2目录中:

```shell
                        test@ubuntu-server:~$ mv study study2
                        test@ubuntu-server:~$ ls
                        study2  study_copied
                        test@ubuntu-server:~$ cd study2
                        test@ubuntu-server:~/study2$ ls
                        study  test
```

`mv`命令不仅能实现文件的移动 还可以实现对文件重命名操作 比如我们想将文件test重命名为yyds 那么直接将其进行移动操作即可:

```shell
                        test@ubuntu-server:~/study2$ ls
                        study  test
                        test@ubuntu-server:~/study2$ mv test yyds
                        test@ubuntu-server:~/study2$ ls
                        study  yyds
```

最后就是删除命令了 使用`rm`进行删除操作 比如现在我们想删除study2目录(注意需要添加-r参数表示递归删除文件夹中的内容):

```shell
                        test@ubuntu-server:~$ rm -r study2
                        test@ubuntu-server:~$ ls
                        study_copied
```

而最常提到的`rm -rf /*`正是删除根目录下所有的文件(非常危险的操作) -f表示忽略不存在的文件 不进行任何提示 *是一个通配符 表示任意文件 这里我们演示一下删除所有.txt结尾的文件:

```shell
                        test@ubuntu-server:~$ touch 1.txt 2.txt 3.txt
                        test@ubuntu-server:~$ ls
                        1.txt  2.txt  3.txt
                        test@ubuntu-server:~$ rm *.txt
                        test@ubuntu-server:~$ ls
                        test@ubuntu-server:~$
```

最后我们再来看文件的搜索 我们使用find命令来进行搜索 比如我想搜索/etc目录下名为passwd的文件:

```shell
                        test@ubuntu-server:~$ sudo find /etc -name passwd
                        [sudo] password for test: 
                        /etc/pam.d/passwd
                        /etc/passwd
```

它还支持通配符 比如搜索以s开头的文件:

```shell
                        test@ubuntu-server:~$ sudo find /etc -name s*
                        /etc/subuid
                        /etc/screenrc
                        /etc/sensors3.conf
                        /etc/sysctl.conf
                        /etc/sudoers
                        /etc/shadow
                        /etc/skel
                        /etc/pam.d/su
                        /etc/pam.d/sshd
                        /etc/pam.d/sudo
                        ...
```

### 系统管理
接着我们来查看一些系统管理相关的命令 比如我们Windows中的任务管理器 我们可以使用`top`命令来打开:

                    top - 10:48:46 up  5:52,  1 user,  load average: 0.00, 0.00, 0.00
                    Tasks: 191 total,   2 running, 189 sleeping,   0 stopped,   0 zombie
                    %Cpu(s):  0.0 us,  0.2 sy,  0.0 ni, 99.8 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
                    MiB Mem :   3919.1 total,   2704.2 free,    215.0 used,    999.9 buff/cache
                    MiB Swap:   3923.0 total,   3923.0 free,      0.0 used.   3521.4 avail Mem
                    
                        PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                       
                        10528 test      20   0    8944   3072   2652 R   0.7   0.1   0:00.07 top                           
                        9847 root      20   0       0      0      0 I   0.3   0.0   0:00.87 kworker/0:0-events            
                        1 root      20   0  102760  10456   7120 S   0.0   0.3   0:02.02 systemd                       
                        2 root      20   0       0      0      0 S   0.0   0.0   0:00.01 kthreadd                      
                        3 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_gp                        
                        4 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_par_gp                    
                        6 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/0:0H-kblockd          
                        8 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 mm_percpu_wq                  
                        9 root      20   0       0      0      0 S   0.0   0.0   0:00.15 ksoftirqd/0                   
                        10 root      20   0       0      0      0 R   0.0   0.0   0:01.49 rcu_sched                     
                        11 root      rt   0       0      0      0 S   0.0   0.0   0:00.24 migration/0                   
                        12 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/0                 
                        14 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/0                       
                        15 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/1                       
                        16 root     -51   0       0      0      0 S   0.0   0.0   0:00.00 idle_inject/1                 
                        17 root      rt   0       0      0      0 S   0.0   0.0   0:00.30 migration/1                   
                        18 root      20   0       0      0      0 S   0.0   0.0   0:00.07 ksoftirqd/1                   
                        20 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/1:0H-kblockd

可以很清楚地看到当前CPU的使用情况以及内存的占用情况

按下数字键1 可以展示所有CPU核心的使用情况:

                        %Cpu0  :  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
                        %Cpu1  :  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st

按下f键可以设置以哪一列进行排序或是显示那些参数:

                        Fields Management for window 1:Def, whose current sort field is %MEM
                            Navigate with Up/Dn, Right selects for move then <Enter> or Left commits,
                            'd' or <Space> toggles display, 's' sets sort.  Use 'q' or <Esc> to end!

按下q键即可退出监控界面

我们可以直接输入free命令来查看当前系统的内存使用情况:

```shell
                        test@ubuntu-server:~$ free -m
                                      total        used        free      shared  buff/cache   available
                        Mem:           3919         212        2706           1         999        3523
                        Swap:          3922           0        3922
```

其中-m表示以M为单位 也可以-g表示以G为单位 默认是kb为单位

最后就是磁盘容量 我们可以使用`lsblk`来查看所有块设备的信息 其中就包括我们的硬盘, 光驱等:

```shell
                        test@ubuntu-server:~$ lsblk
                        NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
                        loop0                       7:0    0 48.9M  1 loop /snap/core18/2127
                        loop1                       7:1    0 28.1M  1 loop /snap/snapd/12707
                        loop2                       7:2    0   62M  1 loop /snap/lxd/21032
                        sr0                        11:0    1 1024M  0 rom  
                        nvme0n1                   259:0    0   20G  0 disk 
                        ├─nvme0n1p1               259:1    0  512M  0 part /boot/efi
                        ├─nvme0n1p2               259:2    0    1G  0 part /boot
                        └─nvme0n1p3               259:3    0 18.5G  0 part 
                          └─ubuntu--vg-ubuntu--lv 253:0    0 18.5G  0 lvm  /
```

可以看到nvme开头的就是我们的硬盘(这个因人而异 可能你们的是sda 磁盘类型不同名称就不同)可以看到`nvme0n1`容量为20G
并且512M用作存放EFI文件 1G存放启动文件 剩余容量就是存放系统文件和我们的用户目录

这里要提到一个挂载的概念:

    挂载 指的就是将设备文件中的顶级目录连接到Linux根目录下的某一目录(最好是空目录) 访问此目录就等同于访问设备文件

比如我们的主硬盘 挂载点就被设定为/根目录 而我们所有保存的文件都会存储在硬盘中 如果你有U盘(最好将U盘的文件格式改为ExFat
可以直接在Windows中进行格式化 然后随便放入一些文件即可)之类的东西 我们可以演示一下对U盘进行挂载:

```shell
                        test@ubuntu-server:~$ sudo fdisk -l
                        ...
                        Disk /dev/sda: 60 GiB, 64424509440 bytes, 125829120 sectors
                        Disk model: USB DISK        
                        Units: sectors of 1 * 512 = 512 bytes
                        Sector size (logical/physical): 512 bytes / 512 bytes
                        I/O size (minimum/optimal): 512 bytes / 512 bytes
                        Disklabel type: dos
                        Disk identifier: 0x4a416279
                        
                        Device     Boot     Start       End   Sectors  Size Id Type
                        /dev/sda1  *       614400 125214719 124600320 59.4G  7 HPFS/NTFS/exFAT
                        /dev/sda2       125214720 125825022    610303  298M  6 FAT16
```

将U盘插入电脑 选择连接到Linux 输入`sudo fdisk -l`命令来查看硬盘实体情况 可以看到有一个USB DISK设备 注意观察一下是不是和自己的U盘容量一致 可以看到设备名称为`/dev/sda1`

接着我们设备挂载到一个目录下:

```shell
                        test@ubuntu-server:~$ mkdir u-test
                        test@ubuntu-server:~$ sudo mount /dev/sda1 u-test/
                        test@ubuntu-server:~$ cd u-test/
                        test@ubuntu-server:~/u-test$ ls
                         CGI
                         cn_windows_10_enterprise_ltsc_2019_x64_dvd_9c09ff24.iso
                         cn_windows_7_professional_x64_dvd_x15-65791.iso
                         cn_windows_8.1_enterprise_with_update_x64_dvd_6050374.iso
                         cn_windows_8.1_professional_vl_with_update_x64_dvd_4050293.iso
                         cn_windows_server_2019_updated_july_2020_x64_dvd_2c9b67da.iso
                        'System Volume Information'
                         zh-cn_windows_10_consumer_editions_version_21h1_updated_sep_2021_x64_dvd_991b822f.iso
                         zh-cn_windows_11_consumer_editions_x64_dvd_904f13e4.iso
```

最后进入到此目录中 就能看到你U盘中的文件了 如果你不想使用U盘了 可以直接取消挂载:

```shell
                        test@ubuntu-server:~/u-test$ cd ..
                        test@ubuntu-server:~$ sudo umount /dev/sda1
```

最后我们可以通过`df`命令查看当前磁盘使用情况:

```shell
                        test@ubuntu-server:~$ df -m
                        Filesystem                        1M-blocks  Used Available Use% Mounted on
                        udev                                   1900     0      1900   0% /dev
                        tmpfs                                   392     2       391   1% /run
                        /dev/mapper/ubuntu--vg-ubuntu--lv     18515  6544     11009  38% /
                        tmpfs                                  1960     0      1960   0% /dev/shm
                        tmpfs                                     5     0         5   0% /run/lock
                        tmpfs                                  1960     0      1960   0% /sys/fs/cgroup
                        /dev/nvme0n1p2                          976   109       800  12% /boot
                        /dev/nvme0n1p1                          511     4       508   1% /boot/efi
                        /dev/loop0                               49    49         0 100% /snap/core18/2127
                        /dev/loop1                               29    29         0 100% /snap/snapd/12707
                        /dev/loop2                               62    62         0 100% /snap/lxd/21032
                        tmpfs                                   392     0       392   0% /run/user/1000
```

输入`ps`可以查看当前运行的一些进程 其实和top有点类似 但是没有监控功能 只能显示当前的

```shell
                        test@ubuntu-server:~$ ps
                              PID TTY          TIME CMD
                            11438 pts/0    00:00:00 bash
                            11453 pts/0    00:00:00 ps
```

添加-ef查看所有的进程:

```shell
                        test@ubuntu-server:~$ ps -ef
                        UID          PID    PPID  C STIME TTY          TIME CMD
                        root           1       0  0 04:55 ?        00:00:02 /sbin/init
                        root           2       0  0 04:55 ?        00:00:00 [kthreadd]
                        root           3       2  0 04:55 ?        00:00:00 [rcu_gp]
                        root           4       2  0 04:55 ?        00:00:00 [rcu_par_gp]
                        root           6       2  0 04:55 ?        00:00:00 [kworker/0:0H-kblockd]
                        ...
```

我们可以找到对应的进程ID(PID) 使用kill命令将其强制终止:

```shell
                        test@ubuntu-server:~$ ps
                            PID TTY          TIME CMD
                          11438 pts/0    00:00:00 bash
                          11455 pts/0    00:00:00 ps
                        test@ubuntu-server:~$ kill -9 11438
                        Connection to 192.168.10.6 closed.
```

比如我们可以将当前会话的bash给杀死 那么会导致我们的连接直接断开 其中-9是一个信号 表示杀死进程:
- 1(HUP): 重新加载进程
- 9(KILL): 杀死一个进程
- 15(TERM): 正常停止一个进程

最后如果我们想要正常关机 只需要输入shutdown即可 系统会创建一个关机计划 并在指定时间关机 或是添加now表示立即关机:

```shell
                        test@ubuntu-server:~$ sudo shutdown
                        [sudo] password for test: 
                        Shutdown scheduled for Mon 2022-01-24 11:46:18 UTC, use 'shutdown -c' to cancel.
                        test@ubuntu-server:~$ sudo shutdown now
                        Connection to 192.168.10.6 closed by remote host.
                        Connection to 192.168.10.6 closed.
```

### 压缩解压
比较常用的压缩和解压也是重点 我们在Windows中经常需要下载一些压缩包 并且将压缩包解压才能获得里面的文件 而Linux中也支持文件的压缩和解压

这里我们使用`tar`命令来完成文件压缩和解压操作 在Linux中比较常用的是gzip格式 后缀名一般为.gz tar命令的参数-c表示对文件进行压缩 创建新的压缩文件
-x表示进行解压操作 -z表示以gzip格式进行操作 -v可以在处理过程中输出一些日志信息 -f表示对普通文件进行操作 这里我们创建三个文件并对这三个文件进行打包:

```shell
                        test@ubuntu-server:~$ tar -zcvf test.tar.gz *.txt
                        1.txt
                        2.txt
                        3.txt
                        test@ubuntu-server:~$ ls
                        1.txt  2.txt  3.txt  test.tar.gz
                        test@ubuntu-server:~$
```

接着我们删除刚刚三个文件 再执行解压操作 得到压缩包中文件:

```shell
                        test@ubuntu-server:~$ rm *.txt
                        test@ubuntu-server:~$ ls
                        test.tar.gz
                        test@ubuntu-server:~$ tar -zxvf test.tar.gz 
                        1.txt
                        2.txt
                        3.txt
                        test@ubuntu-server:~$ ls
                        1.txt  2.txt  3.txt  test.tar.gz
```

同样的 我们也可以对一个文件夹进行打包:

```shell
                        test@ubuntu-server:~$ mv *.txt test
                        test@ubuntu-server:~$ tar -zcvf test.tar.gz test/
                        test/
                        test/1.txt
                        test/2.txt
                        test/3.txt
                        test@ubuntu-server:~$ rm -r test
                        test@ubuntu-server:~$ ls
                        test.tar.gz 
                        test@ubuntu-server:~$ tar -zxvf test.tar.gz 
                        test/
                        test/1.txt
                        test/2.txt
                        test/3.txt
                        test@ubuntu-server:~$ ls
                        test  test.tar.gz
                        test@ubuntu-server:~$ ls test
                        1.txt  2.txt  3.txt
```

到此 Linux的一些基本命令就讲解为止