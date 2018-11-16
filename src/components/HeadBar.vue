<template>
  <header class="header" :style="{ transform: 'translate3d(0,' + header_top + 'px, 0)' }" :class="{'header_open': isMenuOpen}">
    <div class="TheBar" :style="{ backgroundColor: bar_color }">
      <div class="logo_box">
        <a href="https://ubrand.udn.com/ubrand/index" target="_blank" @click="handle_logoGA"><i class="udn-icon udn-icon-logo" :style="{ color: setProps('iconColor') }"></i></a>
        <!-- <a href=""><img class="other_logo" src="../../static/otherlogo.jpg" alt=""></a> -->
        <slot name="logo"></slot>
      </div>
      <div v-if="showMenu" class="menu_box">
        <div class="burger-icon" :class="{ 'burger-open': isMenuOpen }" @click="handle_Burger()">
          <span :style="{ backgroundColor: setProps('iconColor') }"></span>
          <span :style="{ backgroundColor: setProps('iconColor') }"></span>
          <span :style="{ backgroundColor: setProps('iconColor') }"></span>
          <span :style="{ backgroundColor: setProps('iconColor') }"></span>
        </div>
      </div>
      <div class="outlink-wrapper">
        <div class="back-to-video" @click="backToVideo">看影音</div>
        <div class="back-to-video"><a href="https://udn.com/vote2018/index" target="_blank">選舉專區</a></div>  
      </div>
    </div>
    <div class="menu_list" @click="handle_Burger()" :class="{'menu_list-show': showMenuList}">
      <div class="link_box">
        <div class="link_item" :style="{ color: setProps('iconColor') }">
          <slot></slot>
        </div>
      </div>
    </div>
  
    <div class="scrollHint" v-show="showScrollHint" ref="scrollHinter">
      <span>Tip: Shift+滾輪滾動</span>
    </div>
  </header>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
import setProps from '@/mixin/setProps.js'
import Utils from 'udn-newmedia-utils'
import _debounce from 'lodash.debounce'
import _throttle from 'lodash.throttle'
export default {
  name: 'HeadBar',
  mixins: [setProps],
  props: {
    headColor: {
      type: String,
      default: '#fff'
    },
    jsonProps: {
      type: Object,
      default: null
    },
    iconColor: {
      type: String,
      default: '#000'
    }
  },
  data () {
    return {
      isIE: false,
      canNavScroll: false,
      header_top: 0,
      bar_color: 'transparent',
      isMenuOpen: false,
      isNavShow: false,
      showMenuList: false,
      showScrollHint: false,
    }
  },
  computed: {
    showMenu () {
      if (this.$slots.default === undefined) {
        return false
      } else {
        return true
      }
    }
  },
  methods: {
    handle_scroll () {
      let currentH = window.pageYOffset
      if (currentH < 2) {
        this.header_top = 0
        this.isMenuOpen === false ? this.bar_color = 'transparent' : this.bar_color = this.setProps('headColor')
        this.isNavShow = false
        this.showMenuList = false
        if (this.$slots.default !== undefined) {
          for (let i = 0; i < this.$slots.default.length; i++) {
            if (this.$slots.default[i].elm.innerHTML !== undefined && this.$slots.default[i].tag === 'a') {
              this.$slots.default[i].elm.style.backgroundColor = 'transparent'
            }
          }
        }
      } else {
        this.header_top = 2
        this.bar_color = this.setProps('headColor')
        this.isNavShow = true
        this.showMenuList = true
      }
    },
    handle_scrollTo: _throttle(function (title, id) {
      $('html, body').animate({ scrollTop: $('#' + id).offset().top - 85 }, 1333)
      window.ga("newmedia.send", {
        "hitType": "event",
        "eventCategory": "headbar",
        "eventAction": "click",
        "eventLabel": "[" + Utils.detectPlatform() + "] [" + document.querySelector('title').innerHTML + "] [" + title + "] [HeadBar 內滾點擊]"
      })
    }, 1000, {leading: true, trailing: false}),
    handle_nav_arrow (type) {
      switch (type) {
        case 'left':
          $(this.$refs.navigator).animate({ scrollLeft: this.$refs.navigator.scrollLeft - this.$refs.navigator.clientWidth / 5 }, 364)
          break
        case 'right':
          $(this.$refs.navigator).animate({ scrollLeft: this.$refs.navigator.scrollLeft + this.$refs.navigator.clientWidth / 5 }, 364)
          break
      }
    },
    handle_Burger: function (event) {
      if (this.isMenuOpen === false) {
        this.isMenuOpen = true
        this.bar_color = this.setProps('headColor')
      } else {
        this.isMenuOpen = false
        setTimeout(() => {
          window.pageYOffset < 2 ? this.bar_color = 'transparent' : this.bar_color = this.setProps('headColor')
        }, 444)
      }
      window.ga("newmedia.send", {
        "hitType": "event",
        "eventCategory": "hamburger",
        "eventAction": "click",
        "eventLabel": "[" + Utils.detectPlatform() + "] [" + document.querySelector('title').innerHTML + "] [hamburger]"
      })
    },
    handle_resize: _debounce(function () {
      this.isMenuOpen = false
    }, 333),
    handle_logoGA () {
      window.ga("newmedia.send", {
        "hitType": "event",
        "eventCategory": "hamburger",
        "eventAction": "click",
        "eventLabel": "[" + Utils.detectPlatform() + "] [" + document.querySelector('title').innerHTML + "] [圓形logo點擊]]"
      })
    },
    backToVideo () {
      this.$parent.reportFlag = false
      window.ga("newmedia.send", {
        "hitType": "event",
        "eventCategory": "vertical_video",
        "eventAction": "click",
        "eventLabel": "[" + Utils.detectPlatform() + "] [" + document.querySelector('title').innerHTML + "] [看影音按鈕]"
      })
    }
  },
  created () {
    if (Utils.detectIE()) {
      Utils.detectIE() < 16 ? this.isIE = true : this.isIE = false
    }
  },
  mounted () {
    const vm = this
    window.addEventListener('scroll', this.handle_scroll)
    window.addEventListener('resize', this.handle_resize)
    if (this.$slots.default !== undefined) {
      for (let i = 0; i < this.$slots.default.length; i++) {
        if (this.$slots.default[i].elm.innerHTML !== undefined && this.$slots.default[i].tag === 'a') {
          this.$slots.default[i].elm.addEventListener('click', function (e) {
            e.stopPropagation()
            window.ga("newmedia.send", {
              "hitType": "event",
              "eventCategory": "headbar",
              "eventAction": "click",
              "eventLabel": "[" + Utils.detectPlatform() + "] [" + document.querySelector('title').innerHTML + "] [" + vm.$slots.default[i].elm.href + "][" + vm.$slots.default[i].elm.innerHTML + "] [HeadBar 外連點擊]"
            })
          })
          this.$slots.default[i].elm.addEventListener('mouseenter', function (e) {
            if (window.pageYOffset > 2) {
              this.style.color = vm.setProps('headColor')
              this.style.backgroundColor = vm.setProps('iconColor')
            } else {
              this.style.color = vm.setProps('headColor')
              this.style.backgroundColor = vm.setProps('iconColor')
            }
          })
          this.$slots.default[i].elm.addEventListener('mouseleave', function (e) {
            if (window.pageYOffset > 2) {
              this.style.color = vm.setProps('iconColor')
              this.style.backgroundColor = vm.setProps('headColor')
            } else {
              this.style.color = vm.setProps('iconColor')
              this.style.backgroundColor = 'transparent'
            }
          })
        }
      }
    }
  },
  destroyed () {
    const vm = this
    window.removeEventListener('scroll', this.handle_scroll)
    window.removeEventListener('resize', this.handle_resize)
    if (this.$slots.default !== undefined) {
      for (let i = 0; i < this.$slots.default.length; i++) {
        if (this.$slots.default[i].elm.innerHTML !== undefined && this.$slots.default[i].tag === 'a') {
          this.$slots.default[i].elm.removeEventListener('click', function () {
            window.ga("newmedia.send", {
              "hitType": "event",
              "eventCategory": "headbar",
              "eventAction": "click",
              "eventLabel": "[" + Utils.detectPlatform() + "] [" + document.querySelector('title').innerHTML + "] [" + vm.$slots.default[i].elm.href + "][" + vm.$slots.default[i].elm.innerHTML + "] [HeadBar 外連點擊]"
            })
          })
          this.$slots.default[i].elm.removeEventListener('mouseenter', function () {
            if (window.pageYOffset > 2) {
              this.style.color = vm.setProps('headColor')
              this.style.backgroundColor = vm.setProps('iconColor')
            } else {
              this.style.color = vm.setProps('headColor')
              this.style.backgroundColor = vm.setProps('iconColor')
            }
          })
          this.$slots.default[i].elm.removeEventListener('mouseleave', function () {
            if (window.pageYOffset > 2) {
              this.style.color = vm.setProps('iconColor')
              this.style.backgroundColor = vm.setProps('headColor')
            } else {
              this.style.color = vm.setProps('iconColor')
              this.style.backgroundColor = 'transparent'
            }
          })
        }
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.header{
  position: fixed;
  z-index: 9999;
  width: 100%;
  height: 48px;
  transition: transform 222ms ease-out, height 444ms linear;
}
.header_open{
  height: 100vh;
}
.TheBar{
  position: relative;
  z-index: 50;
  width: 100%;
  height: 48px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  // padding-right: 48px;
}
.logo_box{
  position: relative;
  z-index: 100;
  display: flex;
  align-items: center;
  width: 50%;
  height: 100%;
}
.other_logo{
  max-width: 220px;
  max-height: 48px;
}
.udn-icon-logo{
  font-size: 36px;
  color: #000;
  transition: transform 288ms ease-in;
  transform: rotate(0deg);
  text-decoration: none;
  &:hover{
    transform: rotate(16deg);
  }
}
// 選單漢堡頁面
.menu_box{
  position: absolute;
  z-index: 999;
  top: 0;
  right: 0;
  width: 48px;
  height: 48px;
  display: flex;
  align-items: center;
  justify-content: center;
  @media screen and (min-width: 1024px) {
    display: none;
  }
}
.burger-icon {
  width: 46px;
  height: 46px;
  z-index: 30;
  position: relative;
  transform: rotate(0deg);
  transition: .5s ease-in-out;
  cursor: pointer;
  opacity: 1;
  span {
      display: block;
      position: absolute;
      height: 4px;
      width: 30px;
      margin: 0 auto;
      background: #000;
      border-radius: 2px;
      opacity: 1;
      right: 8px;
      transform: rotate(0deg);
      transition: .455s ease-in-out;
  }
  span:nth-child(1){
      top: 12px;
      transition: .343s ease-out;
      transform-origin: right;
  }
  span:nth-child(2),
  span:nth-child(3){
      top: 21px;
  }
  span:nth-child(4){
      top: 30px;
      transition: .343s ease-out;
  }
}
.burger-open{
  span:nth-child(1){
    opacity: 0;
    transform: rotateY(90deg);
  }
  span:nth-child(2){
    transform: rotate(315deg);
  }
  span:nth-child(3){
    transform: rotate(405deg);
  }
  span:nth-child(4){
    opacity: 0;
    transform: rotateY(-90deg);
  }
}
// 選單條列
.menu_list{
  position: absolute;
  z-index: 10;
  padding-top: 48px;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  overflow: hidden;
  opacity: 1;
  background-clip: content-box;
  background-color: #fff;
  transition: height 444ms linear;
  -webkit-overflow-scrolling: auto;
  @media screen and (min-width: 1024px) {
    left: auto;
    padding-top: 0;
    z-index: 51;
    background-color: transparent;
    opacity: 0;
  }
}
.menu_list-show{
  opacity: 1;
}
.link_box{
  margin: 0;
  width: 100%;
  padding: 0;
  display: flex;
  flex-direction: column;
  @media screen and (min-width: 1024px) {
    border-top: none;
    padding: 0;
    flex-direction: row;
  }
}
.link_item{
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background-color: #fff;
  padding: 0 15px 15px 15px;
  color: #000;
  @media screen and (min-width: 1024px) {
    flex-direction: row;
    background-color: transparent;
    padding: 0;
  }
  a{
    flex-shrink: 0;
    position: relative;
    list-style: none;
    width: 100%;
    height: 48px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 20px;
    line-height: 1.4;
    color: inherit;
    transition: 222ms ease;
    border-bottom: 1px solid lightgray;
    padding: 15px;
    color: inherit;
    text-decoration: none;
    @media screen and (min-width: 1024px) {
      width: auto;
      border-bottom: none;
    }
  }
}
// Scroll Nav
.scroll_nav{
  position: absolute;
  z-index: 0;
  top: 40px;
  right: 0;
  overflow: hidden;
  opacity: 0;
  display: flex;
  align-items: center;
  transform: translate3d(0, -200%, 0);
  transition: transform 432ms ease-out;
  padding: 0 12px;
  @media screen and (min-width: 1024px) {
    top: 31px;
  }
}
.nav_show {
  width: 100%;
  opacity: 1;
  background-color: #fff;
  transform: translate3d(0, 0, 0);
  box-shadow: 0 8px 6px -6px rgba(#a4a4a4, .3);
}
.nav_list{
  position: relative;
  top: 8px;
  display: flex;
  align-items: center;
  overflow-x: auto;
  overflow-y: hidden;
  padding-bottom: 8px;
  @media screen and (min-width: 1024px) {
    top: 17px;
    padding-bottom: 17px;
  }
}
.fix-padding{
  padding-bottom: 0 !important;
}
.nav_arrow {
  position: relative;
  top: 8px;
  display: flex;
  justify-content: center;
  align-items: center;
  padding-bottom: 8px;
  width: 25px;
  margin: 0 8px;
  cursor: pointer;
  @media screen and (min-width: 1024px) {
    top: 17px;
    padding-bottom: 17px;
  }
}
.nav_list_item {
  flex-shrink: 0;
  padding: 5px;
  color: #a4a4a4;
  font-weight: normal;
  transition: opacity 444ms ease;
  cursor: pointer;
  &:last-child {
    margin-right: 0;
    &::after{
      content: '';
    }
  }
  &::after{
    position: relative;
    margin-left: 12px;
    top: -1px;
    content: '|';
    color: #a4a4a4;
    opacity: .6;
  }
  @media screen and (min-width: 1025px) {
    &:hover {
      font-weight: bold;
      color: #000;
    }
  }
}
.nav_list_item-active{
  font-weight: bold;
  color: #000;
}
.scrollHint{
  position: fixed;
  top: 0;
  left: 0;
  z-index: 99999;
  background-color: lightgoldenrodyellow;
  font-weight: bold;
  padding: 2.5px;
  opacity: .8;
}
.outlink-wrapper {
  position: absolute;
  right: 0;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.back-to-video {
  position: relative;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 0 10px;
  font-size: 20px;
  cursor: pointer;
  a {
    color: #000000;
  }
  a:hover {
    background-color: #000000;
    color: #ffffff;
    text-decoration: none;
  }
}
.back-to-video:hover {
  background-color: #000000;
  color: #ffffff;
}
</style>
