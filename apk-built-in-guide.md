# 1.APK列表
和dand中的requirement文件中要求的移植

#2.移植步骤

##2.1获取system镜像中的文件
system镜像需要通过这样一个步骤链来获取
    
    mount android-x86.img->get system.sfs->mount system.sfs->get system.img->mount system.img->get files
    
下面针对每一个步骤进行详解
  
mount android-x86.img & get system.sfs
    
    sudo mount -o offset=1048576 android-x86.img YOUR_MOUNT_FOLDER/
    cp YOUR_MOUNT_FOLDER/system.sfs YOUR_WORK_SPACE/
    sudo umount YOUR_MOUNT_FOLDER/
    
mount system.sfs & get system.img
    
    sudo mount system.sfs YOUR_MOUNT_FOLDER/
    cp YOUR_MOUNT_FOLDER/system.img YOUR_WORK_SPACE/
    sudo umount YOUR_MOUNT_FOLDER/
    
mount system.img $ get filse
    
    sudo mount system.img YOUR_MOUNT_FOLDER/
    cp -r YOUR_MOUNT_FOLDER/* SYSTEM_FILES/
    sudo umount YOUR_MOUNT_FOLDER/
    

##2.2加入定制内容
定制内容包括houdini库，技德工程师提供的libgralloc库，以及在其他系统安装过的APK文件夹。其中houdini库分两部分，一个是enable_nativebridge执行文件，该执行文件需要拷入到SYSTEM_FILES/bin/目录下。另外一部分是三个包含lib文件的压缩包，这三个压缩包需要拷入SYSTEM_FILES/目录下。技德工程师提供的libgralloc库文件则要分别将对应版本拷入SYSTEM_FILES/lib/目录和SYSTEM_FILES/lib64/目录下。剩下的获取的APK文件夹都直接放如SYSTEM_FILES/app/目录中即可

##2.3打包img和sfs
打包img需要android-tools-fsutils组件的支持，请先通过

    sudo apt-get install android-tools-fsutils

进行安装。安装完毕后在打包img之前需要通过如下语句对系统文件中的一些内容进行修改以保证正常运行

    sudo chmod 777 SYSTEM_FILES/etc/dhcpcd/dhcpcd-run-hooks SYSTEM_FILES/bin/enable_nativebridge

dhcpcd-run-hooks和网络访问相关，另外一个则是开启对arm应用支持的执行文件。修改完毕后则可以进行打包，打包语句如下

    sudo make_ext4fs -l 4G system.img SYSTEM_FILES/   #-l后的大小可以自由输入，保证不小于文件内容大小即可，打包后的img文件大小为真实大小而不是-l中输入的大小
    sudo mksquashfs system.img system.sfs -comp xz    #压缩方式可以自行选择，xz为最高压缩比，建议选择
    
之后将sfs文件替换原本的sfs文件即可完成定制。
