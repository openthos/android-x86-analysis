###修改Android系统Launcher

####具体步骤

1. 去除原Launcher的Launcher属性。
  
  kitkat-x86使用的Launcher是Trebuchet，发现这点首先是通过查看编译后的system/app/和system/priv-app中均没有Launcher2.apk和Launcher3.apk。于是认为可能是某些app在编译时覆盖了Launcher2。经过在packages/apps中使用grep -r "LOCAL_OVERRIDES_PACKAGES := Launcher2"找到了这个应用。之后编辑 packages/apps/Trebuchet/AndroidManifest.xml文件，去掉intent-filter中的以下三行
      
      <categoryandroid:name="android.intent.category.HOME"/>  
      <categoryandroid:name="android.intent.category.DEFAULT"/>  
      <categoryandroid:name="android.intent.category.MONKEY"/>  
      
  这样此APK就失去了作为Launcher的属性并。
  
2. 给Desktop添加Launcher属性

  同理，修改desktop_for_android/AndroidManifest.xml文件，在initent-filter中添加上面的三行，并去掉
      
      <categoryandroid:name="android.intent.category.LAUNCHER"/>
      
  即可让系统在开机时直接运行Desktop。
  
####环境准备

首先请编译整合了tieto patches的Kitkat-x86。

编译完毕后在源码目录执行以下代码
  
      mmm frameworks/base/libs/multiwindow/
  
执行完毕后保留以下三个文件备用

      out/target/product/x86/system/framework/com.tieto.extension.multiwindow.jar
      out/target/product/x86/system/etc/permissions/com.tieto.extension.multiwindow.xml
      out/target/common/obj/JAVA_LIBRARIES/com.tieto.extension.multiwindow_intermediates/classes.jar
      
      
desktop_for_android的获取及通过Android Studio编译：

1. 从[项目主页](https://github.com/tieto/desktop_for_android)中获取源码
2. 添加工程到Android studio，同意使用gradle但不使用本地gson库替换源码中的gson库。
3. 将上一步获取的classes.jar放到desktop_for_android/libs/目录下，并在Android studio中将该文件添加为lib文件
4. 按照如下方式修改desktop_for_android/app/build.gradle文件
        
        dependencies{
          compile files('./libs/gson-2.2.4.jar')
          -compile files('./libs/classes.jar')
          +provided files('./libs/classes.jar')
        }
5. 向Android system中添加以下文件
      
        cp com.tieto.extension.multiwindow.jar system/framework/
        cp com.tieto.extension.multiwindow.xml system/etc/permissions/

之后即可运行该Launcher。

####后续问题

  1. 屏幕最下方的是三个按键中的最右边的按键（具体学名不太清楚）仍人会调出桌面并显示应用列表，个人认为暂时应该考虑删除该按键的响应。
  
  2. Desktop本身的一些问题（壁纸无法保存，无法非多窗口显示）仍需进一步研读代码。
  
  
  
  
  
***有关提供Launcher属性的设置方法参考[此篇文章](http://blog.csdn.net/bamlook/article/details/11144819)***
