<template>
  <button @click="gotoDemo('chart')">基础 demo</button>
  <button @click="gotoDemo('skyline')">skyline demo</button>
  <button @click="gotoDemo('snapshot')">skyline snapshot demo</button>
  <button @click="gotoDemo('barrace')">动态图表 demo</button>
  <button @click="gotoDemo('saveImg')">保存图片 demo</button>
</template>

<script setup>
  function gotoDemo(key) {
    uni.navigateTo({
      url: `/pages/${key}/${key}`
    })
  }
</script>
