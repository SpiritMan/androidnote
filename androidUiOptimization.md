---
title: Android APP优化之UI优化

date: 2016-07-04 23:15:00

tags:Android

---
#Android APP优化之UI优化
UI优化的目的：制作出加载效率高并且复用性高的UI

##遵从的原则：
* 多用RelativeLayout和LinearLayout
* 将可复用的组件抽取出来并通过< include />标签使用
* 使用< merge />标签减少布局的嵌套层次
* 使用< ViewStub />标签来加载一些不常用的布局
<!-- more -->

## 详细介绍
在同一个布局我们可以用LinearLayout解决的我们尽量用LinearLayout，比如：（宅米）支付方式的选择。使用RelativeLayout能使我们构建的布局适应性更强，构建出来的UI布局对多屏幕的适配效果越好，通过指定UI控件间的相对位置，使在不同屏幕上布局的表现能基本保持一致。
 
###1、< include />的使用
通过它，我们可以将这些共用的组件抽取出来单独放到一个xml文件中，然后使用< include />标签导入共用布局。

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <FrameLayout
        android:id="@+id/container"
        android:layout_width="match_parent"
        android:layout_above="@+id/tabs"
        android:layout_height="match_parent">
        </FrameLayout>

    <include
        android:id="@+id/tabs"
        layout="@layout/bottom_tabs"/>
</LinearLayout>
```

通过这种方式，我们既能提高UI的制作和复用效率，也能保证制作的UI布局更加规整和易维护。

###2、< merge />标签的使用
< merge />标签的作用是合并UI布局，使用该标签能降低UI布局的嵌套层次。该标签主要用于两种场景下，第一是当xml文件的根布局是FrameLayout时，可以用merge作为根节点。第二种情况是当用include标签导入一个共用布局时，如果父布局和子布局根节点为同一类型，可以使用merge将子节点布局的内容合并包含到父布局中。
show_logo.xml

```
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:layout_width="30dp"
        android:layout_alignParentRight="true"
        android:src="@mipmap/ic_launcher"
        android:layout_height="30dp" />

</merge>
```
activity_logo.xml

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">

    <include
        layout="@layout/show_logo"/>

</RelativeLayout>
```

### 3、< ViewStub />的使用
ViewStub是一个不可见的，能在运行期间延迟加载的大小为0的View，它直接继承于View。当对一个ViewStub调用inflate()方法或设置它可见时，系统会加载在ViewStub标签中引入的我们自己定义的View，然后填充在父布局当中。在对ViewStub调用inflate()方法或设置visible之前，它是不占用布局空间和系统资源的。
在我们用来显示网络请求结果的时候，可以使用<ViewStub>，通过网络请求的结果来判断时候要显示控件，提高流畅度。
error.xml

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/empty_prompt"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

        <TextView
            android:id="@+id/empty_content"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:layout_below="@id/empty_image"
            android:layout_marginTop="20dp"
            android:gravity="center"
            android:text="您目前还没有订单"
            android:textColor="@color/grey"
            android:textSize="18sp" />
   </RelativeLayout>
```
fragment_myOrder.xml


```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">

       <ViewStub
        android:id="@+id/error_layout"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
       	android:layout="@layout/error" />

</RelativeLayout>
```
MyOrdersActivity

```
//显示
ViewStub stub = (ViewStub) findViewById(R.id.error_layout);
        stub.inflate();
//隐藏
stub.setVisibility(View.GONE)
```
##工具
1.设置 -> 开发者选项 -> 调试GPU过度绘制 -> 显示GPU过度绘制
![20150507093837866](media/14483451725800/20150507093837866.jpg)

2.Hierarchy Viewer








