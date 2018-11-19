<template>
  <div class="video-list-wrapper" :style="{height: computeHeight, transform: videoListToggleTranslate}" ref="videoListRef">
    <div class="supplemental-block"></div>
    <div class="video-list-container">
      <div class="video-list-split-wrapper">
        <div class="toggle-arrow"></div>
        <div class="video-list-split-line"></div>
      </div>
      <div class="video-image-wrapper" :style="{transform: videoListTranslate}" ref="videoImgsRef">
        <slot></slot>
      </div>
    </div>
  </div>
</template>

<script>
import Utils from 'udn-newmedia-utils'
import * as d3 from 'd3'

export default {
  name: 'VideoList',
  data () {
    return {
      dragPixel: 0,
      currentListPostion: 0,
      previousListPostion: 0,
      toggleDragPixel: 0,
      toggleExtent: 0.55,
      isListToggle: true
    }
  },
  computed: {
    computeHeight: function () {
      return window.innerHeight * 0.6
    },
    videoListTranslate: function () {
      return 'translateX(' + (this.dragPixel) + 'px)'
    },
    videoListToggleTranslate: function () {
      return 'translateY(' + (this.toggleDragPixel) + 'px)'
    },
    listWidth: function () {
      return ((this.$refs.videoImgsRef.children[0].clientWidth * 8) + this.$refs.videoImgsRef.clientWidth) * 0.5 - window.innerWidth - 10
    },
    listHeight: function () {
      return this.$refs.videoListRef.clientHeight
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
        // determing slide left or right.
        let slideDirection = ''

        if (d3.event.x - clickStartPoint > 1) {
          slideDirection = 'left'
        } else if (d3.event.x - clickStartPoint < -1) {
          slideDirection = 'right'
        } else {
          slideDirection = 'no'
        }

        // sliding left but on right side, return to 0
        // sliding right but on left side, return to 0
        // else sliding.
        if (slideDirection === 'left' && vm.dragPixel > 0) {
          videoTranslation(666, 0)
          vm.previousListPostion = 0
        } else if (slideDirection === 'right' && vm.dragPixel < -vm.listWidth) {
          videoTranslation(666, -vm.listWidth)
          vm.previousListPostion = -vm.listWidth
        } else if (slideDirection === 'no') {
          vm.previousListPostion = vm.dragPixel
        } else {
          if (slideDirection === 'left') {
            videoTranslation(333, currentListPostion - tremblePixel)
            vm.previousListPostion = vm.dragPixel
          } else if (slideDirection === 'right') {
            videoTranslation(333, currentListPostion + tremblePixel)
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
      let tremblePixel = 5
      let videoList = d3.select('.video-image-wrapper')
        .call(d3.drag()
          .on('start', dragStarted)
          .on('drag', dragged)
          .on('end', dragEnded)
        )
    },
    assignVerticalDragEvent (vm) {
      function dragStarted () {
        clickStartPoint = d3.event.y
      }

      function dragged () {
        if (vm.isListToggle) {
          currentListPostion = d3.event.y - clickStartPoint
          // dragging up or dragging down
          if (d3.event.y - clickStartPoint < 0) {
            currentListPostion = 0
            specialCaseFlag = true
          } else {
            currentListPostion = d3.event.y - clickStartPoint
            specialCaseFlag = false
          }
        } else {
          currentListPostion = d3.event.y - clickStartPoint + vm.listHeight * 0.8
          // dragging up or dragging down
          if (d3.event.y - clickStartPoint < 0) {
            currentListPostion = d3.event.y - clickStartPoint + vm.listHeight * 0.8
            specialCaseFlag = false
          } else {
            currentListPostion = vm.listHeight * vm.toggleExtent
            specialCaseFlag = true
          }
        }
        vm.toggleDragPixel = currentListPostion
      }

      function dragEnded () {
        if (!specialCaseFlag) {
          if (d3.event.y - clickStartPoint !== 0) {
            event.preventDefault()
            if (d3.event.y - clickStartPoint > 1) {
              vm.videoTranslation(666, vm.toggleDragPixel, vm.listHeight * vm.toggleExtent)
              d3.selectAll('.toggle-arrow').classed('toggle-arrow-down', true)
            } else if (d3.event.y - clickStartPoint < -1) {
              vm.videoTranslation(666, vm.toggleDragPixel, 0)
              d3.selectAll('.toggle-arrow').classed('toggle-arrow-down', false)
            }
            if (vm.isListToggle) {
              vm.isListToggle = false
            } else {
              vm.isListToggle = true
            }
            vm.gaVideoListToggle(vm.isListToggle === true ? '展開' : '收合')
          }
        }
      }

      let specialCaseFlag = false
      let clickStartPoint = 0
      let currentListPostion = 0

      let videoList = d3.select('.video-list-wrapper')
        .call(d3.drag()
          .on('start', dragStarted)
          .on('drag', dragged)
          .on('end', dragEnded)
        )

      // d3.select('.toggle-arrow')
      // videoList
      d3.select('.video-list-split-wrapper')
        .on('click', function () {
          vm.toggleVideoList()
        })
    },
    movingToSelectedVideo (nowPlayIndex) {
      let imageWidth = this.$refs.videoImgsRef.children[0].clientWidth
      let startPosition = this.dragPixel
      let endPosition = this.listWidth < (nowPlayIndex * imageWidth) ? -this.listWidth - 10 : -nowPlayIndex * imageWidth

      // Folding the video list.
      let vm = this
      let videoList = d3.select('.video-list-wrapper')

      if (window.innerWidth <= 768) {
        videoList
          .transition()
          .ease(d3.easeQuad)
          .duration(333)
          .tween('number', function () {
            let k = d3.interpolateRound(startPosition, endPosition)
            return function (t) {
              vm.dragPixel = k(t)
            }
          })
      }

      setTimeout(function () {
        vm.foldVideoList()
      }, 666)
    },
    toggleVideoList () {
      event.preventDefault()

      let from = 0
      let end = this.listHeight * this.toggleExtent
      if (this.isListToggle) {
        this.videoTranslation(666, from, end)
        d3.selectAll('.toggle-arrow').classed('toggle-arrow-down', true)
        this.isListToggle = false
      } else {
        this.videoTranslation(666, end, from)
        d3.selectAll('.toggle-arrow').classed('toggle-arrow-down', false)
        this.isListToggle = true
      }

      this.gaVideoListToggle(this.isListToggle === true ? '展開' : '收合')
    },
    foldVideoList () {
      let from = this.isListToggle === false ? this.listHeight * this.toggleExtent : 0
      let end = this.listHeight * this.toggleExtent

      this.videoTranslation(666, from, end)
      d3.selectAll('.toggle-arrow').classed('toggle-arrow-down', true)
      this.isListToggle = false
    },
    videoTranslation (duration, from, end) {
      let vm = this
      let videoList = d3.select('.video-list-wrapper')
      videoList
        .transition()
        .ease(d3.easeQuad)
        .duration(duration)
        .tween('number', function () {
          let k = d3.interpolateRound(from, end)
          return function (t) {          
            vm.toggleDragPixel = k(t)
          }
        })
    },
    gaVideoListToggle (toggle) {
      window.ga("newmedia.send", {
        "hitType": "event",
        "eventCategory": "vertical_video",
        "eventAction": "click",
        "eventLabel": "[" + Utils.detectPlatform() + "] [" + document.querySelector('title').innerHTML + "] [影片列表" + toggle + "]"
      })
    }
  },
  mounted () {
    let vm = this
    if (window.innerWidth <= 768) {
      this.assignHorizontalDragEvent(vm)
    }
    this.assignVerticalDragEvent(vm)
  }
}
</script>

<style lang="scss" scoped>
  .video-list-wrapper {
    position: fixed;
    z-index: 40;
    left: 0;
    bottom: 0;
    width: 100%;
    background-image: linear-gradient(to top, rgba(0, 0, 0, 1), rgba(0, 0, 0, 0.3), rgba(0, 0, 0, 0));
  }
  .video-list-container {
    position: relative;
    width: 100%;
    height: 70%;
    padding: 10px;
  }
  .supplemental-block {
    position: relative;
    z-index: 15;
    width: 100%;
    height: 30%;
  }
  .video-list-split-wrapper {
    position: relative;
    z-index: 70;
    width: 100%;
    height: 20%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    padding: 10px 0;
    cursor: pointer;
    .video-list-split-line {
      position: absolute;
      left: 0;
      bottom: 10px;
      width: 99%;
      height: 1px;
      background-color: #ffffff;
    }
  }
  .video-image-wrapper {
    position: relative;
    z-index: 10;
    width: 350%;
    height: 80%;
    overflow: hidden;
    @media screen and (min-width: 769px) {
      width: 100%;
    }
  }

  .toggle-arrow {
    position: absolute;
    height: 25px;
    width: 25px;
    top: 0;
    animation: arrow 999ms infinite ease-out;
    &:before, &:after {
      content: '';
      height: 8%;
      width: 48%;
      position: absolute;
      // opacity: 0.8;
      background-color: #ffffff;
      top: 50%;
      transform-origin: center;
      transition: all .3s ease-in-out;
    }
    &:before {
      transform: rotate(230deg) scale(1.5);
      left: 6%;
    }
    &:after {
      transform: rotate(-230deg) scale(1.5);
      right: 6%;
    }
    &-down {
      &:before {
        // animation: LeftSlide .3s ease-in-out, LeftDown .3s ease-in-out;
        transform: rotate(132deg) scale(1.5);;
      }
      &:after {
        // animation: RightSlide .3s ease-in-out, RightDown .3s ease-in-out;
        transform: rotate(-132deg) scale(1.5);;
      }
    }
  }

  @keyframes arrow {
    from{
      transform: translate(0, 0);
    }
    to{
      transform: translate(0, 5px)
    }
  }

  @keyframes LeftSlide {
    0% {
      left: 11%;
    }
    50% {
      left: 2%;
    }
    100% {
      left: 11%;
    }
  }

  @keyframes LeftDown {
    0% {
      transform: rotate(230deg);
    }
    100% {
      transform: rotate(132deg);
    }
  }

  @keyframes RightSlide {
    0% {
      right: 11%;
    }
    50% {
      right: 2%;
    }
    100% {
      right: 11%;
    }
  }

  @keyframes RightDown {
    0% {
      transform: rotate(-230deg);
    }
    100% {
      transform: rotate(-132deg);
    }
  }
</style>
