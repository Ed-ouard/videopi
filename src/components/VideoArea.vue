<template>
  <div class="webRTC">
      <v-alert type="error" v-if="error">{{error}}</v-alert>
    <!-- <v-btn @click="record" v-if="!localStream">Enregistrer une vidéo</v-btn> -->
    <!-- <video id = "localVideo" playsinline autoplay muted></video> -->


    <div class="select">
        <label for="audioSource">Audio input source: </label><select id="audioSource"></select>
    </div>

    <div class="select">
        <label for="audioOutput">Audio output destination: </label><select id="audioOutput"></select>
    </div>

    <div class="select">
        <label for="videoSource">Video source: </label><select id="videoSource"></select>
    </div>

    <video id="video" playsinline autoplay></video>


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





/*
*  Copyright (c) 2015 The WebRTC project authors. All Rights Reserved.
*
*  Use of this source code is governed by a BSD-style license
*  that can be found in the LICENSE file in the root of the source
*  tree.
*

'use strict';

const videoElement = document.querySelector('video');
const audioInputSelect = document.querySelector('select#audioSource');
const audioOutputSelect = document.querySelector('select#audioOutput');
const videoSelect = document.querySelector('select#videoSource');
const selectors = [audioInputSelect, audioOutputSelect, videoSelect];

audioOutputSelect.disabled = !('sinkId' in HTMLMediaElement.prototype);

function gotDevices(deviceInfos) {
  // Handles being called several times to update labels. Preserve values.
  const values = selectors.map(select => select.value);
  selectors.forEach(select => {
    while (select.firstChild) {
      select.removeChild(select.firstChild);
    }
  });
  for (let i = 0; i !== deviceInfos.length; ++i) {
    const deviceInfo = deviceInfos[i];
    const option = document.createElement('option');
    option.value = deviceInfo.deviceId;
    if (deviceInfo.kind === 'audioinput') {
      option.text = deviceInfo.label || `microphone ${audioInputSelect.length + 1}`;
      audioInputSelect.appendChild(option);
    } else if (deviceInfo.kind === 'audiooutput') {
      option.text = deviceInfo.label || `speaker ${audioOutputSelect.length + 1}`;
      audioOutputSelect.appendChild(option);
    } else if (deviceInfo.kind === 'videoinput') {
      option.text = deviceInfo.label || `camera ${videoSelect.length + 1}`;
      videoSelect.appendChild(option);
    } else {
      console.log('Some other kind of source/device: ', deviceInfo);
    }
  }
  selectors.forEach((select, selectorIndex) => {
    if (Array.prototype.slice.call(select.childNodes).some(n => n.value === values[selectorIndex])) {
      select.value = values[selectorIndex];
    }
  });
}

navigator.mediaDevices.enumerateDevices().then(gotDevices).catch(handleError);

// Attach audio output device to video element using device/sink ID.
function attachSinkId(element, sinkId) {
  if (typeof element.sinkId !== 'undefined') {
    element.setSinkId(sinkId)
        .then(() => {
          console.log(`Success, audio output device attached: ${sinkId}`);
        })
        .catch(error => {
          let errorMessage = error;
          if (error.name === 'SecurityError') {
            errorMessage = `You need to use HTTPS for selecting audio output device: ${error}`;
          }
          console.error(errorMessage);
          // Jump back to first output device in the list as it's the default.
          audioOutputSelect.selectedIndex = 0;
        });
  } else {
    console.warn('Browser does not support output device selection.');
  }
}

function changeAudioDestination() {
  const audioDestination = audioOutputSelect.value;
  attachSinkId(videoElement, audioDestination);
}

function gotStream(stream) {
  window.stream = stream; // make stream available to console
  videoElement.srcObject = stream;
  // Refresh button list in case labels have become available
  return navigator.mediaDevices.enumerateDevices();
}

function handleError(error) {
  console.log('navigator.MediaDevices.getUserMedia error: ', error.message, error.name);
}

function start() {
  if (window.stream) {
    window.stream.getTracks().forEach(track => {
      track.stop();
    });
  }
  const audioSource = audioInputSelect.value;
  const videoSource = videoSelect.value;
  const constraints = {
    audio: {deviceId: audioSource ? {exact: audioSource} : undefined},
    video: {deviceId: videoSource ? {exact: videoSource} : undefined}
  };
  navigator.mediaDevices.getUserMedia(constraints).then(gotStream).then(gotDevices).catch(handleError);
}

audioInputSelect.onchange = start;
audioOutputSelect.onchange = changeAudioDestination;

videoSelect.onchange = start;

start();
*/
</script>

<style>
video {
  transform: scale(-1, 1);
}
</style>
