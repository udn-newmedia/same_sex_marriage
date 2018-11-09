<template>
  <div class="head-bar-wrapper">
    <div class="indicator-bar-wrapper">
      <div class="indicator-bar-bottom">
        <div class="indicator-bar-top"></div>
      </div>
    </div>
    <div class="logo-wrapper">
      <div class="logo_box">
        <a href="https://ubrand.udn.com/ubrand/index" target="_blank">
          <i class="udn-icon udn-icon-logo"></i>
        </a>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HeadBar',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App'
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
    height: 8px;
    width: 100%;
    padding: 3px;

    .indicator-bar-bottom {
      width: 100%;
      height: 100%;
      opacity: 0.6;
      background-color: #e8e8e8;;
    }
    .indicator-bar-top {
      width: 100px;
      height: 100%;
      background-color: #ffffff;
    }
  }

  .logo-wrapper {
    position: relative;
    left: 0;
    height: 100%;
    width: 48px;
    font-size: 36px;

    .logo_box{
      position: relative;
      z-index: 100;
      display: flex;
      align-items: center;
      width: 100%;
      height: 100%;
      a {
        color: #ffffff;
        text-decoration: none;
      }
    }
  }
</style>
