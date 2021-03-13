# Vue中form表单验证的参数

```javascript
rules: {
    name:[{
        required: true,
        message: '请输入用户名',
        trigger: 'blur'
        },{
        min: 2,
        max: 5,
        message: '长度在 2 到 5 个字符'
        },{
        pattern: /^[\u4E00-\u9FA5]+$/,
        message: '用户名只能为中文'
    }
        //{ pattern:/^[a-zA-Z]w{1,4}$/, message: '以字母开头，长度在2-5之间， 只能包含字符、数字和下划线'}
    ],
    password: [{
        required: true,
        message: '请输入密码',
        trigger: 'blur'
    }, {
        min: 6,
        max: 30,
        message: '长度在 6 到 30 个字符'
    }, {
        pattern: /^(\w){6,20}$/,
        message: '只能输入6-20个字母、数字、下划线'
    }],
    mobile:[{ 
        required: true,
        message: '请输入手机号码',
        trigger: 'blur'
    },
    {validator:function(rule,value,callback){
            if(/^1[34578]\d{9}$/.test(value) == false){
                callback(new Error("请输入正确的手机号"));
            }else{
                callback();
            }
        }, trigger: 'blur'}
    ],
    //   pattern: /^1[34578]\d{9}$/, message: '目前只支持中国大陆的手机号码' }
    peopleID:[{
        required: true,
        message: '请输入身份证ID',
        trigger: 'blur'
        },{
           pattern:/(^\d{15}$)|(^\d{18}$)|(^\d{17}(\d|X|x)$)/, message: '你的身份证格式不正确' 
        }
    ],
    carId:[
        {required: true, message: '请输入车牌号', trigger: 'blur'}, 
        {pattern:/(^[\u4E00-\u9FA5]{1}[A-Z0-9]{6}$)|(^[A-Z]{2}[A-Z0-9]{2}[A-Z0-9\u4E00-\u9FA5]{1}[A-Z0-9]{4}$)|(^[\u4E00-\u9FA5]{1}[A-Z0-9]{5}[挂学警军港澳]{1}$)|(^[A-Z]{2}[0-9]{5}$)|(^(08|38){1}[A-Z0-9]{4}[A-Z0-9挂学警军港澳]{1}$)/,
         message: '常规格式：晋B12345'},
    ],
},
```

