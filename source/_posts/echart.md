# vue 开发中 Echart 的使用

## 一、`vue` 下 `echart` 的使用

### 1. 安装:  `npm install echarts vue-echarts`

### 2. `main.js` 中引入

```js
import Vue from 'vue'
import ECharts from 'vue-echarts' // 在 webpack 环境下指向 components/ECharts.vue

// 手动引入 ECharts 各模块来减小打包体积
import 'echarts/lib/chart/bar'
import 'echarts/lib/component/tooltip'

// 如果需要配合 ECharts 扩展使用，只需要直接引入扩展包即可
// 以 ECharts-GL 为例：
// 需要安装依赖：npm install --save echarts-gl，并添加如下引用
import 'echarts-gl'

// 注册组件后即可使用
Vue.component('v-chart', ECharts)
```

### 3. `Vue-ECharts` 默认在 webpack 环境下会引入未编译的源码版本，如果你正在使用官方的 Vue CLI 来创建项目，可能会遇到默认配置把 node_modules 中的文件排除在 Babel 转译范围以外的问题。请按如下方法修改配置

```js
// vue.config.js
module.exports = {
  transpileDependencies: [
    'vue-echarts',
    'resize-detector'
  ]
}
```

### 4. 调用组件

```html
<template>
  <v-chart ref="echart" style="width:100%;height:100%" :options="options" />
</template>
<script>
import { mapGetters } from 'vuex'
const axisStyle = {
  axisLine: {
    lineStyle: {
      color: '#dcdee2'
    }
  },
  axisLabel: {
    color: '#657180',
    fontSize: '12px',
    interval: 0 // 使x轴横坐标全部显示
  },
  nameTextStyle: {
    color: '#657180'
  },
  splitLine: {
    show: true,
    lineStyle: {
      color: ['#dcdee2']
    }
  }
}
const drawLine = (data, title) => {
  // 图例配置
  return {
    color: '#4f7fff',
    tooltip: {
      trigger: 'axis',
      confine: true
    },
    grid: {
      top: 20,
      bottom: 20,
      left: 'left',
      right: 20,
      containLabel: true
    },
    xAxis: {
      type: 'category',
      boundaryGap: false,
      data: data.date || [],
      ...axisStyle
    },
    yAxis: {
      type: 'value',
      ...axisStyle
    },
    series: [
      {
        name: title,
        data: data.data,
        type: 'line',
        symbol: 'none',
        areaStyle: {
          color: {
            type: 'linear',
            x: 0,
            y: 0,
            x2: 0,
            y2: 1,
            colorStops: [
              {
                offset: 0,
                color: '#a3baf5' // 0% 处的颜色
              },
              {
                offset: 1,
                color: '#ebeffb' // 100% 处的颜色
              }
            ],
            global: false // 缺省为 false
          }
        }
      }
    ]
  }
}
export default {
  props: {
    title: {
      type: String
    },
    data: {
      type: Object,
      default: () => ({
        data: [],
        date: []
      })
    }
  },
  data() {
    return {
      options: {}
    }
  },
  watch: {
    data: {
      deep: true,
      handler(newData, oldData) {
        this.options = drawLine(newData, this.title)
      }
    }
  },
  created() {
    this.options = drawLine(this.data, this.title)
  },
  mounted() {
    window.addEventListener('resize', this.$refs.echart.resize())
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.$refs.echart.resize())
  }
}
</script>
```

## 二、图例随窗口自适应

 >通过监听 `window` 的 `resize`，来调用 `echart` 组件的 `resize` 方法，来实现图例自适应窗口

```js
mounted() {
  window.addEventListener('resize', this.$refs.echart.resize())
},
beforeDestroy() {
  window.removeEventListener('resize', this.$refs.echart.resize())
}
```

>当一个页面有多个图例时，同时监听`多个`图例自适应封装方法

```js
mounted() {
  window.addEventListener('resize', this.resizeTheChart)
},
beforeDestroy() {
  window.removeEventListener('resize', this.resizeTheChart)
},
methods: {
  resizeTheChart() {
    resize(['pieChart', 'lineChart1', 'lineChart2', 'linesChart'], this)
    function resize(refsArry, that) {
      if (refsArry && refsArry.length > 0) {
        refsArry.forEach(item => {
          if (that.$refs[item]) {
            that.$refs[item].resize()
          }
        })
      }
    }
  }
}
```

`ps: 记得在销毁组件时移除监听`

<!-- TODO 完善 -->