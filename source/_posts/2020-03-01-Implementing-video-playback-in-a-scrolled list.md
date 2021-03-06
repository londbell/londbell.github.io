---
layout: post
title: "在滚动列表中播放视频"
date: 2020-03-01 23:35
comments: true
tags: 
	- 笔记
	- 转载 
	- 收藏 
	- android 
	- java 
---

> * 原文链接 : [Implementing video playback in a scrolled list (ListView & RecyclerView)](https://medium.com/@v.danylo/implementing-video-playback-in-a-scrolled-list-listview-recyclerview-d04bc2148429#.giv0pte0s)
>* 原文作者 : [Danylo Volokh](https://medium.com/@v.danylo)
>* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)

本篇博文将会介绍如何实现在列表中播放视频，具体效果参见：Facebook，Instagram 或 Magiston：

<!-- more -->

### Facebook

![facebook-listview-video][1]

### Magisto

![magisto-listview-video][2]

### Instagram

![Instagram-listview-video][3]

博文内容基于此Github 项目：[VideoPlayerManager](https://github.com/danylovolokh/VideoPlayerManager).

博文中涉及的所有代码和范例都在该项目中，所以本篇博文不会详细讲解每一个细节。如果有人真的想知道实现的机制，最好是下载源码，结合 IDE 边看源码边阅读本文。不过就算你不结合源码，光看我在这里说的内容，也能理解的七七八八了。

## 两个问题

为了实现这个功能，我们需要解决以下两个问题：

1. 管理视频播放。在 Android 框架层中我们可以利用 MediaPlayer 和 SurfaceView 来播放视频，但这种实现方式有许多缺点。我们不能在列表中使用一般的 VideoView，因为 VideoView 是 SurfaceView 的子类，而 SurfaceView 不具有 UI 异步缓存。而这会导致我们无法在滚动时记录视频到底播放到哪个时间点。TextureView 具有 UI 异步缓存，但在 Android SDK 15 没有 VideoView 的实现方式是借助 TextureView 来完成的。因此，我们需要一个 TextureView 的子类与 MediaPlayer 协作完成视频播放的功能。当然了，MediaPlayer 中的所有方法（准备，开始，停止等等……）几乎都是调用与硬件交互的内核层代码。而硬件往往意味着复杂，如果我们需要完成的任何工作的耗时超过 16ms（一般都会），就会看到滞后的视频列表。这也是为什么需要在后台线程中调用它们的原因。

2. 我们还需要知道滚动列表中的哪一个 View 要被激活（播放视频），所以我们还需要追踪用户的滚动行为并定义可见域最大的 View。

## 管理视频播放

我们希望提供以下功能：

假设某个视频正在播放，此时用户滚动了列表使得列表中某个子项目的可见域大于正在播放视频的子项目的可见域，这样我们就需要停止正在播放的视频，并开始播放新的视频（可见域更大的子项目对应的视频）。

![demo-listview-video-mananger][4]

##VideoPlayerView

我们要做的第一件事就是以 TextureView 为父类实现 VideoView。而且我们在滚动列表中不能使用系统的 VideoView，因为用户在视频播放的过程中滚动列表的话，视频渲染就会乱掉。

我把这部分工作分为几个部分：

1. 创建继承于 TextureView 的 ScalableTextureView，它能够调整 SurfaceTexture（正在播放视频的表面结构中）而且提供了一些类似于 ImageView 缩放类型的选项。

``` java
public enum ScaleType {
    CENTER_CROP, TOP, BOTTOM, FILL
}
```

2. 创建 ScalableTextureView 的子类 VideoPlayerView，它包含所有与 MediaPlayer 相关的功能。即，这是一个封装了 MediaPlayer 并提供与 VideoView 几近一致的 API 的自定义 View。VideoPlayerView 具有所有直接调用 MediaPlayer 的方法：setDataSource，prepare, start, stop, pause, reset, release。

## ViedioPlayer 管理器及消息控制机制

视频播放管理器与负责异步调用 MediaPlayer 方法的 MessageHandlerThread 协作。因为我们需要调用的方法，如：prepare(), start() 等等……与硬件直接相关，所以我们需要在一个独立的线程中完成这些调用。此外，当我们在 UI 线程中调用 MediaPlayer.reset() MediaPlayer 会发生一些奇怪的问题，使得该方法阻塞了将近 4 分钟！这也是我们不必须调用异步的 MediaPlayer.prepareAsync() 方法的原因，因为我们完全可以在另一个线程中调用 MediaPlayer.prepare() 方法，免得让这些奇怪的问题折腾自己。因此，我们将在一个独立的线程中异步完成所有任务。

在开始新的视频播放任务的事件流中，下面是一些与 MediaPlayer 相关的步骤：

1. 停止前一个播放任务，通过调用 MediaPlayer.stop() 方法完成。

2. 通过调用 MediaPlayer.reset 方法重置 MediaPlayer。之所以需要这样做是因为：在滚动列表中，View 可能会被重用，而我们想要释放所有的资源。

3. 通过调用 MediaPlayer.release() 方法释放 MediaPlayer 占用的资源。

4. 清除 MediaPlayer 的实例，当 View 执行新的播放任务时，创建新的 MediaPlayer 实例。

5. 为新的可见域最大的 View 创建 MediaPlayer 实例。

6. 调用 MediaPlayer.setDataSource(String url) 为新的 MediaPlayer 设置数据源。

7. 调用 MediaPlayer.prepare() 方法而不是 MediaPlayer.prepareAsync()。

8. 调用 MediaPlayer.start()。

9. 等待播放开始。

以上所有行为都被包裹到 Message 中，交给一个独立的线程完成，例如这是一个 Stop 命令的 Message，它将调用 VideoPlayerView.stop()，实际上他调用的是 MediaPlayer.stop()。我们需要自定义 Message，因为我们可能需要设置当前状态，例如现在是正在停止，还是已经停止，或者是其他的状态……这有助于我们了解当前处理的 Message 是什么命令，如果我们需要用这个命令的话我们可以做什么，例如，开始一个新的播放任务。

``` java
/**
 * This PlayerMessage calls {@link MediaPlayer#stop()} on the instance that is used inside {@link VideoPlayerView}
 */
public class Stop extends PlayerMessage {
    public Stop(VideoPlayerView videoView, VideoPlayerManagerCallback callback) {
        super(videoView, callback);
    }

    @Override
    protected void performAction(VideoPlayerView currentPlayer) {
        currentPlayer.stop();
    }

    @Override
    protected PlayerMessageState stateBefore() {
        return PlayerMessageState.STOPPING;
    }

    @Override
    protected PlayerMessageState stateAfter() {
        return PlayerMessageState.STOPPED;
    }
}
```

如果我们需要开始一个新的播放任务，我们只需要调用 VideoPlayerManager 的一个方法，它就会添加下列 Message 到 MessagesHandlerThread 中：

``` java
// pause the queue processing and check current state
// if current state is "started" then stop old playback
mPlayerHandler.addMessage(new Stop(mCurrentPlayer, this));
mPlayerHandler.addMessage(new Reset(mCurrentPlayer, this));
mPlayerHandler.addMessage(new Release(mCurrentPlayer, this));
mPlayerHandler.addMessage(new ClearPlayerInstance(mCurrentPlayer, this));
// set new video player view
mPlayerHandler.addMessage(new SetNewViewForPlayback(newVideoPlayerView, this));
// start new playback
mPlayerHandler.addMessages(Arrays.asList(
        new CreateNewPlayerInstance(videoPlayerView, this),
        new SetAssetsDataSourceMessage(videoPlayerView, assetFileDescriptor, this), // I use local file for demo
        new Prepare(videoPlayerView, this),
        new Start(videoPlayerView, this)
));
// resume queue processing
```

这些 Message 都是异步处理的，因此我们能在任意时刻停止消息队列对消息的处理，并投递新的消息，例如：

当前一个影片处于准备状态（已经调用了 MediaPlayer.prepare() 方法，而且 MediaPlayer.start() 方法正在消息队列中等待被执行），此时用户滚动了列表，所以我们需要在一个新的 View 上开始新的播放任务。在这种情况下我们会：

1. 停止正在执行的消息队列

2. 移除所有等待执行的消息

3. 投递 “Stop”, “Reset”, “Release”, “Clear Player instance” 这些消息给消息队列，它们将会在 Prepare 消息回调方法执行完成后立刻被执行。

4. 投递 “Create new Media Player instance”, “Set Current Media Player”（将 MediaPlayer 对象绑定到当前需要播放视频的消息上），“Set data source”, “Prepare”, “Start”等消息。这样就会在新的 View 上播放相应的视频。

这样我们就能依照我们设想的那样执行播放任务：停止前一个播放任务，并开始新的播放任务。

下面是相关的依赖：

``` gradle
dependencies {
    compile 'com.github.danylovolokh:video-player-manager:0.2.0'
}
```

## 区分列表中可见域最大的 View——列表可见域判断工具

第一个问题是控制视频的播放，第二个问题则是判断哪一个 View 可见域最大，并切换该 View 对应的视频为新的播放视频。下面是调用 ListItemsVisibilityCalculator 的实体，它的具体实现 SingleListViewItemActiveCalculator 会完成对应的工作。

在 Adapter 中被使用的 Model 类必须实现 ListItem 接口，以计算 List 中子项目的可见域：

``` java
/**
 * A general interface for list items.
 * This interface is used by {@link ListItemsVisibilityCalculator}
 *
 * @author danylo.volokh
 */
public interface ListItem {
    /**
     * When this method is called, the implementation should provide a
     * visibility percents in range 0 - 100 %
     * @param view the view which visibility percent should be
     * calculated.
     * Note: visibility doesn't have to depend on the visibility of a
     * full view. 
     * It might be calculated by calculating the visibility of any
     * inner View
     *
     * @return percents of visibility
     */
    int getVisibilityPercents(View view);

    /**
     * When view visibility become bigger than "current active" view
     * visibility then the new view becomes active.
     * This method is called
     */
    void setActive(View newActiveView, int newActiveViewPosition);

    /**
     * There might be a case when not only new view becomes active,
     * but also when no view is active.
     * When view should stop being active this method is called
     */
    void deactivate(View currentView, int position);
}
```

ListItemsVisibilityCalculator 将追踪滚动方向，并在运行时计算子项目的可见域。子项目的可见域依赖于列表中的任意一个咨询项目，即取决于你实现 getVisibilityPercents() 方法的方式：

``` java
/**
 * This method calculates visibility percentage of currentView.
 * This method works correctly when currentView is smaller then it's enclosure.
 * @param currentView - view which visibility should be calculated
 * @return currentView visibility percents
 */
@Override
public int getVisibilityPercents(View currentView) {

    int percents = 100;

    currentView.getLocalVisibleRect(mCurrentViewRect);

    int height = currentView.getHeight();

    if(viewIsPartiallyHiddenTop()){
        // view is partially hidden behind the top edge
    percents = (height - mCurrentViewRect.top) * 100 / height;
    } else if(viewIsPartiallyHiddenBottom(height)){
        percents = mCurrentViewRect.bottom * 100 / height;
    }

    return percents;
}
```

因此，每一个 View 都需要知道计算其可见域的细节。当发生滚动时，SingleListViewItemActiveCalculator 会要求每一个 View 计算其可见域大小，这也意味其开销之重。

当任意一个子项目的可见域大小超过了当前播放视频项目的可见域大小，就会调用 setActive 方法切换播放任务。

下面是与 Adapter 功能类似的的 ItemsPositionGetter 的抽象逻辑，它将建立 ListItemsVisibilityCalculator 与 ListView/RecyclerView 交互的桥梁。这样的话 ListItemsVisibilityCalculator 不需要了解它需要操作的 View 到底是 ListView 还是 RecyclerView。只需要完成它自己的工作，但仍需要一些由 ItemsPositionGetter 提供的信息。

``` java
/**
 * This class is an API for {@link ListItemsVisibilityCalculator}
 * Using this class is can access all the data from RecyclerView / 
 * ListView
 *
 * There is two different implementations for ListView and for 
 * RecyclerView.
 * RecyclerView introduced LayoutManager that's why some of data moved
 * there
 *
 * Created by danylo.volokh on 9/20/2015.
 */
public interface ItemsPositionGetter {
   View getChildAt(int position);

    int indexOfChild(View view);

    int getChildCount();

    int getLastVisiblePosition();

    int getFirstVisiblePosition();
}
```

这样的实现无疑会让 Model 类混入一些业务逻辑，违反了设计模式的部分原则。但通过一些简单的修改就能将它们解耦。不过现在这样它用起来也没多大的问题。

下面是预览图和依赖：

![demo-listview-video-mananger][5]

``` gradle
dependencies {
    compile 'com.github.danylovolokh:list-visibility-utils:0.2.0'
}
```

## 组合使用上面完成的工作

现在我们需要将上面的工具库结合起来使用以完成我们的功能，下面是使用 RecyclerView 实现的代码：

1. 初始化 ListItemsVisibilityCalculator，并传递列表的引用

``` java
/**
 * Only the one (most visible) view should be active (and playing).
 * To calculate visibility of views we use {@link SingleListViewItemActiveCalculator}
 */
private final ListItemsVisibilityCalculator mVideoVisibilityCalculator = new SingleListViewItemActiveCalculator(
new DefaultSingleItemCalculatorCallback(), mList);
```

当被激活（播放视频）的 View 发生改变，DefaultSingleItemCalculatorCallback 只调用 ListItem.setActive 方法，但你可以重载该方法并按照你的需求实现相应逻辑：

``` java
/**
 * Methods of this callback will be called when new active item is found {@link Callback#activateNewCurrentItem(ListItem, View, int)}
 * or when there is no active item {@link Callback#deactivateCurrentItem(ListItem, View, int)} - this might happen when user scrolls really fast
 */
public interface Callback<T extends ListItem>{
    void activateNewCurrentItem(T item, View view, int position);
    void deactivateCurrentItem(T item, View view, int position);
}
```

2. 初始化 VideoPlayerManager

``` java
/**
 * Here we use {@link SingleVideoPlayerManager}, which means that only one video playback is possible.
 */
private final VideoPlayerManager<MetaData> mVideoPlayerManager = new SingleVideoPlayerManager(new PlayerItemChangeListener() {
    @Override
    public void onPlayerItemChanged(MetaData metaData) {

    }
});
```

3. 为 RecyclerView 设置滚动监听，并传递滚动事件给列表可见工具库处理。

``` java
@Override
public void onScrollStateChanged(RecyclerView view, int scrollState) {
 mScrollState = scrollState;
 if(scrollState == RecyclerView.SCROLL_STATE_IDLE && mList.isEmpty()){

 mVideoVisibilityCalculator.onScrollStateIdle(
          mItemsPositionGetter,
          mLayoutManager.findFirstVisibleItemPosition(),
          mLayoutManager.findLastVisibleItemPosition());
 }
 }

@Override
public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
 if(!mList.isEmpty()){
   mVideoVisibilityCalculator.onScroll(
         mItemsPositionGetter,
         mLayoutManager.findFirstVisibleItemPosition(),
         mLayoutManager.findLastVisibleItemPosition() -
         mLayoutManager.findFirstVisibleItemPosition() + 1,
         mScrollState);
 }
}
});
```

4. 创建 ItemsPositionGetter

``` java
ItemsPositionGetter mItemsPositionGetter = 
new RecyclerViewItemPositionGetter(mLayoutManager, mRecyclerView);
```

5. 在 onResume() 中调用方法以开始计算子项目的可见域，在我们显示出列表时选出可见域最大的子项目

``` java
@Override
public void onResume() {
    super.onResume();
    if(!mList.isEmpty()){
        // need to call this method from list view handler in order to have filled list

        mRecyclerView.post(new Runnable() {
            @Override
            public void run() {

                mVideoVisibilityCalculator.onScrollStateIdle(
                        mItemsPositionGetter,
                        mLayoutManager.findFirstVisibleItemPosition(),
                        mLayoutManager.findLastVisibleItemPosition());

            }
        });
    }
}
```

然后就完成了，在滚动列表中播放许多视频：

![final-demo-listview-video][6]

这个项目关键部分的讲解基本上已经完成了，下面是具体的代码和范例：

[https://github.com/danylovolokh/VideoPlayerManager](https://github.com/danylovolokh/VideoPlayerManager)

感谢阅读;

  [1]: https://cdn.jsdelivr.net/gh/londbell/pic/img/facebook-listview-video.gif
  [2]: https://cdn.jsdelivr.net/gh/londbell/pic/img/magisto-listview-video.gif
  [3]: https://cdn.jsdelivr.net/gh/londbell/pic/img/Instagram-listview-vieo.gif
  [4]: https://cdn.jsdelivr.net/gh/londbell/pic/img/demo-listview-video-mananger.gif
  [5]: https://cdn.jsdelivr.net/gh/londbell/pic/img/simple-demo-listview-video.gif
  [6]: https://cdn.jsdelivr.net/gh/londbell/pic/img/final-demo-listview-video.gif
  