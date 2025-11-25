<template>
  <button @click="handleDispose">点击销毁</button>
  
  <view class="box">
    <JnChart ref="chartRef" :option="optionPie" @init="handleInit" />
  </view>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import JnChart from '@/components/jn-chart/index.vue'

const chartRef = ref(null)
let echartInstance = null

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

onUnmounted(() => {
  console.debug('销毁', echartInstance)
  
  echartInstance.dispose()
})

function handleInit(instance) {
  console.debug('实例 = ', instance)
  echartInstance = instance
}

function handleDispose() {
  console.log('handleDispose', echartInstance)
  echartInstance.dispose()
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
