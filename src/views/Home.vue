<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <VideoArea />
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
import VideoArea from '@/components/VideoArea.vue'

export default {
  name: 'Home',
  components: {
    VideoArea
  }
}
</script>
