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

