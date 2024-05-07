<template>
  <scroll-view type="custom" scroll-y="true" style="height: 100vh;">
    <view class="content-item" v-for="i in 15" :key="i" style="margin: 20rpx;">
        占位文字{{i}}
    </view>
    <div style="height: 300px;">
      <JnChart :option="optionPie" />
    </div>
  </scroll-view>
</template>

<script setup>
import JnChart from '@/components/jn-chart/index.vue'

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

</script>

<style lang="scss" scoped>

</style>
