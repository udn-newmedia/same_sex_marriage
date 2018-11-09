<template>
  <div class="video-player-wrapper">
    <verticle-video></verticle-video>
    <video-list>
      <div v-for="(item, index) in videoImageList"
        v-bind:item="item"
        v-bind:index="index"
        v-bind:key="index"
        class="video-image"
        :style="{
          backgroundImage: 'url(' + item.src + ')'
        }">
        <div>{{item.name}}</div>
        <div>{{item.title}}</div>
        <div>{{item.time}}</div>
      </div>
    </video-list>
  </div>
</template>

<script>
import VerticleVideo from './VerticleVideo'
import VideoList from './VideoList'

export default {
  name: 'VideoPlayer',
  components: {
    VerticleVideo,
    VideoList
  },
  data () {
    return {
      videoImageList: {
        'ding': {
          src: 'static/video_images/6.png',
          title: '國民黨台北市長候選人',
          name: '丁守中',
          time: '0:20'
        },
        'ho': {
          src: 'static/video_images/6.png',
          title: '候選人',
          name: '侯友宜',
          time: '0:20'
        },
        'lee': {
          src: 'static/video_images/6.png',
          title: '候選人',
          name: '李錫錕',
          time: '0:20'
        },
        'miao': {
          src: 'static/video_images/6.png',
          title: '候選人',
          name: '苗博雅',
          time: '0:20'
        },
        'chu': {
          src: 'static/video_images/6.png',
          title: '候選人',
          name: '邱威傑 (呱吉)',
          time: '0:20'
        },
        'wu': {
          src: 'static/video_images/6.png',
          title: '候選人',
          name: '吳沛憶',
          time: '0:20'
        }
      }
    }
  }
}
</script>

<style lang="scss" scoped>
  .video-player-wrapper {
    position: relative;
    width: 100%;
    height: 100%;
    background-color: #666666;
  }
  .video-image {
    width: 15%;
    height: 100%;
    margin: 0 5px;
    border-radius: 25px;
    float: left;
    background-size: cover;
    background-position: top center;
  }
</style>
