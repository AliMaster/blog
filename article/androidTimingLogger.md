####打印程序时间的利器TimingLogger
TimingLogger是安卓自带的一个打印时间的日志工具类
官方文档地址[https://developer.android.com/reference/android/util/TimingLogger.html](https://developer.android.com/reference/android/util/TimingLogger.html)
文档显示在api为1的时候就已经添加了

官方介绍如下：

>A utility class to help log timings splits throughout a method call. Typical usage is:
>
     TimingLogger timings = new TimingLogger(TAG, "methodA");
     // ... do some work A ...
     timings.addSplit("work A");
     // ... do some work B ...
     timings.addSplit("work B");
     // ... do some work C ...
     timings.addSplit("work C");
     timings.dumpToLog();
 
>The dumpToLog call would add the following to the log:
>
     D/TAG     ( 3459): methodA: begin
     D/TAG     ( 3459): methodA:      9 ms, work A
     D/TAG     ( 3459): methodA:      1 ms, work B
     D/TAG     ( 3459): methodA:      6 ms, work C
     D/TAG     ( 3459): methodA: end, 16 ms
 

然后在程序中直接使用时时不起作用的，必须设置TAG的日志输出优先级为 **VERBOSE** 以上
**在console中执行命令即可：xxx表示上面自定义的TAG**
> adb shell setprop log.tag.**xxx** VERBOSE

原因是Timinglogger在初始化的时候，执行了一个reset方法，其中判断了当前tag的输出等级：
```/**
     * Clear and initialize a TimingLogger object that will log using
     * the tag and label that was specified previously, either via
     * the constructor or a call to reset(tag, label). If the
     * Log.isLoggable is not enabled to at least the Log.VERBOSE
     * level for that tag at creation time then the addSplit and
     * dumpToLog call will do nothing.
     */
    public void reset() {
        mDisabled = !Log.isLoggable(mTag, Log.VERBOSE);
        if (mDisabled) return;
        if (mSplits == null) {
            mSplits = new ArrayList<Long>();
            mSplitLabels = new ArrayList<String>();
        } else {
            mSplits.clear();
            mSplitLabels.clear();
        }
        addSplit(null);
    }
```

然而在实际测试中发现，有些rom就算改了日志等级，依旧无法打印。

