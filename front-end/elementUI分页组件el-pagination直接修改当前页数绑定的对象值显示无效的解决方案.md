

分页组件原始代码:
```html
<el-pagination
    @size-change="handleSizeChange"
    @current-change="handleCurrentChange"
    :current-page="nowPage"
    :page-sizes="[10, 20, 30, 50]"
    :page-size="pageSize"
    layout="total, sizes, prev, pager, next, jumper"
    :total="sum">
</el-pagination>
```
直接修改 nowPage = 2 对象无法产生点击下一页的效果:
比如:
![](https://cdn.nlark.com/yuque/0/2021/png/1599502/1612147447178-d74e70d6-cca5-4766-8d66-a184757145cc.png#align=left&display=inline&height=46&margin=%5Bobject%20Object%5D&originHeight=46&originWidth=430&size=0&status=done&style=stroke&width=430)
不能自动变为:
![](https://cdn.nlark.com/yuque/0/2021/png/1599502/1612147447254-14c196ca-bad6-410a-bf0b-9aa1e38872f5.png#align=left&display=inline&height=64&margin=%5Bobject%20Object%5D&originHeight=64&originWidth=441&size=0&status=done&style=stroke&width=441)
解决办法是添加唯一key强制组件刷新:
```html
<el-pagination
    :key="elementui_page_component_key"
    @size-change="handleSizeChange"
    @current-change="handleCurrentChange"
    :current-page="nowPage"
    :page-sizes="[10, 20, 30, 50]"
    :page-size="pageSize"
    layout="total, sizes, prev, pager, next, jumper"
    :total="sum">
</el-pagination>
```
```javascript
methods: {
    autoIncrasePageComKey: function () {
        this.elementui_page_component_key ++
    }
}
```
每次在修改 nowPage 对象前都先调用autoIncrasePageComKey()方法即可做到手动修改页数的效果
