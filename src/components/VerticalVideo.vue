<template>
  <div class="vert-video-wrapper">
    <div class="vert-video-container">
        <video v-show="show"
          playsinline muted autoplay
          class="vertical-video"
          id="vertical-video"
          :src="src"
          ref="verticalVideoRef">
        </video>
      <div class="video-first-frame-wrapper">
        <transition name="fadeIn">
          <img v-show="firstFrameShow" :src="firstFrame" class="video-first-frame">
        </transition>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'VerticalVideo',
  props: {
    src: {
      type: String
    },
    firstFrame: {
      type: String
    }
  },
  data () {
    return {
      show: true,
      firstFrameShow: false
    }
  },
  methods: {
    showToggle () {
      this.show = !this.show
    },
    showFirstFrame (flag) {
      this.firstFrameShow = flag
    }
  },
  mounted () {
    let vm = this
    this.$refs.verticalVideoRef.ontimeupdate = function () {
      let temp = this.currentTime / this.duration
      vm.$parent.$parent.calcProgress((temp * 100) - ((temp * 100) % 1) + 1)
      vm.$parent.playNext((temp * 100) - ((temp * 100) % 1) + 1)
    }
  }
}
</script>

<style lang="scss" scoped>
  .vertical-video {
    position: relative;
    width: 100%;
  }
  .video-first-frame-wrapper {
    position: absolute;
    top: 0;
    // background-color: rgba($color: #ffffff, $alpha: 1);
    .video-first-frame {
      position: relative;
      width: 100%;
      // opacity: 0.3;
      // transform: scale(1.1);
    }
  }
  .fadeIn-enter-active, .fadeIn-leave {
    animation: fade-in .333s;
  }
  @keyframes fade-in {
    0% {
      opacity: 0.5;
      // transform: scale(1.1);
    }
    50% {
      opacity: 0.75;
      // transform: scale(1.05);
    }
    100% {
      opacity: 1;
      // transform: scale(1);
    }
  }

</style>
