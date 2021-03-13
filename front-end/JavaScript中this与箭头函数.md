```javascript
let listener = () => {
  // 这里使用箭头函数，避免this指向问题
  if (this.chart) {
    this.chart.resize()
  }
}
EleResize.on(myChart, listener)
```
