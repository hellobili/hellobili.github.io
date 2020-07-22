# vue 开发中 Echart 的使用

## 一、图形随窗口自适应

 >通过监听 `window` 的 `resize`，来调用 `echart` 组件的 `resize` 方法，来实现图形自适应窗口

```js
mounted() {
  window.addEventListener('resize', this.$refs.echart.resize())
},
beforeDestroy() {
  window.removeEventListener('resize', this.$refs.echart.resize())
}
```
  
`ps: 记得在销毁组件时移除监听`
