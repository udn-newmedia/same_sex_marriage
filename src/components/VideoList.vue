<template>
  <div class="video-list-wrapper" :style="{height: computeHeight}">
    <div class="video-list-container" :style="{transform: videoListToggleTranslate}">
      <div class="video-list-split-wrapper">
        <div class="toggle-arrow-up"></div>
        <div class="video-list-split-line"></div>
      </div>
      <div class="video-image-wrapper" :style="{transform: videoListTranslate}" ref="videoImgsRef">
        <slot></slot>
      </div>
    </div>
  </div>
</template>

<script>
import * as d3 from 'd3'

export default {
  name: 'VideoList',
  data () {
    return {
      dragPixel: 0,
      currentListPostion: 0,
      previousListPostion: 0,
      toggleDragPixel: 0
    }
  },
  computed: {
    computeHeight: function () {
      return window.innerHeight * 0.4
    },
    videoListTranslate: function () {
      return 'translateX(' + (this.dragPixel) + 'px)'
    },
    videoListToggleTranslate: function () {
      return 'translateY(' + (this.toggleDragPixel) + 'px)'
    },
    listWidth: function () {
      return ((this.$refs.videoImgsRef.children[0].clientWidth * 6) + this.$refs.videoImgsRef.clientWidth) * 0.5 - window.innerWidth + 10
    }

  },
  methods: {
    assignHorizontalDragEvent (vm) {
      function dragStarted () {
        clickStartPoint = d3.event.x
      }

      function dragged () {
        currentListPostion = vm.previousListPostion + (d3.event.x - clickStartPoint)
        vm.dragPixel = currentListPostion
      }

      function dragEnded () {
        let isToLeft = d3.event.x - clickStartPoint > 0

        if (isToLeft && vm.dragPixel > 0) {
          videoTranslation(666, 0)
          vm.previousListPostion = 0
        } else if (!isToLeft && vm.dragPixel < -vm.listWidth) {
          videoTranslation(666, -vm.listWidth)
          vm.previousListPostion = -vm.listWidth
        } else {
          if (isToLeft) {
            videoTranslation(333, currentListPostion - 25)
            vm.previousListPostion = vm.dragPixel
          } else {
            videoTranslation(333, currentListPostion + 25)
            vm.previousListPostion = vm.dragPixel
          }
        }
      }

      function videoTranslation (duration, distance) {
        videoList
          .transition()
          .ease(d3.easeQuad)
          .duration(duration)
          .tween('number', function () {
            let k = d3.interpolateRound(vm.dragPixel, distance)
            return function (t) {
              vm.dragPixel = k(t)
            }
          })
      }

      let clickStartPoint = 0
      let currentListPostion = 0
      let videoList = d3.select('.video-image-wrapper')
        .call(d3.drag()
          .on('start', dragStarted)
          .on('drag', dragged)
          .on('end', dragEnded)
        )
    },
    assignVerticalDragEvent(vm) {
      function dragStarted () {
        clickStartPoint = d3.event.y
      }

      function dragged () {
        currentListPostion = d3.event.y - clickStartPoint
        vm.toggleDragPixel = currentListPostion
      }

      function dragEnded () {

      }

      let clickStartPoint = 0
      let currentListPostion = 0
      let videoList = d3.select('.video-list-container')
        .call(d3.drag()
          .on('start', dragStarted)
          .on('drag', dragged)
          .on('end', dragEnded)
        )
    }
  },
  mounted () {
    let vm = this
    this.assignHorizontalDragEvent(vm)
    this.assignVerticalDragEvent(vm)
  }
}
</script>

<style lang="scss" scoped>
  .video-list-wrapper {
    position: fixed;
    bottom: 0;
    width: 100%;
  }
  .video-list-container {
    width: 100%;
    height: 100%;
    padding: 10px;
  }
  .video-list-split-wrapper {
    position: relative;
    width: 100%;
    height: 20%;
    display: flex;
    justify-content: center;
    align-items: center;
    .video-list-split-line {
      width: 95%;
      height: 1px;
      opacity: 0.8;
      background-color: #ffffff;
    }
  }
  .video-image-wrapper {
    position: relative;
    width: 300%;
    height: 80%;
  }
</style>
