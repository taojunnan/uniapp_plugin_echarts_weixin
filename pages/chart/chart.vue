<template>
  <view class="box">
    <JnChart :option="optionLine" lazyLoad @init="handleLineInit" />
  </view>
  
  <view class="box">
    <JnChart :option="optionBar" lazyLoad @init="handleBarInit" />
  </view>
  
  <view class="box">
    <JnChart :option="optionPie" />
  </view>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import JnChart from '@/components/jn-chart/index.vue'

// echarts的配置项
// 如果option是通过接口数据动态组成，建议给组件加上 lazyLoad 配置
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
    
    optionLine.value = getOptionLine(data)
    optionBar.value = getOptionBar(data)
    
  }, 800)
}

function getOptionLine(datasource) {
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
    tooltip: {
      trigger: 'item'
    },
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
function handleLineInit(chart) {
  console.debug('@line chart 实例对象 = ', chart)
  chart.on('click', () => {console.log('点击了line图表')})
}

/**
 * echarts 初始化完成监听
 * @param {Object} chart echarts 实例对象
 */
function handleBarInit(chart) {
  console.debug('@bar chart 实例对象 = ', chart)
  chart.on('click', () => {console.log('点击了bar图表')})
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
