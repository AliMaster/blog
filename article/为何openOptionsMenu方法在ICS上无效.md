项目中一个遇到一些用户反馈在三星的一些pad上无法弹出菜单，但是其他手机上就没有问题。而我们借助optionMenu来实现的菜单。查阅资料后在stackoverflow找到了原因。
详情参见 http://stackoverflow.com/questions/9996333/openoptionsmenu-function-not-working-in-ics

简单说一下原因。
在**com.android.internal.policy.impl.PhoneWindow** 这个类里面代码如下

```java
private void openPanel(PanelFeatureState st, KeyEvent event) {
        // System.out.println("Open panel: isOpen=" + st.isOpen);

        // Already open, return
        if (st.isOpen || isDestroyed()) {
            return;
        }

        // Don't open an options panel for honeycomb apps on xlarge devices.
        // (The app should be using an action bar for menu items.)
        if (st.featureId == FEATURE_OPTIONS_PANEL) {
            Context context = getContext();
            Configuration config = context.getResources().getConfiguration();
            boolean isXLarge = (config.screenLayout & Configuration.SCREENLAYOUT_SIZE_MASK) ==
                    Configuration.SCREENLAYOUT_SIZE_XLARGE;
            boolean isHoneycombApp = context.getApplicationInfo().targetSdkVersion >=
                    android.os.Build.VERSION_CODES.HONEYCOMB;

            if (isXLarge && isHoneycombApp) {
                return;
            }
        }
        ...
   }
```

**也就是说api >= 11且为XLarge屏幕的话，android是不会使用optionMenu的，而且官方强烈安利用action bar来代替**

解决方法，上面stackoverflow里也有很多人回复了，在此就不贴了，追本溯源，还是不要用optionMenu了


