# 改变对象的key



```javascript
// 改变对象的key
reduceKeys(source, keyMap) {
  let result = Object.keys(source).reduce((newData, key) => {
    let newKey = keyMap[key] || key
    newData[newKey] = source[key]
    return newData
  }, {})
  return result
}
```



