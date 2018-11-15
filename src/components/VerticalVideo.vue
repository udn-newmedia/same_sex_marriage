<template>
  <div class="vert-video-wrapper">
    <div class="vert-video-container">
        <transition name="fadeIn">
          <video v-show="show"
            playsinline muted autoplay
            class="vertical-video"
            id="vertical-video"
            ref="verticalVideoRef"
            :src="src"
            :poster="firstFrame"
            key="video">
          </video>
        </transition>
        <!-- <div class="video-first-frame-wrapper" key="image">
          <img v-show="firstFrameShow" :src="firstFrame" class="video-first-frame">
        </div> -->
    </div>
  </div>
</template>

<script>
import Utils from 'udn-newmedia-utils'

export default {
  name: 'VerticalVideo',
  props: {
    src: {
      type: String
    },
    firstFrame: {
      type: String
    },
    nowPlayIndex: {
      type: Number
    }
  },
  data () {
    return {
      show: true,
      firstFrameShow: false,
      vidoeOrder: ['bg', 'ding', 'ho', 'lee', 'miao', 'chu', 'wu', 'end']
    }
  },
  methods: {
    showToggle () {
      this.show = !this.show
    },
    gaVideoListTime () {
      if (!isNaN(this.currentMaxLevel)) {       
        window.ga("newmedia.send", {
          "hitType": "event",
          "eventCategory": "vertical_video",
          "eventAction": "watch",
          "eventLabel": "[" + Utils.detectPlatform() + "] [" + document.querySelector('title').innerHTML + "] [影片" + this.vidoeOrder[this.nowPlayIndex] + '觀看' + this.currentMaxLevel + "秒(以內)]"
        })
      }

      this.currentMaxLevel = 0
    },
    updataCurrentMaxTime (time) {
      if (time > 3 && time <= 5) {
        this.currentMaxLevel = 5
      } else if (time > 5 && time <= 10) {
        this.currentMaxLevel = 10
      } else if (time > 10 && time <= 15) {
        this.currentMaxLevel = 15
      } else if (time > 15 && time <= 20) {
        this.currentMaxLevel = 20
      } else if (time > 20 && time <= 25) {
        this.currentMaxLevel = 25
      } else if (time > 25 && time <= 30) {
        this.currentMaxLevel = 30
      } else if (time > 30 && time <= 35){
        this.currentMaxLevel = 35
      }
    },
    // showFirstFrame (flag) {
    //   this.firstFrameShow = flag
    // },
  },
  mounted () {
    let vm = this
    let currentMaxLevel = 5
    this.$refs.verticalVideoRef.ontimeupdate = function () {
      // $('.vertical-video')[0].muted = false

      // Handling GA.
      vm.updataCurrentMaxTime (this.currentTime)
      
      let temp = this.currentTime / this.duration      
      vm.$parent.$parent.calcProgress((temp * 100) - ((temp * 100) % 1) + 1)
      vm.$parent.playNext((temp * 100) - ((temp * 100) % 1) + 1)
    }
  }
}
</script>

<style lang="scss" scoped>
  .vert-video-wrapper {
    position: relative;
    width: 100%;
    height: 100%;
    .vert-video-container {
      position: relative;
      width: 100%;
      height: 100%;
      @media screen and (min-width: 768px) {
        display: flex;
        justify-content: center;
        align-items: center;
      }
    }
  }
  .vertical-video {
    position: relative;
    z-index: 10;

    @media screen and (min-width: 768px) {
      height: 100%;
    }
    @media screen and (max-width: 767px) {
      width: 100%;
    }
  }
  // .video-first-frame-wrapper {
  //   position: absolute;
  //   z-index: 20;
  //   top: 0;
  //   .video-first-frame {
  //     position: relative;
  //     width: 100%;
  //     opacity: 0;
  //     animation: fade-out .666s;
  //   }
  // }
  // @keyframes fade-out {
  //   0% {
  //     opacity: 0;
  //   }
  //   100% {
  //     opacity: 1;
  //   }
  // }
  // .fadeIn-enter-active, .fadeIn-leave-active {
  //   transition: opacity .666s;
  // }
  // .fadeIn-enter, .fadeIn-leave-to {
  //   opacity: 0.3;
  // }

  .fadeIn-enter-active, .fadeIn-leave-to {
    animation: fade-in .666s;
  }
  @keyframes fade-in {
    0% {
      opacity: 0.3;
    }
    100% {
      opacity: 1;
    }
  }
</style>
