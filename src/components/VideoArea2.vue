<template>
  <div>
    <div>
      <video v-show="!selectedPicture" ref="video" id="video" width="640" height="480" autoplay></video>
      <img class="picture" v-show="selectedPicture" :src="selectedPicture" />
    </div>
    <div>
      <v-btn v-on:click="capture()">Prendre une Photo</v-btn>
    </div>
    <canvas ref="canvas" id="canvas" width="640" height="480"></canvas>
    <ul>
      <li v-for="(c, i) in captures" :key="'c'+i">
        <img class="picture" v-bind:src="c" height="50" @click="selectPicture(c)" />
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      video: {},
      canvas: {},
      captures: [],
      mediaStream: null,
      showPicture: false,
      selectedPicture: null
    };
  },
  mounted() {
    this.video = this.$refs.video;
    this.videoPlay();
  },
  methods: {
    capture() {
      if (this.selectedPicture) {
        this.selectedPicture = null;
        this.videoPlay();
      } else {
        this.canvas = this.$refs.canvas;
        var context = this.canvas
          .getContext("2d")
          .drawImage(this.video, 0, 0, 640, 480);
        this.captures.push(canvas.toDataURL("image/png"));
      }
    },
    selectPicture(picture) {
      this.mediaStream = null;
      this.selectedPicture = picture;
    },
    videoPlay() {
      if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
        navigator.mediaDevices.getUserMedia({ video: true }).then(stream => {
          this.mediaStream = new MediaStream();
          this.video.srcObject = stream;
          // this.video.play();
        });
      }
    }
  }
};
</script>

<style>
body: {
  background-color: #f0f0f0;
}
#app {
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
#video {
  background-color: #000000;
  width: 80%;
}
#canvas {
  display: none;
}
li {
  display: inline;
  padding: 5px;
  cursor: pointer;
}
.picture {
    transform: scaleX(-1);
}
</style>