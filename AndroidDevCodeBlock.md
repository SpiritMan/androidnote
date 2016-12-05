## Android 开发常用代码块

1. 防止Handler造成内存泄漏，避免使用非静态内部类，继承Handler时，要么是放在单独的类文件中，要么就是使用静态内部类

   ```java
   private static class MyHandler extends Handler {
           private WeakReference<MainActivity> activityWeakReference;

           public MyHandler(MainActivity activity) {
               activityWeakReference = new WeakReference<>(activity);
           }

           @Override
           public void handleMessage(Message msg) {
               MainActivity activity = activityWeakReference.get();
               if (activity != null) {
                   out.println(".");
               }
           }
       }
   ```
   
2.swiperefreshlayout,第一次进入页面的时候显示加载进度条
   ```java
   mSwipeRefreshLayout.post(new Runnable() {
    @Override    
   public void run() {
       mSwipeRefreshLayout.setRefreshing(true);
    }
});
   ```
3.消除A页面到A页面之间的页面，例如A->B->C->A，
   ```java
   Intent intent = new Intent(C.this, A.class);
        intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
        startActivity(intent);
   ```   
   或在AndroidManifest.xml中设置Activity的启动模式为singleTask
   
4.替换AndroidManifest中的占位符
   build文件
   ```java
   buildTypes {
        dev {
            debuggable true
            //替换AndroidManifest中的占位符
            manifestPlaceholders = [
                    //环信应用的appkey
                    "emAppKey": "11721dev",
            ]
           }
            
            test {
            debuggable true
            //替换AndroidManifest中的占位符
            manifestPlaceholders = [
                    //环信应用的appkey
                    "emAppKey": "11721test",
            ]
           }
        }
   ```   
   AndroidManifest.xml
   
   ```java
   <!-- 设置环信应用的appkey -->
        <meta-data
            android:name="EASEMOB_APPKEY"
            android:value="${emAppKey}" />
   ```   
5.拨打电话
   Intent.ACTION_DIAL直接拨打 需要权限
   Intent.FLAG_ACTIVITY_NEW_TASK 跳转到拨号界面，不需要权限
   ```java
         //原来用Intent.ACTION_DIAL，会跳到拨号界面
        Intent intent = new Intent(Intent.ACTION_DIAL, Uri.parse("tel:" + "10086"));
        intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        startActivity(intent);
   ```   
6.webview全屏显示image
   ```java
   String imgSrcHtml = "<html><img src='" + url + "' style='width:100%;height:auto'/></html>";
   mWebView.loadData(imgSrcHtml, "text/html", "UTF-8");
   ``` 
