> [https://blog.csdn.net/zjwxyy1994/article/details/101672543](https://blog.csdn.net/zjwxyy1994/article/details/101672543)

百度了好久，都无法解决，有的说有echart2的nodatashow属性，但是echart3删除了，有的说用showloading，但是来回切换的时候有加载动画体验不好，没办法只能翻墙了，皇天不负苦心人，终于找了好的解决方案，不懂说直接贴图
data是接受的数据，如果是数组就写成data.length===0,如果是对象那就写成Object.keys(data).length === 0
 title: {
show: Object.keys(data).length === 0,
extStyle: {
color: "grey",
  fontSize: 20
 },
  text: "暂无数据",
  left: "center",
  top: "center"
   },
只要这样设置title就行了，字体样式自己改！
