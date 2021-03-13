# 解决Vue中使用Echarts多图表页面自适应的问题

> 参考

1. 新建 `debounce.js` 文件

   ```javascript
   /**
   * @param {Function} func
   * @param {number} wait
   * @param {boolean} immediate
   * @return {*}
   */
   export function debounce(func, wait, immediate) {
   let timeout, args, context, timestamp, result

   const later = function() {
    // 据上一次触发时间间隔
    const last = +new Date() - timestamp

    // 上次被包装函数被调用时间间隔 last 小于设定时间间隔 wait
    if (last < wait && last > 0) {
      timeout = setTimeout(later, wait - last)
    } else {
      timeout = null
      // 如果设定为immediate===true，因为开始边界已经调用过了此处无需调用
      if (!immediate) {
        result = func.apply(context, args)
        if (!timeout) context = args = null
      }
    }
   }

   return function(...args) {
    context = this
    timestamp = +new Date()
    const callNow = immediate && !timeout
    // 如果延时不存在，重新设定延时
    if (!timeout) timeout = setTimeout(later, wait)
    if (callNow) {
      result = func.apply(context, args)
      context = args = null
    }

    return result
   }
   }
   ```

2. 在图表子组件中引入`debounce.js`

   ```javascript
   import { debounce } from '@/utils/debounce.js' // 填写实际路径
   ```

3. 在 `monted()` 方法中添加监听事件

   ```javascript
   this.__resizeHandler = debounce(() => {
   if (this.chart) {
    this.chart.resize()
   }
   }, 100)
   window.addEventListener('resize', this.__resizeHandler)
   ```

4. 在 `drawLine()` 方法中添加监听事件

   ```javascript
   this.__resizeHandler = debounce(() => {
   if (this.chart) {
    this.chart.resize()
   }
   }, 100)
   window.addEventListener('resize', this.__resizeHandler)
   ```

