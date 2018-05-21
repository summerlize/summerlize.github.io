---
layout: post
title: "安裝 Guest VM Windows 10 於 Ubuntu Server Host"
date: 2018-05-18 12:00
comments: true
categories: Ubuntu
---
Ubuntu 版本: 16.04 Xenial

VBoxManage: 5.1.34_Ubuntur121010

首先需要安裝好 VirtualBox 和 並準備好 win10 的 iso 安裝檔，

使用指令確認欲安裝的 windows 版本對應之 ID，查出 win10 64bit 資訊如下，

	$ vboxmanage list ostypes|grep Windows

	Family Desc: Microsoft Windows
	ID:          Windows10_64
	Description: Windows 10 (64-bit)
	Family ID:   Windows

使用指令新增 VM，這裡將 VM 命名 WIN10，ostype 代入上面查出之 ID。

	$ vboxmanage createvm --name WIN10 --ostype Windows10_64 --register

	Virtual machine 'WIN10' is created and registered.
	UUID: 17b26672-f7cb-432e-a4e9-b8c0784da1d4
	Settings file: '/home/pipi/VirtualBox VMs/WIN10/WIN10.vbox'

指派記憶體、CPU等硬體資源，

	$ vboxmanage modifyvm WIN10 --memory 4096 --vram 32 --acpi on --ioapic on --cpus 2 --rtcuseutc on --cpuhotplug on --pae on --hwvirtex on11

設定 bridged 網路，

	$ vboxmanage modifyvm WIN10 --nic1 bridged --bridgeadapter1 eno0 --cableconnected1 on

指派 vrde (VirtualBox Remote Desktop Extension) port，可使用遠端桌面軟體連線。

	$ vboxmanage modifyvm WIN10 --vrde on --vrdeport 5001 --vrdeauthtype null

新增 VM 硬碟容量 50G，

	$ vboxmanage createhd --filename /vm/hd/WIN10.vdi --size 51200

加入 SATA 控制器，

ps. 可依需求自設選項 (Hard disk controllers: IDE, SATA (AHCI), SCSI, SAS, USB MSD, NVMe)

	$ vboxmanage storagectl WIN10 --name "SATA controller" --add sata

連結硬碟，

	$ vboxmanage storageattach WIN10 --storagectl "SATA controller" --port 0 --device 0 --type hdd --medium /vm/hd/WIN10.vdi

加入 IDE 控制器，

	$ vboxmanage storagectl WIN10 --name "IDE controller" --add ide

以光碟機方式連結 OS 安裝檔，

	$ vboxmanage storageattach WIN10 --storagectl "IDE controller"  --port 0 --device 0 --type dvddrive --medium /home/user/Windows10_64bit.iso


啟動 VM 後，就能用 windows 的遠端桌面連線 (RDC) 連上 VM 完成後續安裝步驟囉！

###常用 command

啟動 VM

``$ vboxmanage startvm WIN10 --type headless``

關機 VM

``$ vboxmanage controlvm WIN10 poweroff``

list 所有 VM

``$ vboxmanage list vms``

list 執行中的 VM

``$ vboxmanage list runningvms``

show VM 資訊

``$ vboxmanage showvminfo WIN10``

移除 VM 但保留相關檔案

``$ vboxmanage unregistervm WIN10``

移除 VM 並刪除相關檔案

``$ vboxmanage unregistervm WIN10 --delete``

匯出 VM

``$ vboxmanage export WIN10 -o /home/user/WIN10.ova``

增加硬碟容量，進入 OS 使用電腦管理>> 磁碟管理分配容量。

``$ vboxmanage modifyhd /vm/hd/WIN10.vdi --resize 81920``

調整記憶體大小

``$ vboxmanage modifyvm WIN10 --memory 8192``


詳細 API 請參考 [https://www.virtualbox.org/manual/ch08.html](https://www.virtualbox.org/manual/ch08.html)

###區網 IP 設置

前面已設置一組 bridged network nic1 for 設置固定 IP 使用，

接下來再設置一組 nat network nic2 for 區網 IP。

	# 設置一組 nat 區域網路 IP 命名為 natnet1
	$ vboxmanage natnetwork add --netname natnet1 --network "192.168.0.101/24" --enable
	
	$ vboxmanage natnetwork modify --netname natnet1 --dhcp on
	
	# 啟動 nat 網路設定
	$ VBoxManage natnetwork start --netname natnet1
	
	# 關機
	$ vboxmanage controlvm WIN10 poweroff
	
	# 將 natnet1 加入至 VM 網路
	$ vboxmanage modifyvm WIN10 --nic2 nat --nat-network2 natnet1
	
	# 開機
	$ vboxmanage startvm WIN10 --type headless


