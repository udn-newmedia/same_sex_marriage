<template>
  <div class="head-bar-wrapper">
    <div class="logo-wrapper">
      <div class="logo_box">
        <a href="https://ubrand.udn.com/ubrand/index" target="_blank">
          <i class="udn-icon udn-icon-logo"></i>
        </a>
      </div>
      <div class="muted-wrapper" @click="videoMuted">
        <div v-show="mutedFlag" class="muted-image-wrapper"><img class="muted-image" src="static/off.svg"></div>
        <div v-show="!mutedFlag" class="muted-image-wrapper"><img class="muted-image" src="static/on.svg"></div>
      </div>
    </div>
  </div>
</template>

<script>
import Utils from 'udn-newmedia-utils'

export default {
  name: 'VideoHeadBar',
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
    },
    setMute () {
      this.mutedFlag = true
    }
  }
}
</script>

<style lang="scss" scoped>
  .head-bar-wrapper {
    position: absolute;
    z-index: 1000;
    top: 0;
    width: 100%;
    height: 50px;
    display: flex;
    justify-content: center;
    align-items: cneter;
  }

  .logo-wrapper {
    position: relative;
    display: flex;
    height: 100%;
    width: 100%;
    @media screen and (min-width: 768px) {
      width: calc(100vh * 0.5625);
    }
    .logo_box{
      position: relative;
      z-index: 100;
      display: flex;
      align-items: center;
      width: 48px;
      height: 100%;
      font-size: 36px;
      a {
        color: #313131;
        opacity: 0.6;
        text-decoration: none;
      }
    }

    .muted-wrapper {
      position: relative;
      z-index: 100;
      width: 48px;
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
