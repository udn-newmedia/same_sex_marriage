<template>
  <div class="head-bar-wrapper">
    <div class="indicator-bar-wrapper">
      <div class="indicator-bar-bottom">
        <div class="indicator-bar-top" :style="{width: progress}"></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'VideoIndicator',
  props: {
    progress: {
      type: String
    }
  }
}
</script>

<style lang="scss" scoped>
  .head-bar-wrapper {
    position: fixed;
    z-index: 1000;
    top: 0;
    width: 100%;
    height: 50px;
  }

  .indicator-bar-wrapper {
    position: relative;
    height: 2px;
    width: 100%;
    // padding: 3px;

    .indicator-bar-bottom {
      width: 100%;
      height: 100%;
      // background-color: #888888;
    }
    .indicator-bar-top {
      height: 100%;
      background-color: #bf2923;
    }
  }
</style>
