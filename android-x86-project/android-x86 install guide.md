##事前准备

- 编译好的efi_img目标的android_x86_64.img镜像
- 一台能够通过uefi启动的机器
- 1GB以上大小的U盘

###启动器制作

####Windows平台

通过7zip软件打开android_x86_64.img，将里面内容完全复制到U盘中即可。

####linux平台

通过使用DD命令即可完成，具体语句如下

    sudo umount your_usb_device(with partition number e.g. /dev/sdc1)
    sudo dd if=android_x86_64.img of=your_usb_device（no partition number e.g. /dev/sdc)

###安装步骤

- 1.通过uefi启动usb设备

  - 1）进入setup（不同机器有着不同进入方式，不详细阐述），在boot选项卡中修改启动方式，打开uefi模式并重启即可。
  - 2）进入启动项菜单（同不详细阐述），选择通过uefi方式启动装有安装镜像的usb设备。
  
- 2.选择进入installer项

  - 进入usb的grub菜单后能够看到live系统，debug模式，installer和启动Windows四个选项，选择installer项进入。
  
- 3.创建安装分区（若分区已存在则跳过此步）

  - 1）选择create/modify partitions选项进入硬盘选择界面
  - 2）选择自己想要创建分区的硬盘，进入分区配置界面
  - 3）移动光标到空余空间，选择屏幕下方的new选项来分配一个新分区
  - 4）根据提示输入分区大小和其他信息并创建（分区大小和传统字节数并不对应，请多尝试几次来找到对应规律并创建符合自己要求的大小的分区）
  - 5）选择屏幕下方的write选项并输入yes来完成分区的创建并返回分区选择菜单。
  
- 4.进行安装

  - 1）选择想要安装系统的分区
  - 2）选择使用ext4文件系统进行格式化，并确认
  - 3）询问是否安装grub，选择skip跳过
  - 4）询问是否安装grub2，由于新版本android-x86的grub安装部分存在问题，在此选择skip跳过
  - 5）询问是否将system安装到文件系统，为了方便我们调试和获取信息，选择yes进行安装
  - 6）等待安装完成后进入系统或重新启动
  
- 5.添加grub到efi分区（若efi分区已经有grub则跳过此步）

  为了能够通过efi方式启动我们需要将grub安装到efi分区中去，可以通过以下方法来进行安装。
  - 通过Ubuntu
  
    进入Ubuntu，并使用下述命令将efi分区mount到系统中来。
    
        sudo mount /dev/efi_partiton EFI_MOUNT/
    
    之后将U盘目录下的efi/目录下的Boot文件夹全部拷贝到EFI_MOUNT/目录下即可。
  
  - 通过Android
  
    安装完后不选择重启选择进入系统，即可通过Android来将grub内容安装到efi分区。首先按alt+f1进入shell，并通过以下语句将efi分区mount到系统中
    
        mount -t vfat /dev/efi_paritition EFI_MOUNT/
    
    之后通过相同方法将Boot文件夹复制到其中即可。
    
- 6.修改grub增加新装系统的启动入口
  
  - 1）根据编译日期来判断安装后的Android系统主文件夹的名称，i.g. android-2015-12-23
  - 2) 修改grub，按照如下例子添加一个通常系统入口和DEBUG系统入口
  ```
  menuentry 'Android-x86 2015-12-23' --class android-x86 {
	                  search --file --no-floppy --set=root /android-2015-12-23
	                  linuxefi /android-2015-12-23/kernel root=/dev/ram0 androidboot.hardware=android_x86_64 quiet DATA=
	                  initrdefi /android-2015-12-23/initrd.img
            }
 ```
 ```
  menuentry 'Android-x86 2015-12-23 DEBUG' --class android-x86 {
	                  search --file --no-floppy --set=root /android-2015-12-23
	                  linuxefi /android-2015-12-23/kernel root=/dev/ram0 androidboot.hardware=android_x86_64 quiet DATA= DEBUG=2
	                  initrdefi /android-2015-12-23/initrd.img
            }
  ```
  
  - 3）重启后进入grub即可看到添加的入口
  
- 7.启动系统

  - 推荐方法：使用rEFInd来启动grub，配合上一步添加的入口来进入系统，rEFInd的配置请查看其他相关文档（目前实验室的机器中均配有rEFInd）
  - 强制进入grub2：进入setup中的boot选项卡，将所有boot项全部设置为disable并将启动方式设定为uefi only，系统则会在启动后自动进入grub2，之后通过上一步添加的入口即可进入系统。
  
  **有任何android-x86安装相关问题都可以与我联系，祝各位顺利**
