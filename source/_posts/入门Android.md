---
title: 入门Android
date: 2018-09-02 09:00:33
tags: java
catagories: article
cover: img/cover4.jpg
author: 
  nick: Vincent
  link: bloger.top

# 如果文章为转载文章，需要多加文章出处项
editor:
  name: Vincent
  link: https://bloger.top

# 首页每篇文章的子标题
subtitle: Android入门学习
---
<!-- TOC -->

- [如果文章为转载文章，需要多加文章出处项](#如果文章为转载文章需要多加文章出处项)
- [首页每篇文章的子标题](#首页每篇文章的子标题)
- [Android基础](#android基础)
    - [1. 系统架构](#1-系统架构)
    - [2. Android 版本](#2-android-版本)
    - [3. 组件](#3-组件)
        - [3.1 Activity](#31-activity)
            - [设置布局](#设置布局)
            - [启动模式 (LaunchMode)](#启动模式-launchmode)
            - [生命周期](#生命周期)
            - [启动Activity](#启动activity)
            - [片段 (Fragment)](#片段-fragment)
        - [3.2 Service](#32-service)
            - [回调方法](#回调方法)
            - [生命周期](#生命周期-1)
        - [3.3 Content Provider](#33-content-provider)
            - [访问提供程序](#访问提供程序)
            - [创建内容提供程序](#创建内容提供程序)
        - [3.4 Broadcast Receiver](#34-broadcast-receiver)
            - [接收广播](#接收广播)
            - [发送广播](#发送广播)
            - [LocalBroadcast](#localbroadcast)
    - [4. UI](#4-ui)
        - [4.1 布局](#41-布局)
            - [线性布局 LinearLayout](#线性布局-linearlayout)
            - [相对布局 RelativeLayout](#相对布局-relativelayout)
            - [帧布局 FrameLayout](#帧布局-framelayout)
            - [约束布局 ConstraintLayout](#约束布局-constraintlayout)
        - [4.2 控件](#42-控件)
            - [常用属性](#常用属性)
            - [文本](#文本)
            - [输入框](#输入框)
            - [图像](#图像)
            - [其他控件](#其他控件)
            - [RecyclerView](#recyclerview)
        - [4.3 自定义控件](#43-自定义控件)
        - [4.4 其他 UI](#44-其他-ui)
    - [5. 存储](#5-存储)
        - [5.1 SharedPreference.](#51-sharedpreference)
        - [5.2 SQLite](#52-sqlite)
    - [6. 多媒体](#6-多媒体)
    - [7. 资源](#7-资源)
        - [7.1 后缀](#71-后缀)
        - [7.2 drawable](#72-drawable)
        - [7.3  字符串, 颜色值, 数值](#73--字符串-颜色值-数值)
    - [8. 传感器 & 硬件](#8-传感器--硬件)
    - [9. 网络](#9-网络)
    - [10. 线程](#10-线程)
    - [11. 扩展](#11-扩展)

<!-- /TOC -->
# Android基础

## 1. 系统架构

![架构图](Android系统架构图.jpg)

四层架构, 从上往下分别是: 应用层、应用框架层、系统运行库层、Linux内核。

## 2. Android 版本

[Android各版本介绍](https://developer.android.google.cn/preview/)

![版本](android_version.jpg)

## 3. 组件

Android的四大组件：

- Activity（前台展示）
- Service（后台服务）
- BroadCast（广播接收器）
- Content Provider（内容提供）

### 3.1 Activity

*Activity* 表示具有用户界面的单一屏幕。

Activity 是 **Activity** 的子类, 如果用到了 **Fragment**, 那么 Activity需要继承自 **FragmentActivity**. Activity需要在**清单文件**中进行注册. 

#### 设置布局

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
}
```

#### 启动模式 (LaunchMode)

即 Activity 进入任务栈的模式. 有四种启动模式:

- standar

- singleTop

- singleTask

- singleInstance

可以在清单文件中activity下设置启动模式:

```xml
<activity android:name=".MainActivity"
          android:launchMode="singleTop">
</activity>
```

也可以在启动Activity的Intent中添加flag:

```java
intent.setFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
```

1. standard(Default): 标准

   每次启动 Activity 都会创建实例.

2. singleTop: 栈顶复用

   如果要启动的 Activity 已经位于任务栈的栈顶, 那么不会创建新的实例, 但是 onNewIntent 回调会被调用.

3. singleTask: 栈内复用

   与 singleTop类似, 如果要启动的 Activity 在任务栈中存在实例, 那么不会创建新的实例, 同时调用 onNewIntent 回调

4. singleInstance: 单例

   启动 Activity 时会为其创建一个新的任务栈, 然后与 singleInstance相同

#### 生命周期

![生命周期](activity_lifecycle.png)

#### 启动Activity

```java
Intent intent = new Intent(this, MyActivity.class);
// 这里可以传参, 支持基本数据类型, Bundle, Parcelable序列化数据, 数组
intent.putExtra("media_id", "a1b2c3");
// ...
startActivity(intent);
// 如果需要返回值
// startActivityForResult(intent, requestCode);
```

#### 片段 (Fragment)

`Fragment` 表示 `Activity` 中的行为或用户界面部分。您可以将多个片段组合在一个 Activity 中来构建多窗格 UI，以及在多个 Activity 中重复使用某个片段。您可以将片段视为 Activity 的模块化组成部分，它具有自己的生命周期，能接收自己的输入事件，并且您可以在 Activity 运行时添加或移除片段（有点像您可以在不同 Activity 中重复使用的“子 Activity”）。

Android 在 Android 3.0（API 级别 11）中引入了片段，主要是为了给大屏幕（如平板电脑）上更加动态和灵活的 UI 设计提供支持。下图是个新闻应用:

![Fragments](fragments.png)

Fragment 作为 Fragment 的子类, 必须嵌在 Activity 中使用. Fragment也有自己的生命周期.

![Fragment生命周期](activity_fragment_lifecycle.png)

```java
public static class ExampleFragment extends Fragment {
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        // Inflate the layout for this fragment
        return inflater.inflate(R.layout.example_fragment, container, false);
    }
}
```

在Activity中使用Fragment. 两种方式

1. 在Activity布局文件中声名

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
       android:orientation="horizontal"
       android:layout_width="match_parent"
       android:layout_height="match_parent">
       <fragment android:name="com.example.news.ArticleListFragment"
               android:id="@+id/list"
               android:layout_weight="1"
               android:layout_width="0dp"
               android:layout_height="match_parent" />
       <fragment android:name="com.example.news.ArticleReaderFragment"
               android:id="@+id/viewer"
               android:layout_weight="2"
               android:layout_width="0dp"
               android:layout_height="match_parent" />
   </LinearLayout>
   ```

2. 在Activity代码中动态添加

   Fragment需要添加到一个 ViewGroup中, 一般使用 FrameLayout.

   ```java
   FragmentManager fragmentManager = getFragmentManager();
   FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
   
   ExampleFragment fragment = new ExampleFragment();
   fragmentTransaction.add(R.id.fragment_container, fragment);
   fragmentTransaction.commit();
   ```

### 3.2 Service

*服务*是一种在后台运行的组件，用于执行长时间运行的操作或为远程进程执行作业。 服务不提供用户界面。

服务可由其他应用组件启动，而且即使用户切换到其他应用，服务仍将在后台继续运行。 此外，组件可以绑定到服务，以与之进行交互，甚至是执行进程间通信 (IPC)。

两种形式

1. **启动**

   当前应用组件可以通过 **startService()** 启动服务.

2. **绑定**

   组件可通过 **bindService()**绑定服务, 绑定服务提供了一个 客服端-服务端接口, 当所有绑定该服务的组件都取消绑定后, 该服务会销毁.

#### 回调方法

1. onStartCommand()

   当另一个组件（如 Activity）通过调用 `startService()` 请求启动服务时，系统将调用此方法。一旦执行此方法，服务即会启动并可在后台无限期运行。 如果您实现此方法，则在服务工作完成后，需要由您通过调用 `stopSelf()` 或 `stopService()` 来停止服务。（如果您只想提供绑定，则无需实现此方法。） 

2. onBind()

   当另一个组件想通过调用 `bindService()` 与服务绑定（例如执行 RPC）时，系统将调用此方法。在此方法的实现中，您必须通过返回 `IBinder` 提供一个接口，供客户端用来与服务进行通信。请务必实现此方法，但如果您并不希望允许绑定，则应返回 null。

3. onCreate()

   首次创建服务时，系统将调用此方法来执行一次性设置程序（在调用 `onStartCommand()` 或`onBind()` 之前）。如果服务已在运行，则不会调用此方法。

4. onDestroy()

   当服务不再使用且将被销毁时，系统将调用此方法。服务应该实现此方法来清理所有资源，如线程、注册的侦听器、接收器等。 这是服务接收的最后一个调用。

#### 生命周期

![生命周期](service_lifecycle.png)

### 3.3 Content Provider

*内容提供程序*管理一组共享的应用数据。您可以将数据存储在文件系统、SQLite 数据库、网络上或您的应用可以访问的任何其他永久性存储位置。 其他应用可以通过内容提供程序查询数据，甚至修改数据（如果内容提供程序允许）。

内容提供程序以一个或多个表（与在关系型数据库中找到的表类似）的形式将数据呈现给外部应用。

#### 访问提供程序

以用户字典访问程序的查询为例:

```java
// Queries the user dictionary and returns results
mCursor = Context.getContentResolver().query(
    UserDictionary.Words.CONTENT_URI,   // FROM table_name
    // "content://user_dictionary/words"
    mProjection,                        // col col col
    mSelectionClause                    // WHERE col = value
    mSelectionArgs,                     // 替换 上一参数中的占位符
    mSortOrder);                        // ORDER BY
```

用户字典访问程序需要权限: *android.permission.READ_USER_DICTIONARY*

#### 创建内容提供程序

创建一个类 继承自 ContentProvider. 同时需要在清单文件中声名:

```xml
<provider
    android:authorities="package.MyProvider"
    android:name=".MyProvider" />
```

ContentProvider是一个抽象类, 需要实现抽象方法:

```java
public class MyProvider extends ContentProvider {

    public boolean onCreate() {
        return true;
    }
    
    public Cursor query(@NonNull Uri uri, @Nullable String[] projection, @Nullable String selection, @Nullable String[] selectionArgs, @Nullable String sortOrder) {
        return null;
    }
    
    public String getType(@NonNull Uri uri) {
        return null;
    }

    public Uri insert(@NonNull Uri uri, @Nullable ContentValues values) {
        return null;
    }

    public int delete(@NonNull Uri uri, @Nullable String selection, @Nullable String[] selectionArgs) {
        return 0;
    }

    public int update(@NonNull Uri uri, @Nullable ContentValues values, @Nullable String selection, @Nullable String[] selectionArgs) {
        return 0;
    }
}
```

### 3.4 Broadcast Receiver

*广播接收器*是一种用于响应系统范围广播通知的组件。

声名方式:

1. 清单文件中静态声名

   ```xml
   <receiver android:name=".MyBroadcastReceiver"  android:exported="true">
       <intent-filter>
           <action android:name="android.intent.action.BOOT_COMPLETED"/>
           <action android:name="android.intent.action.INPUT_METHOD_CHANGED" />
       </intent-filter>
   </receiver>
   ```

2. 代码中动态声名

   ```java
   IntentFilter filter = new IntentFilter();
   filter.addAction("android.intent.action.BOOT_COMPLETED");
   filter.addAction("android.intent.action.INPUT_METHOD_CHANGED");
   registerReceiver(MyBroadcastReceiver, filter);
   // 不用时注销
   // unregisterReceiver(MyBroadcastReceiver);
   ```

#### 接收广播

```java
public class MyBroadcastReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        // do something
    }
}
```

#### 发送广播

```java
sendBroadcast(new Intent("package.action"));
```

#### LocalBroadcast

只在本应用内部传播, 轻量, 效率高.

声名方式

```java
IntentFilter filter = new IntentFilter();
LocalBroadcastManager.getInstance(this).registerReceiver(MyBroadcastReceiver, filter);
// 不用时注销
// LocalBroadcastManager.getInstance(this).unregisterReceiver(MyBroadcastReceiver);
```

发送广播

```java
LocalBroadcastManager.getInstance(this).sendBroadcast(intent);
```

## 4. UI

### 4.1 布局

常见布局有

- LinearLayout,
- RelativeLayout
- FrameLayout
- ConstraintLayout

#### 线性布局 LinearLayout

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="vertical" >
    <TextView android:id="@+id/text"
              android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:text="Hello, I am a TextView"
              android:layout_weight="1"/>
    <Button android:id="@+id/button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hello, I am a Button"
            android:layout_weight="2"/>
</LinearLayout>
```

使用线性布局必须指定方向 **orientation**, 可选值: *vertical*, *horizontal*.

子控件会按照指定的方向进行排列.

子控件可使用 *android:layout_weight="1"* 来设置权重. 如上面例子, 这个布局会将宽度等分成 3 份, 其中 TextView占 1份, Button占 2份.

#### 相对布局 RelativeLayout

相对布局的子控件会根据相对位置来展示.

```xml
<RelativeLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Text 1" />

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/text"
        android:text="Button"/>

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toEndOf="@id/button"
        android:layout_below="@id/text"
        android:text="Text 2"/>
</RelativeLayout>
```

![相对布局](relative_layout.png)

#### 帧布局 FrameLayout

一层一层的堆叠子控件. 后面的控件在上层.

#### 约束布局 ConstraintLayout

ConstraintLayout 允许您创建具有平面视图层次结构(没有嵌套视图组)的大型复杂布局。 这也是官方推荐的布局方式. 能够有效的减少布局层级.

```xml
<android.support.constraint.ConstraintLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Text 1"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Button"
        app:layout_constraintTop_toBottomOf="@id/text" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Text 2"
        app:layout_constraintStart_toEndOf="@id/button"
        app:layout_constraintTop_toTopOf="@id/button" />
</android.support.constraint.ConstraintLayout>
```

### 4.2 控件

常用控件

- TextView
- Button
- EditText
- ImageView
- CheckBox
- RadioButton
- Switch
- Spinner
- ProgressBar
- SeekBar...

高级控件

- ScrollView
- WebView
- ViewPager
- ListView
- RecyclerView

#### 常用属性

宽高 layout_width layout_height
    match_parent 填充父控件
    wrap_content 自适应
    200dp 具体大小

背景 background 可以是:

- 颜色
- drawable
- mipmap
- selector
- shape
- ...

外边距 margin

内边距 padding

#### 文本

```xml
<TextView
    android:id="@+id/text_test"
    android:layout_width="200dp"
    android:layout_height="wrap_content"
    android:text="Text 1"
    android:textColor="@color/colorAccent"
    android:textSize="14sp"
    android:textAlignment="center"
    android:textAllCaps="true"
    android:drawableStart="@mipmap/ic_launcher"
    android:drawablePadding="8dp" />
```

字体大小一律用sp表示

使用 drawableXX 可以为TextView添加icon

Button 也是 TextView, 只是加了个样式, 用法都一样

#### 输入框

```xml
<EditText
    android:layout_width="200dp"
    android:layout_height="wrap_content"
    android:layout_marginTop="16dp"
    android:hint="姓名"
    android:inputType="none"
    android:textColor="@color/colorPrimary"
    android:textColorHint="@color/colorPrimaryDark"
    android:textSize="16sp" />
```

EditTex也是扩展自 TextView 的, 所以TextView的用法, EditText都有.

输入类型 InputType 文本 数字 密码 等等..

输入提示 hint

#### 图像

```xml
<ImageView
    android:layout_width="200dp"
    android:layout_height="48dp"
    android:src="@mipmap/ic_launcher"
    android:scaleType="centerInside"/>
```

缩放 scaleType fitXY centerCrop centerInside fitCenter ...

资源 src 要显示的图片 跟background一样, 支持多种格式, ImageView会先绘制背景, 再绘制src

#### 其他控件

ScrollView 让视图可滑动, 毕竟屏幕只有那么大.  横向滑动 HorizontalScrollView, 新控件 NestedScrollView

CheckBox 选择框

RadioButton 单选框 配合 RadioGroup使用

Switch  开关

Spinner 下拉

ProgressBar  进度条

SeekBar  滑块

ViewPager *滑动分页*

ListView 列表 推荐使用 RecyclerView

#### RecyclerView

列表视图, 使用之前, 需要添加依赖.

```groovy
implementation 'com.android.support:recyclerview-v7:27.1.1'
```

XML声名, 可以在xml中设置布局管理器, 注意是App 命名空间

```xml
<android.support.v7.widget.RecyclerView
    android:id="@+id/recycler"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:layoutManager="android.support.v7.widget.LinearLayoutManager">
</android.support.v7.widget.RecyclerView>
```

java代码

```java
mRecyclerView.setLayoutManager(new LinearLayoutManager(getContext(), LinearLayoutManager.VERTICAL, false));

TestAdapter adapter = new TestAdapter();
List<String> list = new ArrayList<>();
list.add("Hello World");
list.add("Android");
list.add("SkyWorth");
adapter.setList(list);

mRecyclerView.setAdapter(adapter);
```

使用Recyclerview 必须要指定布局管理器.

填充数据需要用到适配器 *RecyclerView.Adapter*

创建一个Adapter 继承自 RecyclerView.Adapter. 同时(必须)重写三个方法.

+ onCreateViewHolder 在这里绑定布局
+ onBindViewHolder 在这里填充数据
+ getItemCount 返回数据的个数

```java
class TestAdapter extends RecyclerView.Adapter<RecyclerView.ViewHolder> {

    private List<String> mList;

    public void setList(List<String> list) {
        mList = list;
        notifyDataSetChanged();
    }

    @NonNull
    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.recycler_item_test, parent, false);
        return new TestHolder(view);
    }

    @Override
    public void onBindViewHolder(@NonNull RecyclerView.ViewHolder holder, int position) {
        ((TestHolder) holder).setData(mList.get(position));
    }

    @Override
    public int getItemCount() {
        return mList == null ? 0 : mList.size();
    }
    
    @Override
    public int getItemViewType(int position) {
        return super.getItemViewType(position);
    }

    class TestHolder extends RecyclerView.ViewHolder {

        private TextView mTextView;

        public TestHolder(View itemView) {
            super(itemView);

            mTextView = itemView.findViewById(R.id.text);
        }

        private void setData(String str) {
            mTextView.setText(str);
        }
    }
}
```

高级用法

- 自定义分割线(ItemDecoration)
- 自定义布局(LayoutManager)
- 自定义动画(ItemAnimator)
- 多布局(getItemViewType)

### 4.3 自定义控件

常用控件无法满足需求时, 就需要自行定义控件. 自定义控件分为两种 View 和 ViewGroup

创建自定义控件的class 继承自 View.

首先要重写构造函数

```java
public void YourView(Context context) {}
public void YourView(Context context, AttributeSet attrs) {}
public void YourView(Context context, AttributeSet attrs, int defStyleAttr) {}
public void YourView(Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes) {}
```

一个参数的是直接在java代码中新建view, 参数 attrs是接收来自xml的属性, 例如TextView的 text, textColor.

接着需要重写常用方法

```java
@Override
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
    int widthsize = MeasureSpec.getSize(widthMeasureSpec);      //取出宽度的确切数值
    int widthmode = MeasureSpec.getMode(widthMeasureSpec);      //取出宽度的测量模式

    int heightsize = MeasureSpec.getSize(heightMeasureSpec);    //取出高度的确切数值
    int heightmode = MeasureSpec.getMode(heightMeasureSpec);    //取出高度的测量模式
}

@Override
protected void onLayout(boolean changed, int left, int top, int right, int bottom) {
    super.onLayout(changed, left, top, right, bottom);
}

@Override
protected void onDraw(Canvas canvas) {
    canvas.drawCircle(100, 100, 50, mPaint);
}
```

onMeasure 是测量View大小. 其中测量模式有三种, 分别是 AT_MOST (match_parent), UNSPECIFIED (warp_content), EXACTLY (精确值). 调整好大小之后, 调用

```java
setMeasuredDimension(int measuredWidth, int measuredHeight)
```

onLayout 如果自定义的控件是一个ViewGroup, 则需要重写该方法, 来布局子控件.

onDraw 绘制, 绘制操作都是在这里进行. 主要是操作 canvas进行绘制. 该方法调用频率非常高, 因此不要在这个方法中创建对象. 

绘画之前, 了解一下 画笔 Paint 

```java
Paint mPaint = new Paint();
mPaint.setColor(Color.RED);
```

上面的onDraw 方法中, 绘制了一个圆. 坐标100, 100, 半径 50, 画笔颜色为红色. 

注意, 这里的数值都是像素.  我们也可以把像素转成dp.

```java
public static int px2dp(final float pxValue) {
    final float scale = AndLua.getContext().getResources().getDisplayMetrics().density;
    return (int) (pxValue / scale + 0.5f);
}
```

重绘 invalidate();

### 4.4 其他 UI

这里主要讲布局之外的界面

- Toast
- Dialog
- SnackBar
- PopupWindow

Toast 吐司, 做简单的提示

Dialog 弹框, 做强提示, 输入, 或者等待条

SnackBar 稍强于Toast, 轻量级提示. 可以有一个按钮. 多用于撤回

PopupWindow 弹窗. 类似Dialog, Dialog展示在页面中间, SnackBar展示在页面底部, PopupWindow展示在 目标view的周围.

## 5. 存储

四种方式

+ 文件存储
+ SharedPreference
+ SQLite
+ ContentProvider

### 5.1 SharedPreference. 

一般用于应用配置. 格式为 键值对.

获取数据

```java
Context context = getActivity();
SharedPreferences sharedPref = context.getSharedPreferences(
        getString(R.string.preference_file_key), Context.MODE_PRIVATE);
```

保存数据

```java
SharedPreferences sharedPref = getActivity().getPreferences(Context.MODE_PRIVATE);
SharedPreferences.Editor editor = sharedPref.edit();
editor.putInt(getString(R.string.saved_high_score_key), newHighScore);
editor.commit();
```

### 5.2 SQLite

数据库, 存储量大和复杂的数据. 比如记录唤醒, 时间, 关键词, 指令. 微信的聊天记录, 新闻App的缓存等等. 原生的SQLite操作略微繁琐. 建议使用第三方的 ORM(对象关系映射) 框架(GreenDao, Room). (*不用写 SQL语句*)

## 6. 多媒体

+ 音视频
+ 摄像头
+ 闹铃
+ 相册
+ ...

## 7. 资源

主要是res 和Assets下的内容. Assets 就是个文件系统, 用**getAssets()**去操作就好.

### 7.1 后缀

+ -xhdpi

  不同分辨率的屏幕下会使用不同后缀下的资源.

  ![](像素密度.png)

+ -v21

  系统版本 21及以后 采用这个后缀下的资源

主要介绍下res下的各个文件夹.

### 7.2 drawable

drawable这里存放的是 drawable(可绘制对象)资源, 这不是废话.重点是drawable资源都有什么.

+ xxx.png

  图片

+ shape.xml

  用于设置背景的形状, 圆角, 圆形, 边框等

+ selector.xml

  背景选择器. 比如一个按钮, 正常状态, 点击状态, 禁用状态, 聚焦状态都要怎么显示.

+ svg.xml

  在xml中画画

+ .9图

+ ...

如何使用?

+ 在xml中, 直接 R.drawable.xxx
+ 在Java代码中, getResources().getDrawable(R.drawable.xxx);

### 7.3  字符串, 颜色值, 数值

除了demo, 不建议在xml和java代码中出现字符串(strings), 颜色值(colors), 数值(dimens), 全部都写到对应的资源中. 

其实文件名可以随意的, 主要是xml中的标签. 

## 8. 传感器 & 硬件

- 光感
- 重力
- GPS
- 蓝牙
- WiFi
-  ...

获取传感器

```java
mSensorManager = (SensorManager) getSystemService(SENSOR_SERVICE);
        if (mSensorManager != null) {
            mSensorLight = mSensorManager.getDefaultSensor(Sensor.TYPE_LIGHT);
            if (mSensorLight == null) {
                Toastor.showLong("未检测到光感传感器");
            } else {
                mSensorManager.registerListener(mLightListener, mSensorLight, SensorManager.SENSOR_DELAY_NORMAL);
            }
        }
```

## 9. 网络

同样建议使用第三方库, OkHttp3, Retrofit2

原生示例

```java
//1.  URL
URL url = new URL(u);
//2.  HttpURLConnection
HttpURLConnection conn=(HttpURLConnection)url.openConnection();
//3.  set(GET)
conn.setRequestMethod("GET");
//4.  getInputStream
InputStream is = conn.getInputStream();
//5.  解析is，获取responseText，这里用缓冲字符流
BufferedReader reader = new BufferedReader(new InputStreamReader(is));
StringBuilder sb = new StringBuilder();
String line = null;
while((line=reader.readLine()) != null){
    sb.append(line);
}
//获取响应文本
String responseText = sb.toString();
```

OkHttp

```java
OkHttpClient client = new OkHttpClient();

String run(String url) throws IOException {
  Request request = new Request.Builder()
      .url(url)
      .build();

  Response response = client.newCall(request).execute();
  return response.body().string();
}
```

## 10. 线程

多线程与线程池讲解, 需要注意的是, 新建线程时, 不要使用 new Thread(). 一律使用 AsyncTask 或者 线程池.

这里主要讲 Handler. Android中更新 UI只能在主线程, 在子线程中需要更新UI的话, 使用Handler切换至主线程.

耗时操作, 比如网络请求、大量计算, 必须放在子线程.

## 11. 扩展

- Kotlin
- Flutter
- 组件化
- App架构
  - MVP
  - MVVM - DataBinding