# AisenForAndroid - 重构中
----

Aisen一个免费开源、面向AOP、遵循Apache2开源协议发布的Android轻量级快速开发框架。初衷是将面向互联网应用开发过程中大部分重复工作抽象出来，基于反射注入的方式实现具体业务逻辑，让开发人员更专注于项目的业务或者UI特效，不需要关注架构方面的SDK业务接口封装、网络协议、缓存管理之间的耦合关联，以及UI层面常见刷新控件、分页加载、图片OOM、Adapter优化等最常见的UI工作；

Aisen微博是个人实验性项目，依赖AisenForAndroid开发，UI遵循Material Design，也会在部分功能实现上依赖Github上其他优秀的开源组件或者框架，作为范例让感兴趣的同志更加了解AisenForAndroid或者其他项目的使用。

流程图

![](https://github.com/wangdan/AisenForAndroid/raw/master/screenshots/aisen_.jpg) 

## UI层

### 概要说明

1、处理4种基本视图之间的自动切换；
2、统一ListView、GridView、ViewPager等列表容器的刷新逻辑；
3、处理分页加载线程的业务逻辑；

1、ABaseFragment

维护一个主业务线程，通过线程状态的回调，自动维护四种基本视图Loading、Empty、Failure、Content之间的动态切换。

流程图

![](https://github.com/wangdan/AisenForAndroid/raw/master/screenshots/abasefragment.jpg) 

效果图

![](https://github.com/wangdan/AisenForAndroid/raw/master/screenshots/abaseview_v2.jpg) 

2、APagingFragment

2.2.1概要说明
		继承ABaseView，扩展Adapter支持，通过子类分页器配置，维护一个分页线程，自动根据刷新状态、分页等参数调度线程刷新服务端的业务数据，将数据自动append到adapter的数据池中刷新列表；支持ListView、GridView、ViewPager等控件的扩展。支持各种上下拉控件的扩展。
		在Android端，基本所有的列表容器：ListView、GridView、ViewPager等，都是通过适配器BaseAdapter来操作数据，所以APagingView的工作就是维护好一个Adapter，剥离一个分页接口切面，通过分页器的配置，根据用户上拉、下拉的操作，自动的将分页参数传递给子类，让子类自行调度SDK方法完成数据的拉取，获取到数据后，根据刷新模式自动的完成列表页面刷新。即下拉刷新将数据拼接在adapter数据头部，上拉刷新将数据拼接在adapter数据尾部，reset刷新将数据重置adapter。以上所有的操作自动调度，子类只需要实现已经重用了的ItemView(你理解的ViewHolder)，基本一个列表界面3-4个方法搞定。
		
效果图

![](https://github.com/wangdan/AisenForAndroid/raw/master/screenshots/alistview.jpg) 

- [ABaseFragment](https://github.com/wangdan/AisenForAndroid/wiki/五、ABaseFragment)
- ARefreshFragment
  - ASwipeRefreshListFragment
  - AListFragment
- AStripTabsFragment
  - AAutoReleaseStripTabsFragment
- 

## SDK接口层
>将分页、缓存、线程等都归纳在这里

- ABizLogic
- Cache
- Paging
  - PageIndexPaging
- WorkTask
  - TaskException

## 网络协议层

- IHttpUtility
  - HttpConfig
  - Params

## 开发组件

- BitmapLoader
- SqliteUtility

# 使用

**1、依赖关系**
>我只能用这两句话描述了

AisenForAndroid依赖android-support-v7-appcompat<br/>
AisenSample依赖AisenForAndroid

**2、继承GlobalContext**

你的项目Application必须继承自[GlobalContext](https://github.com/wangdan/AisenForAndroid/blob/master/AisenForAndroid/src/com/m/common/context/GlobalContext.java)


# 老的内容，稍后删
## UI层
定义Loading、Failure、Empty、Empty、Content5个基本视图。根据异步线程执行的业务方法状态，自动的维护这5个视图之间的状态切换。
效果
	

解决互联网应用过程中常见的问题，例如：
  * UI层，定义、管理基本视图的状态切换，封装列表视图的状态控制；
  * SDK层，申明客户端所有接口方法，定义入参和出参以及业务异常；
  * Cache层，定义缓存接口，自由配置每个接口的缓存方式；
  * Network层，定义网络接口，
  * 图片加载框架，一行代码完成图片的加载绑定，优化OOM、错位、压缩、网络开销等问题；
  * Sqlite的ORM操作组件，基于GreenDAO框架，面向对象的一行代码CRUD操作；
  * IOC注解，UI组件的绑定，一般性事件设置；


## 我能做什么

 * 使用ViewInject绑定view
  * fragment可以
  * activity可以
  * adapter的itemview可以
  * 任何地方
 * BitmapLoader，一行代码加载图片
  * 可以加载web、sdcard、assets、drawable、contentprovider、任何地方
  * 可以裁剪图片、圆角、压缩、任意处理
  * 二级缓存
  * 下载进度回调，开始、错误、进度、结束等事件
  * 其他配置：Displayer、LRU算法
 * ORM
  * 一行代码针对object的sqlite增删改查操作
 * 业务接口规范：[BizLogic](https://github.com/wangdan/AisenForAndroid/wiki/二、BizLogic)
  * 看这里[SinaSDK](https://github.com/wangdan/AisenWeiBo/blob/master/AisenWeiBo/sdk/org/sina/android/SinaSDK.java)，再看这里[OSCSdk](https://github.com/wangdan/AisenForOSC/blob/master/AisenForOSC/sdk/org/aisen/osc/sdk/OSCSdk.java)
 * 网络通讯协议规范：[HttpUtlitiy](https://github.com/wangdan/AisenForAndroid/wiki/三、HttpUtility)
  * http、https
  * soap
  * socket
  * 等
 * 接口缓存管理
  * 将接口数据保存为文件
  * 将接口数据保存到db
  * 将接口数据保存到任何你想保存的地方
 * 异步线程：[WorkTask](https://github.com/wangdan/AisenForAndroid/wiki/附、WorkTask)

## 我还能做什么

 * Loading视图、Failure视图、Empty视图、Empty视图、Content视图这5中基本视图的切换管理，你什么java代码都不需要写，只需要在layout的xml文件中按照自己的需求绘制5种视图的ui部分，就能
  * 加载数据时显示Loading界面
  * 加载失败显示Failure界面，将faild的提示信息绑定到ui显示，如果有ReloadBtn点击自动刷新
  * 加载空数据显示Empty界面，将empty的提示信息绑定到ui显示，如果有ReloadBtn点击自动刷新
  * 加载成功后显示Content界面
 * 只需实现3个方法，你就做完了一个刷新列表界面（支持三种刷新控件：示例一、示例二、示例三）。它有这些功能：
  * 支持ListView、GridView
  * 上拉刷新、自动加载更多
  * 接口分页加载
  * 自动保存、刷新缓存数据
  * 缓存数据时效过期自动刷新列表
  * 保存阅读位置
  * 资源重用
 * app运行时切换网络通讯协议
  * 某些项目，根据网络环境切换网络通讯协议，网段内实现p2p的wifi socket协议通讯，网段外与云服务器的http+soap+https协议通讯，或者与硬件交互的bluetooth socket协议通讯等。不管什么协议之间的切换，只需要一行代码动态配置即可
 * 事件传递机制
  * 没有任何耦合，任何异常信息以Exception的形式上报至UI层。UI层只处理异常信息的UI反馈，业务接口层上报业务异常信息(登录失败、表单错误等业务相关)，网络通讯层上报通讯异常信息(无网络、连接超时等)
 * 4层结构，层与层之间低耦合，恪守单一职责原则，面向AOP
  * UI层，处理ui上的特效、事件、基本的业务逻辑处理
  * SDK层(业务接口层)，规范所有与服务端通讯的接口定义，包括定义方法的入参、出参、异常
  * HttpUtility层(网络通讯层)，实现app所支持的网络通讯协议
  * CacheUtility层(持久层)，针对业务接口的数据持久管理


## ORM(SqliteUtility)
假设你已经熟悉sqlite操作，那么，SqliteUtility的相关api方法就不需要再说太多。同样面向对象，一行代码对数据库进行增删改查操作，但是剔除了一对一或者一对多这些操作，更多的示例代码请查看[SqliteUtility](https://github.com/wangdan/AisenForAndroid/wiki/附、SqliteUtility "SqliteUtility") 。

## IOC(ViewInject)
继承BaseActivity、ABaseFragment等均可以使用ViewInject对属性自动装配，可以给view设置点击事件，像这样
```java
@ViewInject(idStr = "layoutLoadFailed", click = "reload")
View loadFailureLayout;// 加载失败视图
```

或者像ItemView，也可以自动绑定，像这样
```java
public class TimelineItemView extends AbstractItemView<StatusContent> 
											implements OnClickListener {
	@ViewInject(id = R.id.imgPhoto)
	ImageView imgPhoto;
	@ViewInject(id = R.id.txtName)
	TextView txtName;
  ...
}
```

目前只支持click事件，我是个很实在的人，确实还没有遇到其他例如OnLongClick事件需要绑定就没有添加支持。

## BitmapLoader

请使用这一行代码加载图片
```java
BitmapLoader.display(BitmapOwner owner, String url, ImageView imageView, ImageConfig ImageConfig)
```

更多详细请查看[BitmapLoader](https://github.com/wangdan/AisenForAndroid/wiki/附、BitmapLoader "BitmapLoader")

## 依赖工程
 * [SmoothProgressBar](https://github.com/castorflex/SmoothProgressBar)
 * [PhotoView](https://github.com/chrisbanes/PhotoView)
 * [ActionBar-PullToRefresh](https://github.com/chrisbanes/ActionBar-PullToRefresh)
 * [Android-PullToRefresh](https://github.com/chrisbanes/Android-PullToRefresh)
 * [DragSortListView](https://github.com/bauerca/drag-sort-listview)
 * [ChangeLog](https://github.com/gabrielemariotti/changeloglib)
 * [DateTimePicker](https://github.com/flavienlaurent/datetimepicker)
 * [Android PagerSlidingTabStrip](https://github.com/astuetz/PagerSlidingTabStrip)


## License

Copyright (c) 2014 Jeff Wang

Licensed under the [Apache License, Version 3.0](http://opensource.org/licenses/GPL-3.0)

