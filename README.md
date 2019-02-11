
20190211 更新

在之前的版本上，增加了石像鬼 qos 的支持，测试效果不错，比 openwrt 自带的 sqm 好很多， sqm 的qos就是渣，基本上没什么效果

石像鬼 qos 和  SFE 加速 冲突， 两者只能运行一个， 开启了 SFE加速  会导致石像鬼 qos 失效，所以我专门发布了一个石像鬼 qos 的版本

如果你需要石像鬼 qos 功能，就下载带有  gargoyle 字样的文件

如果你不需要 qos 但是你很在乎 SFE 加速，那么下载之前的版本就可以了，之前的版本往下看 


针对 大麦路由器  DW33D 专门优化的固件，带 v2ray 你懂的 

固件   [DW33D-v2ray-极度优化-openwrt-ar71xx-nand-domywifi-dw33d-squashfs-sysupgrade.bin](https://github.com/qiang-yu/firmware/raw/master/DW33D-v2ray-%E6%9E%81%E5%BA%A6%E4%BC%98%E5%8C%96-openwrt-ar71xx-nand-domywifi-dw33d-squashfs-sysupgrade.bin)

固件初始登陆密码：password   刷机之后自己记得修改密码

由于 DW33D 的 GIGA 板子有缺陷，不能开启 2.4G 无线，否则必定死机，我这里专门编译了一个 GIGA的版本，自己下载 [no-ath9k-openwrt-ar71xx-nand-domywifi-dw33d-squashfs-sysupgrade](https://github.com/qiang-yu/firmware/raw/master/no-ath9k-openwrt-ar71xx-nand-domywifi-dw33d-squashfs-sysupgrade.bin)，只有 5G 没有 2.4G ，可以在 GIGA 板子上正常工作 

采用 L大的 OpenWrt 8.1稳定版本编译，专门为 DW33D 编译，对 v2ray 做了极度优化，能非常顺畅的跑

固件优化内容 ， L大的原始版本固件（下面称 原始固件）


1. 原始固件 v2ray 在 tcp,udp 会分别开 2 个 v2ray 进程，独立处理 tcp,udp
	v2ray 本身就已经非常消耗内存了，开2个占用资源太大
	
	优化：只开一个 v2ray ，同时处理 tcp,udp，节省一半的内存占用
	
	
2. 原始固件 v2ray 只能做透明代理
	
	优化： v2ray 同时做 透明代理，socks代理，http代理 三个功能，随你喜欢
	那么优化后的固件，socks5代理端口 = 端口 +1 ， http代理端口 = 端口 + 2
	
	假设你的路由器IP是  192.168.1.1 ， v2ray 设置端口为 1079 
		
	192.168.1.1:1079  为透明代理专用端口
	192.168.1.1:1080  为socks5代理，你可以把浏览器代理指向这个
	192.168.1.1:1081  为http代理，你可以把浏览器代理指向这个


3. 原始固件  是直接下载 v2ray 官方编译好的程序，非常大，非常占用空间
		原始固件  v2ray 13MB， v2ctl  14MB，两个占用了 27MB的空间
		
	优化：我自己重新编译了 v2ray ，把不需要的功能都去除了，大大缩小了 v2ray 的占用空间
	优化后的 v2ray 集成了 v2ctl 读取 json的功能，所以不需要额外的 v2ctl 了
	优化后 v2ray 一共只有 12MB，比之前节省一半的空间
	
	好处： 固件 df -h 显示， overlay 剩余 3MB 空间，省下来给你自己决定安装别的什么 
	
	
4. 	原始固件 采用的 v2ray 是不带 softfloat 的，采用内核的 FPU 做加密运算
	优化： 我测试过， softfloat 比 内核FPU 效率要高很多，不容易卡系统
	优化： 我重新编译的 v2ray 是自带 softfloat 的，不需要内核 FPU了，效率更高
	
	亲测，带 softfloat 的 v2ray 看 youtube 的网速比之前的快 20% （之前的只有 8000kbps，带 softfloat 的有 10000kbps）
	
	
5. 内核自带 ntfs, ext4 文件系统，以及对应的 fdisk , hdparam , mkfs.ext4  ... 之类的工具
	由于 DW33D 自带 16GB 的 TF 卡（我的有卡）
	TF卡对应系统设备为 /dev/sda ，类似硬盘
	可以把 TF 卡格式化成 ext4 文件系统，用于做系统扩展盘，比如挂载到 overlay 


6. 固件稳定性
	一句话形容----稳如狗 

	
7. 怎么刷这个固件？
	
	在你之前的 openwrt 固件之上，直接 系统--->升级（不保留配置） 就可以了	
	
恩山没账号，注册了也发不了贴，只能放到 github 了，用 dw33d 的兄弟能不能看到这个就看缘分了
	
	
	
