在使用element-UI 的表单时，发生一个验证错误，例如已输入值但求验证纠错：
 
![](https://cdn.nlark.com/yuque/0/2021/png/1599502/1611646852580-0ddbc40b-40ab-4fde-97e9-eb6c79dc3314.png#align=left&display=inline&height=57&margin=%5Bobject%20Object%5D&originHeight=57&originWidth=337&size=0&status=done&style=none&width=337)
 
 
**代码如下所示：**
```javascript
<el-form :model="correction" :inline="true" 
          :rules="rules" ref="correctionForm"  class="demo-ruleForm" > 
           <el-form-item label="联系人邮箱" prop="correctorEmail"   
            label-suffix="sfdfsd">
                <el-input v-model="correction.correctorEmail" ></el-input>
            </el-form-item>
 </el-form>
rules: {
  correctorEmail: [
    { required: true, message: '请输入联系人邮箱地址', trigger: 'blur' },
  ],
},
```


**绑定都是没有错误的，然后我们利用自定义校验规则验证**
```javascript
this.$refs[formName].validate((valid) => {
  if (valid) {
    Http.fetch({
      method: "post",
      url: `${master}/exter/catalog/correction`,
      data:vm.correction
    }).then((res)=>{
      if (res.status == 200) {
        if (res.data.result) {
          vm.dialogFormVisible = false; //关闭对话框
          vm.$message({
            showClose: true,
            message: '纠错成功！',
            type: 'success'
          });
          vm.loadData();
          vm.disable = true;
        } else {
          vm.$message({
            type: "error",
            title: '纠错失败',
            message: res.message,
          });
        }
      }
      vm.disable = false;
    })
  } else {
    console.log('error submit!!');
    return false;
  }
});
```


##### 使用官网给出的这种形式，将value值打印出来，发现并不能获取到input中的值。
##### 显示undefined最终发现，prop对应的不单单是rules规则里面的验证项，同时应该对应着我们form-item下的v-model的值。
##### prop绑定的类要与el-form-item下的v-model的值相对应。我们将其做个修改便可以正常纠错了。
![](https://cdn.nlark.com/yuque/0/2021/png/1599502/1611646852589-1ecfd7b8-669c-4130-a376-e47262405ecd.png#align=left&display=inline&height=289&margin=%5Bobject%20Object%5D&originHeight=289&originWidth=401&size=0&status=done&style=none&width=401)
