###修改Android系统Launcher

####具体步骤

1. 去除原Launcher的Launcher属性。
  
  kitkat-x86使用的Launcher是Trebuchet，发现这点首先是通过查看编译后的system/app/和system/priv-app中均没有Launcher2.apk和Launcher3.apk。于是认为可能是某些app在编译时覆盖了Launcher2。经过在packages/app中使用grep -r "LOCAL_OVERRIDES_PACKAGES := Launcher2"找到了这个应用。之后编辑 Trebuchet的AndroidManifest.xml文件，去掉intent-filter中的以下三行
      
      <categoryandroid:name="android.intent.category.HOME"/>  
      <categoryandroid:name="android.intent.category.DEFAULT"/>  
      <categoryandroid:name="android.intent.category.MONKEY"/>  
      
  这样此APK就失去了作为Launcher的属性并。
  
2. 给Desktop添加Launcher属性

  同理，修改Desktop的AndroidManifest.xml文件，在initent-filter中添加上面的三行，并去掉
      
      <categoryandroid:name="android.intent.category.LAUNCHER"/>
      
  即可让系统在开机时直接运行Desktop。
  

####后续问题

  1. 屏幕最下方的是三个按键中的最右边的按键（具体学名不太清楚）仍人会调出桌面并显示应用列表，个人认为暂时应该考虑删除该按键的响应。
  
  2. Desktop本身的一些问题（壁纸无法保存，无法非多窗口显示）仍需进一步研读代码。
  
  
  
  
  
***有关提供Launcher属性的设置方法参考[此篇文章](http://blog.csdn.net/bamlook/article/details/11144819)***
