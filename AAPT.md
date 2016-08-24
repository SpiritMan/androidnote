# AAPT

aapt即Android Asset Packaging Tool，在SDK的build-tools目录下。该工具可以查看，创建， 更新ZIP格式的文档附件(zip, jar, apk)。也可将资源文件编译成[二进制文件](http://baike.baidu.com/view/1473761.htm)，尽管你可能没有直接使用过aapt工具，但是build scripts和IDE[插件](http://baike.baidu.com/view/18979.htm)会使用这个工具打包apk文件构成一个Android 应用程序。在使用aapt之前需要在环境变量里面配置SDK-tools路径，或者是路径+aapt的方式进入aapt。

## MAC 调用AAPT

我们可以在mac 命令行下找到自己android sdk所在的路径例如：

```
/Users/yolo/Library/Android/sdk/build-tools/23.0.3
```

23.0.3为android sdk的版本。

### AAPT命令介绍

1.列出压缩文件的目录：

```
 aapt l[ist] [-v] [-a] file.{zip,jar,apk}
```

例如：

```
./aapt l /Users/yolo/AndroidStudioProjects/android/app/build/outputs/apk/origin.apk
```

-v:会以table的形式输出目录，table的表目有：Length、Method、Size、Ratio、Date、Time、CRC-32、Name。
其中Method表示压缩形式，有：Deflate及Stored两种，即该Zip目录采用的算法是压缩模式还是存储模式；可以看出resources.arsc、*.png采用压缩模式，而其它采用压缩模式。
Ratio表示压缩率。CRC-32未明其意，Sodino盼指教。

-a:会详细输出所有目录的内容。

2.查看apk包的packageName、versionCode、applicationLabel、launcherActivity、permission等各种详细信息

```
 aapt d[ump] [--values] WHAT file.{apk} [asset [asset ...]]
```

aapt dump badging filepath 用来查看apk的所有信息，包括权限，包名，启动页等。

```
./aapt dump badging /Users/yolo/Desktop/Apk/origin/origin.apk
```

aapt dump permissions filepath 用来查看apk中所用到的权限

```
./aapt dump permissions /Users/yolo/Desktop/Apk/v3.5.3/origin.apk
```

aapt dump resources filepath 用来查看apk中的资源信息

```
./aapt dump resources /Users/yolo/Desktop/Apk/v3.5.3/origin.apk
```

aapt dump configurations filepath 用来查看apk的配置信息。

```
./aapt dump configurations /Users/yolo/Desktop/Apk/v3.5.3/origin.apk
```

在查看文件太多时，命令行查看不好，我们可以导出来 在命令后面加入 > filepath(导出文件的目录)就可以了。

```
./aapt dump badging /Users/yolo/Desktop/Apk/v3.5.3/origin.apk >/Users/yolo/Desktop/Apk/v3.5.3/test.txt
```

