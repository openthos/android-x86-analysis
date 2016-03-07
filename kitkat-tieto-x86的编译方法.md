###事前准备

####编译环境

1. 参考[此法](http://www.cnblogs.com/Wisp/articles/3099494.html)安装Oracle-jdk的__1.6版本__。
2. 参考[此教程](https://github.com/openthos/openthos/wiki/Establishing-a-Build-Environment%28lollipop-x86%29)安装软件包。

####源码准备

1. 参考[android-x86](http://www.android-x86.org/getsourcecode)官网获取kitkat-x86源码
2. 进入源码目录分别在framework/base、framework/native、packages/apps/Settings中应用[base-patch](https://github.com/openthos/android-x86-analysis/blob/master/ladehunter-Tieto-patches/base-patch.patch),[native-patch](https://github.com/openthos/android-x86-analysis/blob/master/ladehunter-Tieto-patches/native-patch.patch),[Setting-patch](https://github.com/openthos/android-x86-analysis/blob/master/ladehunter-Tieto-patches/Setting-patch.patch)

使用方法：
      
      cd framework/base
      git apply --reject base-patche
      cd ../native
      git apply --reject native-patch
      cd ../../packages/apps/Settings
      git apply --reject Settings-patch
      

###编译

进入源码目录，通过如下代码编译

      . build/envsetup.sh 
      lunch  //selet 5
      make iso_img -jX

###运行

参考此教程，https://github.com/openthos/openthos/wiki/How-to-Run-the-System
      
