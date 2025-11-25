<template>
  <canvas
    type="2d"
    :canvas-id="canvasId"
    class="jn-canvas"
    @touchstart="touchStart"
    @touchmove="touchMove"
    @touchend="touchEnd">
  </canvas>
</template>

<script setup>
import { onReady } from '@dcloudio/uni-app'
import { getCurrentInstance, watch } from 'vue'
// import WxCanvas from '../../static/jn-chart/wx-canvas.js';
// import * as echarts from '/static/jn-chart/echarts.js';
const WxCanvas = require('../../static/jn-chart/wx-canvas.js').default
const echarts = require('../../static/jn-chart/echarts.js')

const props = defineProps({
  // echarts option 配置项
  option: {
    required: true,
    type: Object,
    default() {
      return {}
    }
  },
  // 是否懒加载，如果true，第一次不会触发echarts init 绘制，props.option变化后才触发
  lazyLoad: {
    type: Boolean,
    default: false
  },
  // canvas id
  canvasId: {
    required: false,
    type: String,
    default: 'jn-canvas'
  }
})

const emits = defineEmits(['init'])

// 当前echarts实例对象
let chart = null
// vue 实例对象
const instance = getCurrentInstance()
// canvas 的位置信息 { top, bottom, left, right }
let canvasRect = {}

onReady(() => {
  // Disable prograssive because drawImage doesn't support DOM as parameter
  // See https://developers.weixin.qq.com/miniprogram/dev/api/canvas/CanvasContext.drawImage.html
  echarts.registerPreprocessor(option => {
    if (option && option.series) {
      if (option.series.length > 0) {
        option.series.forEach(series => {
          series.progressive = 0;
        });
      } else if (typeof option.series === 'object') {
        option.series.progressive = 0;
      }
    }
  });

  // 开启懒加载第一次不初始化
  if (!props.lazyLoad) {
    init();
  }
})

// 监听数据源变化，重新触发初始化方法刷新chart
watch(() => props.option, init, { deep: true })

function init() {
  const proxy = instance.proxy

  // version >= 2.9.0; 兼容skyline渲染
  const query = proxy.createSelectorQuery()
  query
    .select('.jn-canvas')
    .fields({
      node: true,
      size: true
    })
    .exec(res => {
      const { node, width, height } = res[0]
      
      const canvasNode = node
      const canvasDpr = uni.getWindowInfo().pixelRatio
      const canvasWidth = width
      const canvasHeight = height

      const ctx = canvasNode.getContext('2d')

      const canvas = new WxCanvas(ctx, canvasNode)
      if (echarts.setPlatformAPI) {
        echarts.setPlatformAPI({
          createCanvas: () => canvas,
          loadImage: (src, onload, onerror) => {
            if (canvasNode.createImage) {
              const image = canvasNode.createImage();
              image.onload = onload;
              image.onerror = onerror;
              image.src = src;
              return image;
            }
            console.error('加载图片依赖 `Canvas.createImage()` API，要求小程序基础库版本在 2.7.0 及以上。');
          }
        })
      } else {
        echarts.setCanvasCreator(() => canvas)
      }

      chart = initChart(canvas, canvasWidth, canvasHeight, canvasDpr, props.option)
      
      // 初始化后向上抛出echarts实例对象
      emits('init', chart)
    })
}

// 初始化echarts
function initChart(canvas, width, height, dpr, option) {
  const chart = echarts.init(canvas, null, {
    width: width,
    height: height,
    devicePixelRatio: dpr
  })
  canvas.setChart(chart)

  chart.setOption(option)
  
  return chart
}

// 获取canvas元素的位置信息
function getCanvasRect() {
  const query = instance.proxy.createSelectorQuery()
  
  return new Promise(resolve => {
    query.select('.jn-canvas').boundingClientRect(rect => {
      // console.debug('@rect = ', rect)
      canvasRect = { left: rect.left, top: rect.top }
      
      resolve()
    }).exec()
  })
}

async function touchStart(e) {
  await getCanvasRect()
  // console.debug('@touchstart', e)
  
  if (chart && e.touches.length > 0) {
    const touch = e.touches[0];
    const handler = chart.getZr().handler;
    
    const args = {
      zrX: touch.x || touch.pageX - canvasRect.left,
      zrY: touch.y || touch.pageY - canvasRect.top,
      preventDefault: () => {},
      stopImmediatePropagation: () => {},
      stopPropagation: () => {}
    }
    
    handler.dispatch('mousedown', args);
    handler.dispatch('mousemove', args);
    handler.processGesture(wrapTouch(e, args), 'start');
  }
}

function touchMove(e) {
  // console.debug('@touchMove', e)
  if (chart && e.touches.length > 0) {
    const touch = e.touches[0];
    const handler = chart.getZr().handler;
    const args = {
      zrX: touch.x || touch.pageX - canvasRect.left,
      zrY: touch.y || touch.pageY - canvasRect.top,
      preventDefault: () => {},
      stopImmediatePropagation: () => {},
      stopPropagation: () => {}
    }
    
    handler.dispatch('mousemove', args);
    handler.processGesture(wrapTouch(e, args), 'change');
  }
}

function touchEnd(e) {
  // console.debug('@touchend', e)
  if (chart) {
    const touch = e.changedTouches ? e.changedTouches[0] : {};
    const handler = chart.getZr().handler;
    const args = {
      zrX: touch.x || touch.pageX - canvasRect.left,
      zrY: touch.y || touch.pageY - canvasRect.top,
      preventDefault: () => {},
      stopImmediatePropagation: () => {},
      stopPropagation: () => {}
    }
    
    handler.dispatch('mouseup', args);
    handler.dispatch('click', args);
    handler.processGesture(wrapTouch(e, args), 'end');
  }
}

function wrapTouch(event, args) {
  event.zrX = args.zrX
  event.zrY = args.zrY
  
  for (let i = 0; i < event.touches.length; ++i) {
    const touch = event.touches[i];
    
    touch.offsetX = args.zrX;
    touch.offsetY = args.zrY;
  }
  
  return event;
}

/**
 * 保存至图片
 * @param {Object} opt 同uni.canvasToTempFilePath(opt)的第一个参数
 * @see https://uniapp.dcloud.net.cn/api/canvas/canvasToTempFilePath.html#canvastotempfilepath
 */
function canvasToTempFilePath(opt) {
  const proxy = instance.proxy
  const query = proxy.createSelectorQuery()
  
  query
    .select('.jn-canvas')
    .fields({ node: true, size: true })
    .exec(res => {
      const { node } = res[0]
      
      opt.canvas = node
      uni.canvasToTempFilePath(opt, instance)
    })
}

defineExpose({
  canvasToTempFilePath
})
</script>

<style scoped>
  .jn-canvas {
    width: 100%;
    height: 100%;
  }
</style>