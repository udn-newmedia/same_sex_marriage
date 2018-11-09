<template>
  <div class="vert-video-wrapper">
    <div class="vert-video-container">
        <video playsinline loop muted autoplay :src="src"></video>
    </div>
  </div>
</template>

<script>
export default {
  name: 'VerticleVideo',
  props: {
    src: {
      type: String
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
