## 先定个小目标：搞懂ViewStub

Hi大家好，我叫Yolo.cc，今天我们来彻底了解ViewStub。

### 什么是ViewStub

ViewStub是一个不可见并且用来在运行时延迟加载试图，高宽都为0的View，ViewStub在没有调用inflate()方法或setVisibility(View.VISIBLE)之前，它不占用的布局和系统资源。

### ViewStub主要用来干什么呢？

- 用来显示一些延迟加载的UI，减少启动时的资源消耗。
- 用来显示一些不常用的UI，比如网络出错提示页面、空页面提示等。

### ViewStub该怎么用呢？

首先我们可以建立个subView.xml文件用来显示不常用UI

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <TextView
        android:id="@+id/hello_tv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="DATA EMPTY!"/>

</FrameLayout>
```

在activity_main.xml中用ViewStub控件。

```xml
<LinearLayout
    android:id="@+id/linearLayout"
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="com.yolocc.animator.MainActivity">

    <Button
        android:id="@+id/button"
        android:text="Show ViewStub"
        android:layout_height="wrap_content"
        android:layout_width="match_parent"/>

    <ViewStub
        android:id="@+id/view_stub"
        android:layout_marginTop="20dp"
        android:layout_width="match_parent"
        android:layout="@layout/subView"
        android:layout_height="wrap_content"/>
</LinearLayout>

```

然后可以在MainActivity.java中通过调用它的inflate()方法或setVisibility(View.VISIBLE)方法，显示ViewStub中的UI。

```java
ViewStub viewStub = (ViewStub) findViewById(R.id.view_stub);
Button button = (Button) findViewById(R.id.button);
button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                viewStub.inflate();
//                viewStub.setVisibility(View.VISIBLE);
            }
        });
```

到这里ViewStub的简单使用就完了。那ViewStub有什么高级用法呢或需要注意的地方呢，要用到它子View该怎么初始化呢，如果想隐藏这个UI又该怎么做呢。

### ViewStub的高级用法

我想在代码中改变subView.xml中Textview的显示内容，首先我们会想到用viewStub(R.id.hello_tv)，然后调用setText()方法，运行发现出错了。好了，现在我们来看源码解决这个问题吧，在ViewStub的inflate()方法中我们找到了答案。

```java
//mLayoutResource为ViewStub设置的id
final View view = factory.inflate(mLayoutResource, parent,
                        false);

                if (mInflatedId != NO_ID) {
                  //view在父控件中的id，我们可以在xml中通过inflatedId设置
                    view.setId(mInflatedId);
                }
				
                final int index = parent.indexOfChild(this);
//移除自己
                parent.removeViewInLayout(this);
				//将view加入父控件中
                final ViewGroup.LayoutParams layoutParams = getLayoutParams();
                if (layoutParams != null) {
                    parent.addView(view, index, layoutParams);
                } else {
                    parent.addView(view, index);
                }
```

从上面的代码我们可以看到其实ViewStub所包含的View最后都会重新赋值到新的View中，并将自己移除，从这我们就明白了为什么调用inflate()方法后，viewStub(R.id.hello)运行会出错了。不过我们发现在这个里面view有着viewStub里面的所有控件，并在最后被返回了，Soga，所以我们可以在上面的button点击事件下改动下。

```java
button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                View subView = viewStub.inflate();
                TextView hello = (TextView) subView.findViewById(R.id.hello_tv);
                hello.setText("hello yolo.cc");
//                viewStub.setVisibility(View.VISIBLE);
            }
        });
```

有时我们判断ViewStub的visibility来显示和隐藏控件。但是发现System.out.println("getVisibility:" + viewStub.getVisibility());输出的总是8，即View.Gone。不管怎么设置控件的状态都没用。看了源码发现：第一在初始化时，调用了setVisibility(GONE);

```java
 public ViewStub(Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes) {
        super(context);

        final TypedArray a = context.obtainStyledAttributes(attrs,
                R.styleable.ViewStub, defStyleAttr, defStyleRes);
        mInflatedId = a.getResourceId(R.styleable.ViewStub_inflatedId, NO_ID);
        mLayoutResource = a.getResourceId(R.styleable.ViewStub_layout, 0);
        mID = a.getResourceId(R.styleable.ViewStub_id, NO_ID);
        a.recycle();

        setVisibility(GONE);
        setWillNotDraw(true);
    }
```

这也合理，但是为什么setVisibility(View.VISIBLE)后viewStub.getVisibility())还是8呢，带着这个问题我有找了下它的setVisibility()的代码

```java
public void setVisibility(int visibility) {
        if (mInflatedViewRef != null) {
            View view = mInflatedViewRef.get();
            if (view != null) {
                view.setVisibility(visibility);
            } else {
                throw new IllegalStateException("setVisibility called on un-referenced view");
            }
        } else {
            super.setVisibility(visibility);
            if (visibility == VISIBLE || visibility == INVISIBLE) {
                inflate();
            }
        }
    }
```

从中可以看出，原来setVisibility()并不对ViewStub做操作而是对inflate()中的view进行操作。所以我们再对它进行显示和隐藏操作时该这么改进

```java
button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
              if (view != null && view.getVisibility() == View.VISIBLE) {
                view.setVisibility(View.GONE);
                button.setText("Show ViewStub");
            } else {
                if (isInflate) {
                    view = viewStub.inflate();
                    isInflate = false;
                } else {
                    view.setVisibility(View.VISIBLE);
                }
                button.setText("Hide ViewStub");
                TextView hello = (TextView) subView.findViewById(R.id.hello_tv);
                hello.setText("hello yolo.cc");
//                viewStub.setVisibility(View.VISIBLE);
            }
        });
```

### ViewStub我们该注意的地方

- ViewStub不支持<merge>标签

好了ViewStub就介绍完了，di'yi'ci分析源码，继续努力，O(∩_∩)O~。
