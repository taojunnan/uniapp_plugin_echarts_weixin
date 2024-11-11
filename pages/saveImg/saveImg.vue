<template>
  <button @click="saveImg">保存图片</button>
  
  <view class="box">
    <JnChart ref="chartRef" :option="optionPie" />
  </view>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import JnChart from '@/components/jn-chart/index.vue'

const chartRef = ref(null)
const imgSrc = ref('')

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


function saveImg() {
  chartRef.value.canvasToTempFilePath({
    success: res => {
      console.debug('@save img success res = ', res)
      const imgSrc = res.tempFilePath
      
      // 保存至相册
      uni.saveImageToPhotosAlbum({
        filePath: imgSrc,
        success: (res) => {
          console.debug('@保存相册成功', res)
          uni.showToast({
            title: '保存成功'
          })
        },
        fail: (e) => {
          console.debug('@保存相册失败', e)
        }
      })
    },
    fail: e => {
      console.debug('@save img error', e)
      uni.showToast({
        icon: 'none',
        title: '保存失败'
      })
    }
  })
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
