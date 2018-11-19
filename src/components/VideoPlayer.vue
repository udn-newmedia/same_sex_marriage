<template>
  <div class="video-player-wrapper">
    <vertical-video
      :src="currentPlayVideo"
      :firstFrame="currentFirstFrame"
      :nowPlayIndex="nowPlayIndex">
    </vertical-video>
    <div v-show="nowPlayIndex === 0" class="first-share">
      <share href="https://udn.com/upf/newmedia/2018_data/same_sex_marriage_referendum/index.html"></share>
    </div>
    <div v-if="nowPlayIndex !== 0"
      class="move-arrow-left-wrapper"
      @click="moveVideo('backward')">
      <div  class="move-arrow-left"></div>
    </div>
    <div v-if="nowPlayIndex !== vidoeOrder.length - 1"
      class="move-arrow-right-wrapper"
      @click="moveVideo('forward')">
      <div class="move-arrow-right"></div>
    </div>
    <video-list ref="videoListRef">
      <div v-for="(item, index) in videoImageList"
        v-bind:item="item"
        v-bind:index="index"
        v-bind:key="index"
        class="video-image"
        :style="{
          backgroundImage: 'url(' + item.src + ')',
          borderStyle: item.style
        }"
        @click="selectVideo(index)">
        <div class="video-mask" :style="{opacity: item.opacity}"></div>
        <div class="video-label-wrapper">
          <div class="video-label-name">{{item.name}}</div>
          <div class="video-label-title">{{item.title}}</div>
          <div class="video-label-time">{{item.time}}</div>
        </div>
      </div>
    </video-list>
    <div class="watch-report-wrapper" v-show="showWatchReport">
      <transition name="fade">
        <div v-show="showWatchReport" class="watch-report" @click="watchReport"  key="report">看專題報導</div>
      </transition>
      <transition name="fade">
        <share v-show="showWatchReport" href="https://udn.com/upf/newmedia/2018_data/same_sex_marriage_referendum/index.html" key="share"></share>
      </transition>
    </div>
  </div>
</template>

<script>
import Utils from 'udn-newmedia-utils'
import VerticalVideo from './VerticalVideo'
import VideoList from './VideoList'
import Share from './Share'

export default {
  name: 'VideoPlayer',
  components: {
    VerticalVideo,
    VideoList,
    Share
  },
  data () {
    return {
      vidoeOrder: ['bg', 'ding', 'ho', 'lee', 'miao', 'chu', 'wu', 'end'],
      nowPlayIndex: 0,
      videoImageList: {
        'bg': {
          src: 'static/video_images/p_bg_2.jpg',
          title: '同婚修民法或立專法？候選人怎麼說',
          name: '　',
          time: '0:10',
          style: 'solid',
          opacity: 0
        },
        'ding': {
          src: 'static/video_images/p_ding.jpg',
          title: '國民黨台北市長候選人',
          name: '丁守中',
          time: '0:25',
          style: 'none',
          opacity: 0.5
        },
        'ho': {
          src: 'static/video_images/p_ho.jpg',
          title: '國民黨新北市長候選人',
          name: '侯友宜',
          time: '0:25',
          style: 'none',
          opacity: 0.5
        },
        'lee': {
          src: 'static/video_images/p_lee.jpg',
          title: '無黨籍台北市長候選人',
          name: '李錫錕',
          time: '0:32',
          style: 'none',
          opacity: 0.5
        },
        'miao': {
          src: 'static/video_images/p_miao.jpg',
          title: '社民黨台北市議員候選人',
          name: '苗博雅',
          time: '0:32',
          style: 'none',
          opacity: 0.5
        },
        'chu': {
          src: 'static/video_images/p_chu.jpg',
          title: '無黨籍台北市議員候選人',
          name: '邱威傑 (呱吉)',
          time: '0:23',
          style: 'none',
          opacity: 0.5
        },
        'wu': {
          src: 'static/video_images/p_wu.jpg',
          title: '民進黨台北市議員候選人',
          name: '吳沛憶',
          time: '0:30',
          style: 'none',
          opacity: 0.5
        },
        'end': {
          src: 'static/video_images/p_end_2.jpg',
          title: '看專題報導',
          name: '　',
          time: '0:10',
          style: 'none',
          opacity: 0.5
        }
      },
      videoList: {
        'bg': {
          src: 'static/videos/v_bg_4.mp4',
          firstFrame: 'static/first_frames/v_bg_3.jpg'
        },
        'ding': {
          src: 'static/videos/v_ding_3.mp4',
          firstFrame: 'static/first_frames/v_ding.jpg'
        },
        'ho': {
          src: 'static/videos/v_ho_3.mp4',
          firstFrame: 'static/first_frames/v_ho.jpg'
        },
        'lee': {
          src: 'static/videos/v_lee_3.mp4',
          firstFrame: 'static/first_frames/v_lee.jpg'
        },
        'miao': {
          src: 'static/videos/v_miao_3.mp4',
          firstFrame: 'static/first_frames/v_miao.jpg'
        },
        'chu': {
          src: 'static/videos/v_chu_3.mp4',
          firstFrame: 'static/first_frames/v_chu.jpg'
        },
        'wu': {
          src: 'static/videos/v_wu_3.mp4',
          firstFrame: 'static/first_frames/v_wu.jpg'
        },
        'end': {
          src: 'static/videos/v_end_3.mp4',
          firstFrame: 'static/first_frames/v_end_2.jpg'
        }
      },
      currentPlayVideo: 'static/videos/v_bg_4.mp4',
      currentFirstFrame: 'static/first_frames/v_bg_3.jpg',
      showWatchReport: false
    }
  },
  methods: {
    handleHeadBarColor () {
      if (this.nowPlayIndex === 0 || this.nowPlayIndex === 7) {
        this.$parent.$children[1].chooseLogoColor(false)
        this.$parent.$children[1].chooseMuteOffColor(false)
        this.$parent.$children[1].chooseMuteOnColor(false)
      } else {
        this.$parent.$children[1].chooseLogoColor(true)
        this.$parent.$children[1].chooseMuteOffColor(true)
        this.$parent.$children[1].chooseMuteOnColor(true) 
      }
    },
    selectVideo (index) {
      this.currentFirstFrame = this.videoList[index].firstFrame
      this.currentPlayVideo = this.videoList[index].src
      this.nowPlayIndex = this.vidoeOrder.indexOf(index)
      this.handleVideoSelection(index)
      this.gaVideoListClick(index)
      this.handleHeadBarColor()
    },
    moveVideo (control) {
      if (control === 'backward') {
        if (this.nowPlayIndex !== 0) {
          this.nowPlayIndex--
          this.currentPlayVideo = this.videoList[this.vidoeOrder[this.nowPlayIndex]].src
          this.currentFirstFrame = this.videoList[this.vidoeOrder[this.nowPlayIndex]].firstFrame
        }
      } else {
        if (this.nowPlayIndex !== this.vidoeOrder.length - 1) {
          this.nowPlayIndex++
          this.currentPlayVideo = this.videoList[this.vidoeOrder[this.nowPlayIndex]].src
          this.currentFirstFrame = this.videoList[this.vidoeOrder[this.nowPlayIndex]].firstFrame
        }
      }
      this.handleHeadBarColor()
      this.handleVideoSelection(this.vidoeOrder[this.nowPlayIndex])
    },
    handleVideoSelection (index) {
      for (let i in this.videoImageList) {
        this.videoImageList[i].style = 'none'
        this.videoImageList[i].opacity = '0.5'
      }
      this.videoImageList[index].style = 'solid'
      this.videoImageList[index].opacity = '0'
      this.$refs.videoListRef.movingToSelectedVideo(this.nowPlayIndex)
      
      this.$children[0].showToggle()
      this.$children[0].gaVideoListTime()
      this.$parent.$children[1].setMute()

      // If it is last video, show the watch report button.
      let vm = this
      if (vm.nowPlayIndex === vm.vidoeOrder.length - 1) {
        setTimeout(function () {
          vm.showWatchReport = true
        }, 999)
      } else {
        vm.showWatchReport = false
      }

      setTimeout(function () {
        vm.$children[0].showToggle()
      }, 666)
    },
    playNext (progress) {
      if (progress > 99) {
        if (this.nowPlayIndex < this.vidoeOrder.length - 1) {
          this.nowPlayIndex++
          this.currentPlayVideo = this.videoList[this.vidoeOrder[this.nowPlayIndex]].src
          this.currentFirstFrame = this.videoList[this.vidoeOrder[this.nowPlayIndex]].firstFrame
          this.handleVideoSelection(this.vidoeOrder[this.nowPlayIndex])
          this.handleHeadBarColor()
        }
      }
    },
    watchReport () {
      this.$parent.watchReport()
      window.ga("newmedia.send", {
        "hitType": "event",
        "eventCategory": "vertical_video",
        "eventAction": "click",
        "eventLabel": "[" + Utils.detectPlatform() + "] [" + document.querySelector('title').innerHTML + "] [看更多報導按鈕]"
      })
    },
    gaVideoListClick (name) {
      window.ga("newmedia.send", {
        "hitType": "event",
        "eventCategory": "vertical_video",
        "eventAction": "click",
        "eventLabel": "[" + Utils.detectPlatform() + "] [" + document.querySelector('title').innerHTML + "] [影音點擊: " + name + "]"
      })
    }
  }
}
</script>

<style lang="scss" scoped>
  .video-player-wrapper {
    position: relative;
    width: 100%;
    height: 100%;
    background-color: #000000;
  }
  .video-image {
    width: 11%;
    height: 100%;
    margin: 0 3px;
    border-color: #ffffff;
    border-width: 2px;
    border-radius: 7px;
    float: left;
    background-size: cover;
    background-position: top center;
    color: #ffffff;
    cursor: pointer;
    
    @media screen and (min-width: 769px) {
      width: 11%;
      margin: 0 0.7%;
    }
    .video-mask {
      position: relative;
      width: 100%;
      height: 100%;
      border-radius: 7px;
      background-color: #000000;
    }
    .video-label-wrapper {
      position: absolute;
      bottom: 5px;
      width: 11%;
      padding: 0 15px;
      font-size: 12px;
    
      @media screen and (min-width: 769px) {
        width: 10%;
        margin: 0 0.7%;
      }

      .video-label-name {
        position: relative;
        text-align: start;
      }
      .video-label-title {
        position: relative;
        text-align: start;
      }
      .video-label-time {
        position: relative;
        text-align: end;
        margin-top: 5px;
      }
    }
  }
  .move-arrow-left-wrapper {
    position: fixed;
    z-index: 50;
    top: 0;
    left: 0;
    width: 35px;
    height: 100%;
    cursor: pointer;
    .move-arrow-left {
      position: relative;
      top: 50%;
      left: 10px;
      height: 25px;
      width: 25px;
      border-radius: 12.5px;
      background-color: rgba($color: #000000, $alpha: 0.5);
      cursor: pointer;
      &:before, &:after {
        content: '';
        height: 8%;
        width: 50%;
        position: absolute;
        background-color: #ffffff;
        top: 57%;
        transform-origin: center;
        transition: all .3s ease-in-out;
      }
      &:before {
        transform: rotate(40deg) scale(0.8);
        left: 20%;
      }
      &:after {
        transform: rotate(-40deg) scale(0.8);
        left: 20%;
        top: 33%;
      }
    }
  }
  .move-arrow-right-wrapper {
    position: fixed;
    z-index: 50;
    top: 0;
    right: 0;
    width: 35px;
    height: 100%;
    cursor: pointer;
    .move-arrow-right {
      position: relative;
      top: 50%;
      height: 25px;
      width: 25px;
      border-radius: 12.5px;
      background-color: rgba($color: #000000, $alpha: 0.5);
      cursor: pointer;
      &:before, &:after {
        content: '';
        height: 8%;
        width: 48%;
        position: absolute;
        background-color: #ffffff;
        top: 57%;
        transform-origin: center;
        transition: all .3s ease-in-out;
      }
      &:before {
        transform: rotate(140deg) scale(0.8);
        right: 20%;
      }
      &:after {
        transform: rotate(-140deg) scale(0.8);
        right: 20%;
        top: 33%;
      }
    }
  }
  .first-share {
    position: fixed;
    z-index: 60;
    width: 100%;
    top: 10%;
    left: 7%;
    @media screen and (min-width: 768px) {
      left: calc(50% - (45vh * 0.5625));
    }
  }
  .watch-report-wrapper {
    position: fixed;
    z-index: 60;
    top: 30%;
    left: calc(50% - 100px);
    width: 200px;
    height: 150px;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;

    .watch-report {
      width: 75%;
      height: 30%;
      margin: 20px;
      display: flex;
      justify-content: center;
      align-items: center;
      border-style: solid;
      border-color: #ffffff;
      border-width: 1px;
      border-radius: 5px;
      color: #ffffff;
      font-size: 20px;
      cursor: pointer;
    }
  }

  .fade-enter-active, .fade-leave-active {
    transition: opacity .666s;
  }
  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
</style>
