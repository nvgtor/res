---
title: APP视频模块
date: 2016-07-25 01:06:22
categories: Android笔记
tags: [gensee]
---

# 展示互动Gensee

点播SDK：VodSDK   播放控制类：GenseePointVideoActivity

## 入口：

### 形式一
	/**
	* castId:点播id	
	* className：课程名称
	* teacherUnitId：FreeCourseEntity. classVideo
	* quizzesGroupId: 都传了空
	*/
	newIntent(Context context, String castId, String className, int teacherUnitId, String quizzesGroupId)

#### SectionPostDetailFragment:  帖子详情
#### HistoryCourseFragment：  免费课
#### HomeFreeCardHistoryFragment: teacherUnitId = 0,其他3个不为0
#### MostPopularCourseFragment

### 形式二
	/**
	* onlyLandScape：true 横屏
	*/
	newIntent(Context context, String castId, String className, int teacherUnitId,String quizzesGroupId,boolean onlyLandScape)

#### MyDownloadActivity-----VideoDownloadDoneResourceFragment-----VideoDownloadDoneResourcePresenter-----VideoDownloadDoneAdapter-----VideoDownloadDoneItemView  下载
			

### 形式三

	/**
	* RemindingTaskEntity remindingTaskEntity
	* stutas 课程状态 
	* stutas ==0 即将开始
	* stutas ==1 上课中	
	* stutas ==2已结束,有录播id或者课件可以点击进入
	* stutas ==3 预习
	* stutas ==4：复习->有录播或者有课件可以点击进入
	* 2、4状态 可以进入 点播
	*/
	newIntent(Context context, RemindingTaskEntity remindingTaskEntity, int stutas)

#### HomeVipFragment   VIP页

### 形式四

	/**
	* ourseEntity courseEntity
	* stutas 课程状态 
	* stutas ==0 即将开始
	* stutas ==1 上课中	
	* stutas ==2已结束,有录播id或者课件可以点击进入
	* stutas ==3 预习
	* stutas ==4：复习->有录播或者有课件可以点击进入
	* 2、4状态 可以进入 点播
	*/
	newIntent(Context context, CourseEntity courseEntity, int stutas)

#### CoursePackageDetailCourseFragment： 课程详情
#### ScheduleActivity-----ScheduleCourseAdapter 日期控件 提示

## 点播VodSDK功能

主要功能在 VodSite、 VodDownloader 和 VodPlayer 中。

###主要回调： 

- 点播信息回调 OnVodListener：
点播信息回调是通过点播编号或点播 id进行初始化，得到播放或下载的参数， 该参数还可以获取点播的聊天记录或问答记录的响应。

- 下载状态和下载进度回调 OnDownloadListener：
OnDownloadListener 作为通知 app下载状态和下载进度的接口定义，在点播下载过程中会相应的调用所定义的函数， app需要实现本接口并传递一个有效的实例给 sdk。注：回调函数非 UI 线程线程调用，要更新 UI 请在 UI 线程里面更新。

- 播放状态回调 OnVodPlayListener
播放状态回调包含了播放的状态、错误、进度的上报， 在播放过程中被调用， 一般情况下都需要实现该接口，并做好相关处理。

### 调用API: 

1. VodSite 点播管理器、InitParam 初始化参数结构、VodDownLoader 点播下载器、VODPlayer 点播播放器

2. UI:
GSDocViewGx (GSDocView) 文档显示 UI、
GSVideoView视频显示 UI

### 点播主要使用步骤：

1. VodSite.init(context,null) 初始化
2. vodSite = new VodSite(context) 创建实例 
3. vodSite.setVodListener(onVodListener) 设置监听
4. vodSite.getVodObject(initParam) 获取点播对象
5. onVodListener.onVodObject(String vodId){
	得到点播 id，此时 vodId可以用来下载或播放
	}
6. VodSite.release() 进行释放

### 下载点播
1. VodDownLoader downloader = VodDownLoader.instance(appContext,
	downloadListener,saveDir);
2. downloader.download(vodId) //下载或停止后恢复下载
3. downloader.stop(vodId) //停止(暂停)下载
4. onDownloadListener.onFinished(String vodId,String localPath){
		下载完成后通知点播的本地路径： localPath
	}//下载完成响应

### 播放

1. 创建播放器实例
VODPlayer player = new VODPlayer ();
2. 设置视频显示 view,用于显示视频画面
player.setGSVideoView(gsVideoView);
设置文档显示 view	， 用于显示文档内容。
player.setGSDocViewGX(gsDocViewGx);
3. 开始播放

	播放已经下载好的点播
	player.play (localPath, vodPlaylistener, "");

	直接在线播放
	player.play (vodId, vodPlaylistener, "");

## GenseePointVideoActivity

### 布局

![](http://thumbnail0.baidupcs.com/thumbnail/aaf2e526ed9169194454c34b37967fcd?fid=1277786617-250528-343160436351928&time=1469376000&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-T6T4Ne1GbxCOLrM9Y8G75KH90LE%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=4778199767101001108&dp-callid=0&size=c710_u400&quality=100)

### 类图

![](http://thumbnail0.baidupcs.com/thumbnail/78dac6c27bc8864a0621600ca3ff4d19?fid=1277786617-250528-577862666497047&time=1469376000&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-mnktGEnA4Tj5AHU7CYtyvOMqaKY%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=4778184400197523182&dp-callid=0&size=c710_u400&quality=100)

### 主要方法

#### onCreate()
- initViews(); //initView
- initData();  //getIntent data
- initFragment();
	- 判断LoadView可见性以及副屏幕可见性
	- registerReceiver(); 监听网络变化
	- loadVideo() --> initGensee();初始化视频
	- VideoFloatFragment
	- init --> chatFragment、quizzFragment、feedBackFragment、attachmentFragment

#### onStart()

- initOrientationEventListener() 手机重力监听

#### onResume()

- setVideoLiveStatus(isLive)

#### onStop()

- setVideoLiveStatus(isLive)

#### onDestroy()

- unregisterReceiver();
- sdkPresenter.release();
- chatFragment.onDestroyView();
- feedBackFragment.onDestroyView();
- quizzFragment.onDestroyView();
- floatFragment.onDestroyView();

# 欢脱
