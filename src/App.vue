<template>
  <div id="app">
    <head-bar></head-bar>
    <cover-video></cover-video>
    <video-player></video-player>
    <report-content></report-content>
  </div>
</template>

<script>
import CoverVideo from './components/CoverVideo'
import HeadBar from './components/HeadBar'
import ReportContent from './components/ReportContent'
import VideoPlayer from './components/VideoPlayer'

export default {
  name: 'App',
  components: {
    CoverVideo,
    HeadBar,
    ReportContent,
    VideoPlayer
  }
}
</script>

<style lang="scss">

</style>
