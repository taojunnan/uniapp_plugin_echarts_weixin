<template>
  <snapshot mode="picture" id="target">
    <view style="height: 300px;">
      <JnChart :option="optionPie" />
    </view>
  </snapshot>
  
  <button @click="save">点击保存海报</button>
  
  <text>目前微信官方不支持截图canvas、map等原生组件，详见👇</text>
  <text>https://developers.weixin.qq.com/community/develop/doc/0004e80e098c583d11614dfcc66c00</text>
</template>

<script setup>
// import { onReady } from '@dcloudio/uni-app'
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

function save() {
  uni.createSelectorQuery()
    .select("#target")
    .node()
    .exec(res => {
      const node = res[0].node
      node.takeSnapshot({
        type: 'arraybuffer',
        format: 'png',
        success: (res) => {
          console.debug('@success ', res)
          
          const f = `${wx.env.USER_DATA_PATH}/hello.png`
          const fs = wx.getFileSystemManager();
          
          // 将海报数据写入本地文件
          fs.writeFileSync(f, res.data, 'binary')            
          // 把海报图片保存到本地
          wx.saveImageToPhotosAlbum({
            filePath: f
          })
          
          uni.showToast({
            icon: 'none',
            title: '保存成功，相册查看'
          })
        },
        fail(res) {
          console.debug('@fail', res)
          uni.showToast({
            icon: 'none',
            title: '保存失败'
          })
        }
      })
    })
}

</script>

<style lang="scss" scoped>

</style>
