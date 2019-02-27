#### Themes

##### dialog style

    //背景透明设置圆角布局即可实现圆角
    <style name="MyBaseDialog" parent="@android:style/Theme.Dialog">
        //不显示frame框
        <item name="android:windowFrame">@null</item>
        //是否浮现在activity之上
        <item name="android:windowIsFloating">true</item>
        //窗口是否透明
        <item name="android:windowIsTranslucent">true</item>
        
        <item name="android:windowNoTitle">true</item>
        //内容层背景颜色
        <item name="android:background">@android:color/transparent</item>
        
        <item name="android:windowBackground">@android:color/transparent</item>
        //是否允许dialog的背景变暗，突出弹窗的交互
        <item name="android:backgroundDimEnabled">true</item>
        //背景灰度
        <item name="android:backgroundDimAmount">0.6</item>
        //动画设置
        <item name="android:windowAnimationStyle">@style/dialog_animation</item>
    </style>
    
    
#### 关于资源找不到的原因
- 可能是编译问题，clean重新编译
- textView.setText(param),param只能为字符串，如果是int则会从资源中找，找不到该id也会出现此问题

