<template>

  <text>等待 3s 后开始</text>
  
  <view class="box">
    <JnChart :option="option" lazyLoad @init="handleInit" />
  </view>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import JnChart from '@/components/jn-chart/index.vue'

const data = []

const option = ref({})

onMounted(initData)

function initData() {
  for (let i = 0; i < 5; ++i) {
    data.push(Math.round(Math.random() * 200));
  }
  
  option.value = {
    xAxis: {
      max: 'dataMax'
    },
    yAxis: {
      type: 'category',
      data: ['A', 'B', 'C', 'D', 'E'],
      inverse: true,
      animationDuration: 300,
      animationDurationUpdate: 300,
      max: 2
    },
    series: [
      {
        realtimeSort: true,
        name: 'X',
        type: 'bar',
        data: data,
        label: {
          show: true,
          position: 'right',
          valueAnimation: true
        }
      }
    ],
    legend: {
      show: true
    },
    animationDuration: 0,
    animationDurationUpdate: 3000,
    animationEasing: 'linear',
    animationEasingUpdate: 'linear'
  }
}

function run(myChart) {
  for (var i = 0; i < data.length; ++i) {
    if (Math.random() > 0.9) {
      data[i] += Math.round(Math.random() * 2000);
    } else {
      data[i] += Math.round(Math.random() * 200);
    }
  }
  myChart.setOption({
    series: [
      {
        type: 'bar',
        data
      }
    ]
  });
}

/**
 * echarts 初始化完成监听
 * @param {Object} chart echarts 实例对象
 */
function handleInit(chart) {
  console.debug('@实例对象 = ', chart)
  
  setInterval(() => {
    run(chart);
  }, 3000);
}
</script>

<style lang="scss" scoped>
.box {
  height: 500rpx;
}
</style>
