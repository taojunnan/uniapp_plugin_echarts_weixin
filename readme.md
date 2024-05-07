# uniapp vue3 echarts 微信小程序 兼容Skyline

> uniapp 插件，由大名鼎鼎的 [echarts-for-weixin](https://github.com/ecomfe/echarts-for-weixin) 改造而来，适用于vue3 微信小程序（其他小程序没试过，可以自行尝试）

[github地址](https://github.com/taojunnan/uniapp_echarts_weixin)  
[插件地址](https://ext.dcloud.net.cn/plugin?id=15140)  

请仔细阅读文档哦

## 如何使用

1、引入组件（路径改成实际组件路径）

```js
import JnChart from '@/components/jn-chart/index.vue'
```

2、使用组件

```html
<JnChart :option="echartsOption" />
```

## 组件配置项

| 参数     | 类型     | 必须 | 说明                                                         |
| -------- | -------- | ---- | ------------------------------------------------------------ |
| option   | Object   | 是   | 我们熟悉的`echarts option`配置项                              |
| lazyLoad | Boolean  | 否   | 是否懒加载，默认`false`，如果`true`，第一次不会触发绘制，监听`option`变化后才触发绘制 |
| @init    | Function | 否   | `v1.0.1` 初始化完成后的回调，返回`echarts`实例对象           |

## 其他说明

1.**(重要)**组件中的`echarts.js`是作者方便测试从[echarts官网定制的echarts.js](https://echarts.apache.org/zh/builder.html)，只包含了基础功能，实际使用中请**替换成自己的`echarts.js`**

2.是否支持`vue2`?   

其实组件只是使用了`vue3`的语法，如果你愿意可以把`vue3`的语法替换成`vue2`的语法是完成可行的，后期会考虑增加兼容  

3.其他更多使用可以查看[使用示例](https://ext.dcloud.net.cn/plugin?id=15140)

4.有问题的话在插件页面评论或者github issue 我都能及时看到并一一回复  

## 更新日志
[地址](https://ext.dcloud.net.cn/plugin?id=15140&update_log)

## 完整例子

```vue
<template>
  <view class="box">
    <JnChart canvasId="line" :option="optionLine" lazyLoad @init="handleInit" />
  </view>
  
  <view class="box">
    <JnChart canvasId="bar" :option="optionBar" lazyLoad />
  </view>
  
  <view class="box">
    <JnChart canvasId="pie" :option="optionPie" />
  </view>
</template>

<script setup>
import { ref, onMounted } from 'vue'
// 请使用自己项目中本组件的真实路径
import JnChart from '@/components/jn-chart/index.vue'

// echarts的配置项
// 如果 option 是通过接口数据动态组成，建议给组件加上 lazyLoad 配置
const optionLine = ref({})
const optionBar = ref({})
// 静态的option，不用也不能加lazyLoad，否则不显示
const optionPie = {
  tooltip: {
    trigger: 'item'
  },
  legend: {
    top: '5%',
    left: 'center'
  },
  series: [
    {
      name: 'Access From',
      type: 'pie',
      radius: ['40%', '70%'],
      avoidLabelOverlap: false,
      label: {
        show: false,
        position: 'center'
      },
      emphasis: {
        label: {
          show: true,
          fontSize: 20,
          fontWeight: 'bold'
        }
      },
      labelLine: {
        show: false
      },
      data: [
        { value: 1048, name: 'Search Engine' },
        { value: 735, name: 'Direct' },
        { value: 580, name: 'Email' },
        { value: 484, name: 'Union Ads' },
        { value: 300, name: 'Video Ads' }
      ]
    }
  ]
}

onMounted(getData)

// 模拟从接口获取echarts渲染的数据
function getData() {
  setTimeout(() => {
    const data = {
      names: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
      values: [820, 932, 901, 934, 1290, 1330, 1320]
    }
    
    optionLine.value = getOption(data)
    optionBar.value = getOptionBar(data)
    
  }, 800)
}

function getOption(datasource) {
  const { values, names } = datasource
  
  const option = {
    // 如果图表显示的很小，记得加上grid
    grid: {
      top: '5%',
      left: '5%',
      right: '5%',
      bottom: 0,
      containLabel: true
    },
    tooltip: {
      show: true,
      trigger: 'axis',
      confine: true,
      formatter: (params) => {
        const { value, name } = params[0]
        
        return `自定义tooltip\n${value}\n${name}`
      }
    },
    xAxis: {
      type: 'category',
      data: names
    },
    yAxis: {
      type: 'value'
    },
    series: [
      {
        data: values,
        type: 'line',
        smooth: true
      }
    ]
  };
  
  return option
}

function getOptionBar(datasource) {
  const { values, names } = datasource 
  
  const option = {
    grid: {
      top: '5%',
      left: '5%',
      right: '5%',
      bottom: 0,
      containLabel: true
    },
    xAxis: {
      type: 'category',
      data: names
    },
    yAxis: {
      type: 'value'
    },
    series: [
      {
        data: values,
        type: 'bar',
        showBackground: true,
        backgroundStyle: {
          color: 'rgba(180, 180, 180, 0.2)'
        }
      }
    ]
  }
  
  return option
}

/**
 * echarts 初始化完成监听
 * @param {Object} chart echarts 实例对象
 */
function handleInit(chart) {
  console.debug('@line chart 实例对象 = ', chart)
}
</script>

<style lang="scss" scoped>
  .box {
    height: 460rpx;
    
    & + .box {
      margin-top: 30rpx;
    }
  }
</style>

```

