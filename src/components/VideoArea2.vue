<template>
  <div>
    <div>
      <p class="timerVideo" v-show="timevideo>0">
        <v-icon color="red" large class="mr-1">mdi-radiobox-marked</v-icon>
        {{timevideo}}
      </p>
      <p class="timecountdown" v-show="timecountdown">{{timecountdown}}</p>

      <video v-show="!selectedPicture" ref="video" id="video" width="640" height="480" autoplay></video>
      <img class="picture" v-show="selectedPicture" :src="selectedPicture" />
    </div>
    <div v-show="!videoTimer && !timecountdown">
      <v-btn v-on:click="capture()">Prendre une {{ selectedPicture ? 'autre' : ''}} Photo</v-btn>
      <v-btn v-on:click="captureVideo()">Faire une Vidéo</v-btn>
    </div>
    <canvas ref="canvas" id="canvas" width="640" height="480"></canvas>
    <ul v-show="!videoTimer && !timecountdown">
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
      record: null,
      mediaStream: null,
      mediaRecorder: null,
      recordedChunks: [],
      timecountdown: 0,
      timevideo: 0,
      countdownTimer: null,
      videoTimer: null,
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
          this.timecountdown = 3;
        let self = this;
        this.countdownTimer = setInterval(function() {
          self.timecountdown--;
          console.log(self.timecountdown);
          if (self.timecountdown <= 0) {
            self.canvas = self.$refs.canvas;
            var context = self.canvas
              .getContext("2d")
              .drawImage(self.video, 0, 0, 640, 480);
            self.captures.push(canvas.toDataURL("image/png"));
            self.selectPicture(self.captures[self.captures.length-1])
            clearInterval(self.countdownTimer);
          } else {
          }
        }, 1000);
      }
    },
    captureVideo() {
      this.selectedPicture = null;
      var options = { mimeType: "video/webm; codecs=vp9" };
      this.record = this.$refs.video.captureStream(25);
      console.log(this.record);
      this.mediaRecorder = new MediaRecorder(this.record, options);
      this.mediaRecorder.ondataavailable = this.handleDataAvailable;
      this.mediaRecorder.start();

      let self = this;
      this.timevideo = 10;
      this.videoTimer = setInterval(function() {
        self.timevideo--;
        console.log(self.timevideo);
        if (self.timevideo <= 0) {
          console.log("stopping");
          self.mediaRecorder.stop();
          self.videoTimer=null;
          clearInterval(this);
        } else {
        }
      }, 1000);
    },
    selectPicture(picture) {
      this.mediaStream = null;
      this.selectedPicture = picture;
    },
    videoPlay() {
      //   if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
      //     navigator.mediaDevices.getUserMedia({ video: true }).then(stream => {});
      //   }

      // Get access to the camera!
      if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
        // Not adding `{ audio: true }` since we only want video now
        navigator.mediaDevices
          .getUserMedia({ audio: false, video: true })
          .then(stream => {
            this.mediaStream = new MediaStream();
            this.video.srcObject = stream;
            // this.video.play();
          })
          .catch(function(err) {
            //log to console first
            console.log(err); /* handle the error */
            if (
              err.name == "NotFoundError" ||
              err.name == "DevicesNotFoundError"
            ) {
              //required track is missing
            } else if (
              err.name == "NotReadableError" ||
              err.name == "TrackStartError"
            ) {
              //webcam or mic are already in use
            } else if (
              err.name == "OverconstrainedError" ||
              err.name == "ConstraintNotSatisfiedError"
            ) {
              //constraints can not be satisfied by avb. devices
            } else if (
              err.name == "NotAllowedError" ||
              err.name == "PermissionDeniedError"
            ) {
              //permission denied in browser
            } else if (err.name == "TypeError" || err.name == "TypeError") {
              //empty constraints object
            } else {
              //other errors
            }
          });
      } else if (navigator.getUserMedia) {
        // Standard
        navigator.getUserMedia(
          { video: true },
          function(stream) {
            this.mediaStream = new MediaStream();
            this.video.srcObject = stream;
          },
          errBack
        );
      } else if (navigator.webkitGetUserMedia) {
        // WebKit-prefixed
        navigator.webkitGetUserMedia(
          { video: true },
          function(stream) {
            this.mediaStream = new MediaStream();
            this.video.src = window.webkitURL.createObjectURL(stream);
          },
          errBack
        );
      } else if (navigator.mozGetUserMedia) {
        // Mozilla-prefixed
        navigator.mozGetUserMedia(
          { video: true },
          function(stream) {
            this.mediaStream = new MediaStream();
            this.video.srcObject = stream;
          },
          errBack
        );
      }
    },
    handleDataAvailable(event) {
      console.log("data-available");
      if (event.data.size > 0) {
        this.recordedChunks.push(event.data);
        console.log(this.recordedChunks);
        this.download();
      } else {
        console.log("no data");
      }
    },
    download() {
      var blob = new Blob(this.recordedChunks, {
        type: "video/webm"
      });
      var url = URL.createObjectURL(blob);
      var a = document.createElement("a");
      document.body.appendChild(a);
      a.style = "display: none";
      a.href = url;
      a.download = "test.webm";
      a.click();
      window.URL.revokeObjectURL(url);
    }
  }
};
</script>

<style>
body {
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
.timerVideo {
  font-size: 3em;
  position: absolute;
  color: white;
  left: 78%;
  text-shadow: #0f0f0f 1px 1px;
  z-index: 100;
}
.timecountdown {
  z-index: 101;
  position: absolute;
  margin: 8% 48%;
  font-size: 10em;

}
</style>