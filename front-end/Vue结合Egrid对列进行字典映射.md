借鉴这篇文章，但是此文章使用的是原生的el-table，而使用Egrid之后，就不能直接替换，需要使用Egrid的方式。
# 原生el-table
实现代码有两种：
一是类似布尔类型，在三元运算符中加入判断，以及嵌套实现。


```
<el-table-column label="类型" prop="type" align="center" width="80px">
        <template slot-scope="scope">
          {{ scope.row.type == 1 ? '表单' : scope.row.type == 2 ?'列表': '' }} 
        </template>
      </el-table-column>
```
第二种方法：


```
<el-table-column label="类型" prop="type" align="center" width="80px">
        <template slot-scope="scope">
          {{ map[scope.row.type] }}
        </template>
</el-table-column>
```
在data中定义一个map存储根据map的值来对应转换的数据


```
data() {
    return {
        map: {
          1: '表单',
          2: '列表',
          3: '列表和打印'
      }
    }
  },
```


# Egrid实现方式
```javascript
/***
* 字典表组件
*/
columnsSchema: {
  'type':{
    propsHandler ({ col, row }) {
      return { type: row[col.prop] }
    },
      component: Vue.extend({
        template: "<p>{{ type === 'mobile' ? '手机' : '固话' }}</p>",
        props: ['type'],
      })
  },
},
```
现在需要在template 中必须添加一个标签，比如<p>标签，暂时还没找到去除的方式
