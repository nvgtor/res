# XhsEmoticonsKeyboard开源表情键盘使用与分析

## 项目特点
[github地址][1]

## 在APP中的使用

### 导入Module EmojiLibrary

- 导入源码方便查看已经更改，以适配项目需求
- 主要包括了一系列自定义键盘布局
- 表情集以及适配器
- 动画效果
- 事件监听

### 布局

- 改动：把原来聊天布局的父布局换成自定义的 SundlandsEmoticonsKeyboard
- 在 SundlandsEmoticonsKeyboard 中初始化输入框的布局以及表情布局和功能布局
- 在SundlandsEmoticonsKeyboard 处理相应的事件监听以及回调

### Activity中使用

- 初始化 initEmoticonsKeyBoardBar()，完成EmoticonsEditText的初始化、添加表情集的适配器、添加发送照片布局以及响应相关事件回调（本版本未做处理）、
- 监听系统键盘删除事件
- 监听聊天列表 ouTouch 事件隐藏软键盘
- 监听发送消息事件

### 发送和显示表情流程

- 监听 EmoticonsEditText 的 onTextChanged() 方法，回调给 SunlandsFilter 的 filter()方法，这里可以处理输入框显示表情，本项目需求并不需要在输入框中显示表情，需要在这里取消掉改动能
- 发送表情文字到服务端，沿用原来的服务器请求方法
- 解析表情字符，例如 [微笑]、[献媚],在聊天列表中将其替换为对应表情图片

### 根据实际项目需求做的变化与适应

- 去掉源码中的发送语音的布局
- 添加项目需求中的表情集
- 更改表情ViewPager指示器单页不显示的问题
- 在输入框中删除事件中，对删除字符是表情字符的情况特殊处理
- 根据实际项目改变隐藏软键盘的策略

## 源码分析

### 视图结构

![视图树][2]

### 类图

![类图结构][3]

#### SoftKeyboardSizeWatchLayout

继承 RelativeLayout 主要在构造函数中监听了视图树的变化，得到全屏可视高度即不包含状态栏的全屏高度和软键盘的高度，并通过接口OnResizeListener中的OnSoftPop()方法，传递软键盘的高度

```
public SoftKeyboardSizeWatchLayout(Context context, AttributeSet attrs) {
        super(context, attrs);
        this.mContext = context;
        getViewTreeObserver().addOnGlobalLayoutListener(new ViewTreeObserver.OnGlobalLayoutListener() {
            @Override
            public void onGlobalLayout() {
                Rect r = new Rect();
                ((Activity) mContext).getWindow().getDecorView().getWindowVisibleDisplayFrame(r);
                if (mScreenHeight == 0) {
                    mScreenHeight = r.bottom;
                }
                mNowh = mScreenHeight - r.bottom;
                if (mOldh != -1 && mNowh != mOldh) {
                    if (mNowh > 0) {
                        mIsSoftKeyboardPop = true;
                        if (mListenerList != null) {
                            for (OnResizeListener l : mListenerList) {
                                l.OnSoftPop(mNowh);
                            }
                        }
                    } else {
                        mIsSoftKeyboardPop = false;
                        if (mListenerList != null) {
                            for (OnResizeListener l : mListenerList) {
                                l.OnSoftClose();
                            }
                        }
                    }
                }
                mOldh = mNowh;
            }
        });
    }
```

#### AutoHeightLayout

这是一个抽象类，继承 SoftKeyboardSizeWatchLayout，包含抽象方法,子类实现该方法获得软键盘的高度

```
public abstract void onSoftKeyboardHeightChanged(int height);
```

该类中主要完成布局键盘高度和
 

  [1]: https://github.com/w446108264/XhsEmoticonsKeyboard
  [2]: https://raw.githubusercontent.com/w446108264/XhsEmoticonsKeyboard/master/output/treeview.png
  [3]: https://raw.githubusercontent.com/nvgtor/res/master/XhsEmoticonsKeyBoard.png