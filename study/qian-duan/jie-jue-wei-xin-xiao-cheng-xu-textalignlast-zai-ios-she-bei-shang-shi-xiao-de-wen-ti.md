# 解决微信小程序text-align-last在ios设备上失效的问题

## 解决微信小程序text-align-last在ios设备上失效的问题

[https://www.csdn.net/tags/MtTaEg0sMzgzMjItYmxvZwO0O0OO0O0O.html](https://www.csdn.net/tags/MtTaEg0sMzgzMjItYmxvZwO0O0OO0O0O.html)

问题描述：我的样式需要实现文字固定宽度的两端对齐，当我跑在模拟器和安卓设备的时候一切正常，但是跑在苹果设备上就会失效。

解决方法：通过after伪类，使得当前行不是最后一行

```text
 <view class="box">
 文字居中
 </view>
```

```text
 .box{
        width: 200px;
        text-align: justify;
 }
 .box:after{
      content: '';
      width: 100%;
      display: inline-block;
 }
```

原网址: [访问](https://blog.csdn.net/weixin_44364001/article/details/111675633)

创建时间: 2021-09-16 15:26:35

目录: 剪藏

标签: 无  


