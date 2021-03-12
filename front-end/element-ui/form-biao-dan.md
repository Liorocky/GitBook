# Form表单

## 表单校验

| 参数 | 说明 | 类型 | 可选值 | 默认值 |
| :--- | :--- | :--- | :--- | :--- |
| rules | 表单验证规则 | object | — | — |

### 注意事项

> 当一个表单不是必填项，但如果填了就需要校验时，不能带有 required 参数，否则无论其取何值，都会导致剩下的校验失效

```text
name: [
    { required: false, message: '请输入活动名称', trigger: 'blur' },
    { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
]

// 此时，第3行的校验会失效
```



