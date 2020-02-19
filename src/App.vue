<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        
        <v-img
          alt="Vuetify Name"
          class="shrink mt-1 hidden-sm-and-down"
          contain
          min-width="100"
          src="https://i2.wp.com/blog.petehouston.com/wp-content/uploads/2017/08/picam.jpg?fit=1280%2C720&ssl=1"
          width="100"
        /> VideoPi
      </div>

      <v-spacer></v-spacer>

      <v-btn
        href="https://github.com/vuetifyjs/vuetify/releases/latest"
        target="_blank"
        text
      >
        <span class="mr-2">V.0.0.1</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>

    <v-content class="pa-0">
      <router-view></router-view>
    </v-content>
  </v-app>
</template>

<script>
import HelloWorld from './components/HelloWorld';
import VideoArea from './components/VideoArea';

export default {
  name: 'App',

  components: {
    VideoArea,
  },

  data: () => ({
    //
  }),
};
</script>
