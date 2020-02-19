<template>
  <div class="webRTC">
      <v-alert type="error" v-if="error">{{error}}</v-alert>
    <v-btn @click="record" v-if="!localStream">Enregistrer une vidéo</v-btn>
    <video id = "localVideo" playsinline autoplay muted></video>
  </div>
</template>

<script>

export default {
  name: 'webRTC',
  data: function () {
    return {
        error: "",
      localStream: null,
      remoteStream: null,
      pc1: null,
      pc2: null
    }
  },

  methods: {
animate() {
    if (context) {
      var piImage = new Image();

      piImage.onload = function() {
        console.log('Drawing image');
        context.drawImage(piImage, 0, 0, canvas.width, canvas.height);

        texture.needsUpdate = true;
      }

      piImage.src = "http://192.168.0.35/picam/cam_pic.php?time=" + new Date().getTime();
    }

    requestAnimationFrame(animate);

    update();
    render();
  },
    async record() {
        try {
            if(navigator.mediaDevices) {
                if(navigator.mediaDevices.getUserMedia()) {
                    let prom = navigator.mediaDevices.getUserMedia({ audio: true, video: true }).then(this.gotDevices).catch(this.handleMediaError)
                } else {
                    this.error = "Navigator.MediaDevices.getUserMedia() not detected."
                }
            } else {
                this.error = "Navigator.MediaDevices not detected."
            }
        }
        catch(e) {
            this.error = e;
        }

        

    },
    gotDevices: function (stream) {
      let localVideo = document.querySelector('#localVideo')
      this.localStream = stream
      localVideo.srcObject = stream
    },

    handleMediaError: function (error) {
      console.error('Error:' + error.name)
    },
  },

  created: function () {
    console.log('webRTC is created!')
    
//     navigator.mediaDevices.getUserMedia({
//     video: true,
//     audio: true
// }).then(async function(stream) {
//     console.log('1');
//     let recorder = RecordRTC(stream, {
//         type: 'video',
//     });
//     recorder.startRecording();
//     console.log('2');
//     const sleep = m => new Promise(r => setTimeout(r, m));
//     await sleep(3000);
//     console.log('3');

//     recorder.stopRecording(function() {
//         console.log('4');
//         let blob = recorder.getBlob();
//         console.log(blob);
//         invokeSaveAsDialog(blob);
//         console.log('5');
//     });
//     console.log('6');
// });


  }
}
</script>
