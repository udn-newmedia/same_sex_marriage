<template>
  <div id="app">
    <indicator v-if="reportFlag"></indicator>
    <head-bar v-if="reportFlag">
      <a slot="logo" href="https://vision.udn.com/"></a>
    </head-bar>
    <video-head-bar v-if="!reportFlag" :progress="videoPlayProgress"></video-head-bar>
    <video-player v-if="!reportFlag"></video-player>
    <transition name="fade">
      <report-content v-if="reportFlag"></report-content>
    </transition>
  </div>
</template>

<script>
import HeadBar from './components/HeadBar'
import Indicator from './components/Indicator'
import ReportContent from './components/ReportContent'
import VideoHeadBar from './components/VideoHeadBar'
import VideoPlayer from './components/VideoPlayer'

export default {
  name: 'App',
  components: {
    HeadBar,
    Indicator,
    ReportContent,
    VideoHeadBar,
    VideoPlayer
  },
  data () {
    return {
      videoPlayProgress: '',
      reportFlag: false
    }
  },
  methods: {
    watchReport () {
      this.reportFlag = !this.reportFlag
    },
    calcProgress (progress) {
      this.videoPlayProgress = progress + '%'
    }
  }
}
</script>

<style lang="scss">
  .fade-enter-active, .fade-leave-active {
    transition: opacity 1.5s;
  }
  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
</style>
