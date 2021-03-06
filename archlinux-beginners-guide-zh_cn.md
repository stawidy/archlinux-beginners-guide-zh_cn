# Arch Linux Beginners' guide (简体中文)
---
欢迎，本向导写给 Arch 新用户，但是会尽量做到成为所有用户的参考和信息库。 本文档指导您使用Arch安装脚本来安装Arch Linux：一个简单、轻量级、适合计算机水平较高用户使用的发行版。建议在安装前先浏览一下[FAQ](https://wiki.archlinux.org/index.php/Frequently_asked_questions_%28%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87%29)。 社区维护的 [ArchWiki](https://wiki.archlinux.org/index.php/Main_page_%28%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87%29)应该有办法解决遇到的疑难。若在其它地方找不到解决办法，[IRC 频道](irc://irc.freenode.net/#archlinux-cn) 和论坛都是求助的好地方。为了贯彻[Arch之道](https://wiki.archlinux.org/index.php/Arch_Linux_%28%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87%29)，如遇陌生的命令，可输入`man command`以查询相关`man`手册页。  
## 准备
---
理论上，Arch Linux能在任何内存空间不小于 256MB 的[i686](https://en.wikipedia.org/wiki/P6_(microarchitecture))兼容机上运行。最基本的[base](https://www.archlinux.org/groups/x86_64/base/)组中包含的包将占用约 800MB 存储空间。官方Arch Linux安装媒介的准备方法在[此分类](https://wiki.archlinux.org/index.php/Category:Getting_and_installing_Arch_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))中介绍，建议始终使用最新的 ISO 镜像。 

## 引导安装媒介
---
让系统从 Arch 安装介质中启动。大多现代操作系统允许您在[POST](https://en.wikipedia.org/wiki/Power-on_self_test)时手动设置引导设备，在开机屏幕中一般会显示需要的按键。进入BIOS设置界面后，修改设备引导顺序。把包含 Arch ISO 的设备设为系统引导首选。再选 "Save & Exit" （或您 BIOS 相应的选项），您的计算机接着应该会开始常规的引导流程了。  
当Arch菜单出现时，选 "Boot Arch Linux" 并按 `Enter`进入安装环境。[启动参数](https://wiki.archlinux.org/index.php/Kernel_parameters_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E9.85.8D.E7.BD.AE)页面提供了额外的参数修改。  
启动完成后会以 root 登录并进入[zsh](https://wiki.archlinux.org/index.php/Zsh_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))命令提示， [grml config](http://grml.org/zsh/)提供了额外的配置。编辑配置文件时可以使用 [nano](https://wiki.archlinux.org/index.php/Nano#Usage) 或 [vim](https://wiki.archlinux.org/index.php/Vim#Usage) 。 

### UEFI 模式
如果您使用 [UEFI](https://wiki.archlinux.org/index.php/Unified_Extensible_Firmware_Interface_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 主板，且 UEFI 启动模式（优于 BIOS/Legacy 模式）已启用，CD/USB 会自动通过[systemd-boot](http://www.freedesktop.org/wiki/Software/systemd/systemd-boot/) 启动 Arch Linux。要确认是否已进入UEFI模式，检查下面目录是否有文件：  
`# ls /sys/firmware/efi/efivars`  
详情参阅 [UEFI#UEFI Variables](https://wiki.archlinux.org/index.php/UEFI#UEFI_Variables) 。  
早期的设备对 UEFI 的支持并不规范，请通过主板，机型等关键字在网络和 Wiki 中进行搜索，看看有没有需要注意的事项。 

### 设置键盘布局
>提示: 此步骤只对该安装过程生效。  

[控制台键盘布局](https://wiki.archlinux.org/index.php/Keyboard_configuration_in_console) 默认为us（美式键盘映射）。如果您正在使用非[美式](https://en.wikipedia.org/wiki/File:KB_United_States-NoAltGr.svg)键盘布局，通过以下的命令选择相应的键盘映射表：  
```
# loadkeys layout
```  
将 layout 转换为您的键盘布局，如`fr`，`uk`，`dvorak`或`be-latin1`。这里有国家的二位字母编码表。使用命令`localectl list-keymaps`列出所有可用的键盘布局。  
要修改终端字体，请阅读 [Fonts (简体中文)](https://wiki.archlinux.org/index.php/Fonts_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)).

### 连接到因特网

#### 有线连接
安装程序会在启动时自动运行 [dhcpcd](https://wiki.archlinux.org/index.php/Dhcpcd) 守护进程以尝试有线连接。需要网页验证的网络可以通过[ELinks](https://wiki.archlinux.org/index.php/ELinks)浏览器进行登录。  
可以用 [ping 命令检查网络是否正常](https://wiki.archlinux.org/index.php/Network_configuration#Check_the_connection)，如果网络不可用，需要 [手动配置网络](https://wiki.archlinux.org/index.php/Network_Configuration_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))。下面用 [netctl](https://wiki.archlinux.org/index.php/Netctl) 做示例。  
为了防止冲突，首先停用 dhcpcd 服务，将 `enp0s25` 替换为正确的有线接口：  
```
# systemctl stop dhcpcd@enp0s25.service
```  
要保存设置，在配置基本系统之前将修改过的文件复制到新系统中。详见 。[Category:Network configuration](https://wiki.archlinux.org/index.php/Category:Network_configuration)包含了其它配置方法，比如拨号连接。  
[网络设备名](https://wiki.archlinux.org/index.php/Network_configuration#Device_names)可以通过 `ip link` 或 `iw dev`(无线网络)可以查到设备名称。通常以 `en` (ethernet), `wl` (WLAN) 或 `ww` (WWAN)开头。 

#### 无线网络连接
使用 [netctl](https://wiki.archlinux.org/index.php/Netctl) 的 wifi-menu 连接到[无线网络](https://wiki.archlinux.org/index.php/Wireless_network_configuration):  
```
# wifi-menu -o wlp2s0
```  
生成的配置文件位于 `/etc/netctl`中，对于需要用户名和密码的网络，请参考 [WPA2 Enterprise#netctl](https://wiki.archlinux.org/index.php/WPA2_Enterprise#netctl) 页面。  
如果网络还不可用，可以尝试手动配置网络，除了驱动，大多无线网卡还需要固件。按照 [检查驱动状态](https://wiki.archlinux.org/index.php/Wireless_network_configuration#Check_the_driver_status)中列出的方法设置无线固件。然后按照[手动设置](https://wiki.archlinux.org/index.php/Wireless_network_configuration#Manual_setup) 中的步骤配置网络。[iw](https://www.archlinux.org/packages/?name=iw) 和 [wpa_supplicant](https://www.archlinux.org/packages/?name=wpa_supplicant) 都已经位于安装介质中。 

#### 其它
有其它配置示例， [静态 IP 地址](https://wiki.archlinux.org/index.php/Network_configuration#Static_IP_address)，将 [netctl](https://wiki.archlinux.org/index.php/Netctl) 示例文件复制到 `/etc/netctl`：  
```
# cp /etc/netctl/examples/ethernet-static /etc/netctl
```  
然后启用配置:  
```
# netctl start ethernet-static
```  

### 更新系统时间
用 [systemd-timesyncd](https://wiki.archlinux.org/index.php/Systemd-timesyncd) 确保系统时间是正确的：  
```
# timedatectl set-ntp true
```  
用 `timedatectl status` 检查服务状态.详情阅读 [Time (简体中文)](https://wiki.archlinux.org/index.php/Time_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)). 

## 准备存储设备
---
>警告: 分区可能会毁掉原数据。强烈建议先备份重要的数据。  

此部分为新系统准备存储空间，更详细的介绍请阅读[Partitioning (简体中文)](https://wiki.archlinux.org/index.php/Partitioning_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))。  

* 如果您打算安装在 USB 上，请阅读 [Installing Arch Linux on a USB key](https://wiki.archlinux.org/index.php/Installing_Arch_Linux_on_a_USB_key).
* 如果您打算为 [LVM](https://wiki.archlinux.org/index.php/LVM_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))、[磁盘加密](https://wiki.archlinux.org/index.php/Disk_encryption) 或 [RAID](https://wiki.archlinux.org/index.php/RAID) 创建堆栈式块设备，请在此部分加入需要的步骤。
* 如果您想实现与 Windows 共存的双启动，在 UEFI/GPT 系统上不要格式化 UEFI 分区，因为这个分区包含 Windows .efi 启动文件。而且 Arch 应该按照同样的固件启动模式和本区组合。详见[Dual boot with Windows#Important information](https://wiki.archlinux.org/index.php/Dual_boot_with_Windows#Important_information).

### 识别设备
首先要确定系统安装的目标设备，下面命令会显示所有连接到系统的设备和分区状况：  
```
# lsblk
```  
结果中会包含 Arch 安装设备(例如 USB 安装盘)，不是所有设备都适合安装。磁盘设备名一般以 sda, sdb 的形式出现，如果设备上有分区，会以 sda1,sda2 的名称出现。`rom`, `loop` 或 `airoot` 格式的分区可以忽略。  
文章后面会用 `sdxY` 表示磁盘和分区，请按照您系统的实际状况修改命令。不要直接复制和执行。如果不需要重新分区，可以跳到 #创建文件系统，否则继续。 

### 分区工具
硬盘首先要[分区](https://wiki.archlinux.org/index.php/Partitioning_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))，接着将分区格式化为需要的[文件系统](https://wiki.archlinux.org/index.php/File_Systems_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))。  
有两种分区类型：  

* [GUID 分区表](https://wiki.archlinux.org/index.php/GUID_Partition_Table_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))(GPT)  
* [主引导记录](https://wiki.archlinux.org/index.php/Master_Boot_Record_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))(MBR)  

Arch 安装盘中包含了多种分区工具，可以根据分区表的类型进行选择：  

* [parted](https://wiki.archlinux.org/index.php/Parted): 支持 GPT 和 MBR
* [fdisk](https://wiki.archlinux.org/index.php/Fdisk#Fdisk_usage_summary), cfdisk, sfdisk: GPT 和 MBR
* [gdisk](https://wiki.archlinux.org/index.php/Gdisk#Gdisk_usage_summary), cgdisk, sgdisk: GPT  

>警告: 如果使用不兼容分区表格式的工具，会造成分区表损坏和所有数据丢失。  

下面例子里面使用 parted，它同时支持 BIOS/MBR 和 UEFI/GPT. 以交互模式启动，可以用 `(parted) help` 查看帮助，用 `(parted) quit` 退出。  
可以在启动 Arch 安装之前先用 Live 分区系统进行分区，[GParted](https://wiki.archlinux.org/index.php/GParted) 就很不错，它就有现成的 [Live CD](http://gparted.sourceforge.net/livecd.php). 

### 创建新分区表
如果设备没有分区，或者要改变分区表类型，需要新建分区表。  
打开需要新建分区表的设备：  
```
# parted /dev/sdx
```  
为 BIOS 系统创建 MBR/msdos 分区表：  
```
(parted) mklabel msdos
```  
为 UEFI 系统创建 GPT 分区表：  
```
(parted) mklabel gpt
```  

### 分区方案
您可以决定磁盘应该分为多少个区，每个分区又挂载在系统的哪个目录。将分区如何映射至目录（一般称此为挂载点），取决于您的[分区方案](https://wiki.archlinux.org/index.php/Partitioning_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E5.88.86.E5.8C.BA.E6.96.B9.E6.A1.88)。  

* 至少需要创建一个 `/` (root) 目录，有些分区类型和 [启动加载器](https://wiki.archlinux.org/index.php/Boot_loaders_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))组合有额外的分区要求：
* BIOS/GPT + [GRUB](https://wiki.archlinux.org/index.php/GRUB): 需要按照 [BIOS 启动分区设置](https://wiki.archlinux.org/index.php/GRUB#GUID_Partition_Table_.28GPT.29_specific_instructions) 的方式创建一个 1M 或 2M 的 `EF02` 类型分区.
* UEFI 的主板，需要一个 [EFI 系统分区](https://wiki.archlinux.org/index.php/Unified_Extensible_Firmware_Interface#EFI_System_Partition).
* 如果您需要[加密磁盘](https://wiki.archlinux.org/index.php/Disk_encryption)，则必须加以调整分区方案。系统安装后，也可以再配置加密文件夹，容器或 home 目录。  

系统需要需要 `/boot`、`/home` 等目录， [Arch 文件系统架构](https://wiki.archlinux.org/index.php/Arch_filesystem_hierarchy) 有各目录的详细介绍。如果没有创建单独的`/boot` 或 `/home` 分区，这些目录直接放到了根分区下面。后面会介绍如何创建 [交换分区](https://wiki.archlinux.org/index.php/Swap_space)。 

### 用 parted 进行分区
用下面命令打开 parted 交互模式：   
```
# parted /dev/sdx
```  
用下面命令创建分区：  
```
(parted) mkpart part-type fs-type start end
```  

* `part-type` 是分区类型，可以选择 `primary`, `extended` 或 `logical`，仅用于 MBR 分区表.
* `fs-type` 是文件系统类型，所有支持的类型列表在 [这里](http://www.gnu.org/software/parted/manual/parted.html#mkpart). 这里并不会实际创建文件系统，而是把分区的编码类型写入到磁盘供启动管理器使用。参阅 [Wikipedia:Disk partitioning#PC partition types](https://en.wikipedia.org/wiki/Disk_partitioning#PC_partition_types).
* `start` 是分区的起始位置，可以带单位, 例如 1M 指 1MiB.end 是设备的结束位置(不是 与 start 值的差)，同样可以带单位，也可以用百分比，例如 100% 表示到设备的末尾。
* `end` 是设备的结束位置(不是 与 `start` 值的差)，同样可以带单位，也可以用百分比，例如 `100%` 表示到设备的末尾。
* 为了不留空隙，分区的开始和结束应该首尾相连。  

如果看到下面警告：  
```
Warning: The resulting partition is not properly aligned for best performance.
Ignore/Cancel?
```
表示分区没 [对齐](https://wiki.archlinux.org/index.php/Partitioning#Partition_alignment)，请按照 [GNU Parted#Alignment](https://wiki.archlinux.org/index.php/GNU_Parted#Alignment) 进行修正。  
下面命令设置 `/boot` 为启动目录：  
```
(parted) set partition boot on
```  

* `partition` 是分区的编号，从 `print` 命令获取。

### UEFI/GPT 示例
首先需要一个 [EFI 系统分区](https://wiki.archlinux.org/index.php/Unified_Extensible_Firmware_Interface#EFI_System_Partition).如果是和 Windows 双系统启动，此分区已经存在，不要重新创建。  
用下面命令创建分区 (建议大小是 512MiB)。  
```
(parted) mkpart ESP fat32 1M 513M
(parted) set 1 boot on
```
剩下的空间可以按需要创建，root 占用全部 100% 剩余空间：   
```
(parted) mkpart primary ext4 513M 100%
```  
`/` (20GiB)，剩下的给 `/home`：  
```
(parted) mkpart primary ext4 513M 20.5G
(parted) mkpart primary ext4 20.5G 100%
```
创建 `/` (20GiB), swap (4Gib), 剩下给 `/home`：  
```
(parted) mkpart primary ext4 513M 20.5G
(parted) mkpart primary linux-swap 20.5G 24.5G
(parted) mkpart primary ext4 24.5G 100%
```

### BIOS/MBR 示例
单根目录分区：  
```
(parted) mkpart primary ext4 1M 100%
(parted) set 1 boot on
```
20Gib `/` 分区，剩下的给 `/home`：  
```
(parted) mkpart primary ext4 1M 20G
(parted) set 1 boot on
(parted) mkpart primary ext4 20G 100%
```
/boot (100MiB), `/` (20Gib), swap (4GiB) 剩下的给 `/home`:  
```
(parted) mkpart primary ext4 1M 100M
(parted) set 1 boot on
(parted) mkpart primary ext4 100M 20G
(parted) mkpart primary linux-swap 20G 24G
(parted) mkpart primary ext4 24G 100%
```

### 格式化文件系统并使用swap
仅仅分区是不够的，还需要 `mkfs` 将分区格式化为指定的[文件系统](https://wiki.archlinux.org/index.php/File_Systems_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))。  
先查看所有分区：  
```
# lsblk /dev/sdx
```
如果新创建了 UEFI 系统分区，需要格式化成 `fat32` 或 `vfat32` 文件系统，否则无法启动。Windows 双启动系统不要再格式化。  
```
# mkfs.vfat -F32 /dev/sdxY
```
建议用 `ext4` 文件系统格式化其它分区：  
```
# mkfs.ext4 /dev/sdxY
```
若您分了一个 [swap](https://wiki.archlinux.org/index.php/Swap_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 区，也不要忘了格式化并启用它：  
```
# mkswap /dev/sdaX
# swapon /dev/sdaX
```
先挂载 `/` (root) 分区，其它目录都要在 `/` 分区中创建然后再挂载。在安装环境中用 `/mnt` 目录挂载 root：  
```
# mount /dev/sdxR /mnt
```
然后挂载其余单独分区(除了 Swap)，比如 `/boot`，`/var`。先创建目录，然后挂载分区：  
```
# mkdir /mnt/home
# mount /dev/sda2 /mnt/home
```
建议将 EFI 系统分区挂载到 `/mnt/boot`，其它方式参阅[EFISTUB](https://wiki.archlinux.org/index.php/EFISTUB)。  
```
# mkdir -p /mnt/boot
# mount /dev/sdXY /mnt/boot
```
挂载好设备，就可以安装 Arch 了. 

## 安装
---

### 选择安装镜像
如果想要安装某个软件，必须先从 `/etc/pacman.d/mirrorlist` 中定义的镜像站中下载安装包到本地。在live系统里，该文件中所有的镜像站都默认开启，并且按照镜像系统被创建时各镜像站的与官方镜像站的同步状态和速度来排序。  
当系统在下载软件包的时候，列表中排的越靠前的镜像站优先级越高。你可以手动调整`/etc/pacman.d/mirrorlist`文件来将地理位置上离你比较近的镜像站放在文件最开头，当然，调整该文件应该以获得尽量大的下载速度为主要的标准，参考 [Mirrors](https://wiki.archlinux.org/index.php/Mirrors) 来获取更多信息。  
mirrorlist 文件也会被 `pacstrap` 复制到新系统，所以最好现在就设置，以中科大源为例：  
```
# nano /etc/pacman.d/mirrorlist

Server = http://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
```
如果您愿意，您可以只使用一个镜像并全删光其他行，但为保险，还是留其他几个离您较近的镜像作备用好。 更改镜像列表后请务必使用 `pacman -Syy` 强制刷新，详见 [Mirrors_(简体中文)](https://wiki.archlinux.org/index.php/Mirrors_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)). 

### 安装基本软件包
使用 pacstrap 来安装基本系统。如果您想通过 [AUR (简体中文)](https://wiki.archlinux.org/index.php/AUR_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 或者 [ABS (简体中文)](https://wiki.archlinux.org/index.php/ABS_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 编译安装软件包,需要装上 [base-devel](https://www.archlinux.org/groups/x86_64/base-devel/)：  
```
# pacstrap -i /mnt base base-devel
```
使用 `-i` 选项时会在实际安装前进行确认。此章节会给您安装好最基本的 Arch 系统，其它软件以后会用 [pacman (简体中文)](https://wiki.archlinux.org/index.php/Pacman_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 安装得到。 

## 配置
---

###fstab
用以下命令生成 [fstab](https://wiki.archlinux.org/index.php/Fstab). 之所以用 [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) 是因为它们能唯一且独立地标识，详见 [fstab#Identifying filesystems](https://wiki.archlinux.org/index.php/Fstab#Identifying_filesystems). 如果您想用卷标，用 -L 代替 -U 即可。  
```
# genfstab -U -p /mnt >> /mnt/etc/fstab
```
**强烈建议** 在执行完以上命令后，后检查一下生成的 `/mnt/etc/fstab` 文件是否正确。若在运行 genfstab 或是之后发生错误，后续修改请直接手动编辑 `fstab` 文件。详见 [fstab](https://wiki.archlinux.org/index.php/Fstab)。  

### chroot
将配置文件复制到`/mnt`的新系统 (例如 netctl 配置文件在 `/etc/netctl`), 然后 [chroot](https://wiki.archlinux.org/index.php/Change_Root_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 到新系统：  
```
# arch-chroot /mnt /bin/bash
```

### Locale


本地化的程序与库若要本地化文本，都依赖 [Locale](https://wiki.archlinux.org/index.php/Locale), 后者明确规定地域、货币、时区日期的格式、字符排列方式和其他本地化标准等等。在下面两个文件设置：`locale.gen` 与 `locale.conf`.  
`/etc/locale.gen`是一个仅包含注释文档的文本文件。指定您需要的本地化类型，只需移除对应行前面的注释符号（`＃`）即可，建议选择帶`UTF-8`的項：  
```
# nano /etc/locale.gen

en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
zh_TW.UTF-8 UTF-8
```
接着执行`locale-gen`以生成locale讯息：  
```
# locale-gen
```
`/etc/locale.gen` 生成指定的本地化文件，每次 [glibc](https://www.archlinux.org/packages/?name=glibc) 更新之后也会运行 `locale-gen`。  
创建 locale.conf 并提交您的本地化选项：  
>Tip: 将系统 locale 设置为`en_US.UTF-8`，系统的 Log 就会用英文显示，这样更容易问题的判断和处理。用户可以设置自己的 locale，详情参阅[Locale#Per user](https://wiki.archlinux.org/index.php/Locale#Per_user).  

```
# echo LANG=en_US.UTF-8 > /etc/locale.conf
```
> 警告: 不推荐在此设置任何中文locale，或导致tty乱码。  


### 终端字体和键盘映射
>提示: 对于大多中文用户，可忽略此章节。  

如果您在[#设置键盘布局](https://wiki.archlinux.org/index.php/Beginners%27_guide_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E8.AE.BE.E7.BD.AE.E9.94.AE.E7.9B.98.E5.B8.83.E5.B1.80) 时就已修改键盘布局和字体过，您就得再相应地编辑 `/etc/vconsole.conf` 以使该变动对新系统生效，比如：  
```
/etc/vconsole.conf

KEYMAP=de-latin1
FONT=lat9w-16
```
> 警告: 如果您设置的 KEYMAP 与 loadkeys 变量并不一样，那当您 [#Set the root password](https://wiki.archlinux.org/index.php/Beginners%27_guide_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#Set_the_root_password) 并重启后，可能没法再正常登录新系统了，因为一些键在两种布局的映射下并不一致。  

此章节只对您的虚拟终端生效，即对 [Xorg](https://wiki.archlinux.org/index.php/Xorg) 无效，详见 [Console fonts](https://wiki.archlinux.org/index.php/Fonts#Console_fonts).  

### 时间
可用的时区全集中在 `/usr/share/Zone/SubZone` 目录里了。选择时区：  
```
# tzselect
```
将 `/etc/localtime` 软链接到 `/usr/share/zoneinfo/Zone/SubZone`，以上海为例：  
```
# ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```
建议设置时间标准 为 [UTC](https://wiki.archlinux.org/index.php/Time_standard)，并调整 [时间漂移](https://wiki.archlinux.org/index.php/Time_skew):  
```
# hwclock --systohc --utc
```
系统上安装的其它操作系统也应该一起调整。 

### 创建初始 ramdisk 环境
在您用 pacstrap 安装 {[linux](https://www.archlinux.org/packages/?name=linux) 时就会自动运行 [mkinitcpio](https://wiki.archlinux.org/index.php/Mkinitcpio)，大部分用户都可以使用 `mkinitcpio.conf` 默认设置，如果有定制需求，请阅读[re-generate the initramfs image](https://wiki.archlinux.org/index.php/Mkinitcpio#Image_creation_and_activation)。然后运行：  
```
# mkinitcpio -p linux
```

### 设置 Root 密码
用 `passwd` 设置一个 root 密码：  
```
# passwd
```

### 安装 bootloader
[Boot loaders (简体中文)](https://wiki.archlinux.org/index.php/Boot_loaders_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 包含了可用选项和相应的配置. Intel CPU 也需要安装 [intel-ucode](https://www.archlinux.org/packages/?name=intel-ucode) 并根据 [Microcode](https://wiki.archlinux.org/index.php/Microcode) 配置 boot loader.  

###UEFI/GPT
安装程序假定系统使用 GPT 分区表，具有 [Unified Extensible Firmware Interface#EFI](https://wiki.archlinux.org/index.php/Unified_Extensible_Firmware_Interface#EFI_.E7.B3.BB.E7.BB.9F.E5.88.86.E5.8C.BA) 系统分区，FAT32 格式，512 MiB 或更大，gdisk type 为 `EF00`). EFI 系统分区挂载到 `/boot`。  
安装 [systemd-boot](https://wiki.archlinux.org/index.php/Systemd-boot) 到 EFI 系统分区：  
```
# bootctl install
```
当成功执行以上命令之后，依照 [systemd-boot#Configuration](https://wiki.archlinux.org/index.php/Systemd-boot#Configuration) 中的描述为系统创建一个引导入口（将 `$esp` 替换为 `/boot`）。或者使用 `/usr/share/systemd/bootctl/` 的示例配置文件。  

### BIOS/MBR
下面介绍在 MBR 系统上使用 Grub。安装 [grub](https://www.archlinux.org/packages/?name=grub) 和 [os-prober](https://www.archlinux.org/packages/?name=os-prober) 包，并执行 `grub-install`. 须根据实际分区自行调整 `/dev/sda`, 切勿在块设备后附加数字，比如 `/dev/sda1` 就不对.  
```
# pacman -S grub os-prober
# grub-install /dev/sda
```
下面命令会自动生成配置文件  
```
# grub-mkconfig -o /boot/grub/grub.cfg

```
详情参考[GRUB](https://wiki.archlinux.org/index.php/GRUB) 。  

### 配置网络
该过程与#建立网络连接基本一致，只不过该配置在新系统每次开机时都会自动生效。  
>注意: 了解更多网络配置相关信息，请访问 [Network_Configuration_(简体中文)](https://wiki.archlinux.org/index.php/Network_Configuration_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 及 [Wireless network configuration (简体中文)](https://wiki.archlinux.org/index.php/Wireless_network_configuration_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)).

### 主机名
设置个您喜欢的[主机名](https://wiki.archlinux.org/index.php/Network_configuration#Set_the_hostname)，例如：  
```
# echo myhostname > /etc/hostname
```
并在 `/etc/hosts` 添加同样的主机名：  
```
#<ip-address>       <hostname.domain.org>   <hostname>
127.0.0.1           localhost.localdomain   localhost   myhostname
::1                 localhost.localdomain   localhost   myhostname
```

###有线网络
如果您只用单一且固定的有线网络连接，启动 dhcpcd 服务，`interface` 是您的网络接口名：  
```
# systemctl enable dhcpcd@interface.service
```

### 无线网络
安装 [iw](https://www.archlinux.org/packages/?name=iw), [dialog](https://www.archlinux.org/packages/?name=dialog) 和 [wpa_supplicant](https://www.archlinux.org/packages/?name=wpa_supplicant), 您要靠它们连网：  
```
# pacman -S iw wpa_supplicant dialog
```
可能还需要[安装固件](https://wiki.archlinux.org/index.php/Wireless_network_configuration#Installing_driver.2Ffirmware)。  
如果使用wifi-menu 配置网络，在执行完所有设置之后、重启然后再进行设置，以避免进程间冲突。详情请阅读 [Netctl](https://wiki.archlinux.org/index.php/Netctl)。  

### 卸载分区并重启系统
设置 root [密码](https://wiki.archlinux.org/index.php/Password):  
```
 # passwd
```
离开 chroot 环境：  
```
# exit
```
systemd 在关机时会自动卸载分区，为了确保安全，可以用 `umount -R /mnt` 手动卸载分区。如果分区被占用，可以用 [fuser](https://en.wikipedia.org/wiki/fuser_(Unix))检查原因。  
重启计算机：  
```
# reboot
```
移除安装媒介，并还原 BIOS 中的启动选项。

## 安装之后
---
您现在应该有了一个完全可用的 Arch 系统，以此为起点，您可以将这些优雅的工具加以改造成理想的样子。强烈建议您阅读 [General recommendations (简体中文)](https://wiki.archlinux.org/index.php/General_recommendations_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))，特别是前两个部分.请继续阅读 [General recommendations (简体中文)](https://wiki.archlinux.org/index.php/General_recommendations_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 的剩余页面，它包含了安装后的众多教程，包括设置图形用户界面，声卡和触摸板等等。如果想捣鼓一大堆应用程序，详见 [List of Applications (简体中文)](https://wiki.archlinux.org/index.php/List_of_Applications_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)).[Arch Linux 中文化](https://wiki.archlinux.org/index.php/Arch_Linux_%E4%B8%AD%E6%96%87%E5%8C%96) 页面还包含了关于系统、软件中文支持的内容。  
>[Category](https://wiki.archlinux.org/index.php/Special:Categories):[Getting and installing Arch (简体中文)](https://wiki.archlinux.org/index.php/Category:Getting_and_installing_Arch_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))  
