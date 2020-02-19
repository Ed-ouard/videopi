<template>
  <div class="home">    
      <v-row>
        <v-col :cols="12" :md="6">      
          <VideoArea />
        </v-col>
        <v-col :cols="12" :md="6">
        </v-col>
      </v-row>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
import VideoArea from '@/components/VideoArea2.vue'

export default {
  name: 'Home',
  components: {
    VideoArea
  }
}
</script>
