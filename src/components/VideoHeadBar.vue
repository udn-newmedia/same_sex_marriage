<template>
  <div class="head-bar-wrapper">
    <div class="indicator-bar-wrapper">
      <div class="indicator-bar-bottom">
        <div class="indicator-bar-top" :style="{width: progress}"></div>
      </div>
    </div>
    <div class="logo-wrapper">
      <div class="logo_box">
        <a href="https://ubrand.udn.com/ubrand/index" target="_blank">
          <i class="udn-icon udn-icon-logo"></i>
        </a>
      </div>
      <div class="muted-wrapper" @click="videoMuted">
        <div v-if="mutedFlag" class="muted-image-wrapper"><img class="muted-image" src="static/off.svg"></div>
        <div v-if="!mutedFlag" class="muted-image-wrapper"><img class="muted-image" src="static/on.svg"></div>
      </div>
    </div>
  </div>
</template>

<script>
import Utils from 'udn-newmedia-utils'

export default {
  name: 'VideoHeadBar',
  props: {
    progress: {
      type: String
    }
  },
  data () {
    return {
      mutedFlag: true
    }
  },
  methods: {
    videoMuted () {
      this.mutedFlag = !this.mutedFlag
      $('.vertical-video')[0].muted = this.mutedFlag

      window.ga("newmedia.send", {
        "hitType": "event",
        "eventCategory": "vertical_video",
        "eventAction": "click",
        "eventLabel": "[" + Utils.detectPlatform() + "] [" + document.querySelector('title').innerHTML + "] [打開聲音按鈕]"
      })
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

  .logo-wrapper {
    position: relative;
    left: 0;
    display: flex;
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
        color: #313131;
        opacity: 0.6;
        text-decoration: none;
      }
    }

    .muted-wrapper {
      position: relative;
      z-index: 100;
      width: 100%;
      height: 100%;
      cursor: pointer;

      .muted-image-wrapper {
        position: relative;
        width: 48px;
        height: 48px;
        display: flex;
        justify-content: center;
        align-items: center;

        .muted-image {
          width: 90%;
          height: 85%;
        }
      }
    }
  }
</style>
