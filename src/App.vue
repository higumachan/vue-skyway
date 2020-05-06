<template>
  <div id="app">
    <video id="my-video" width="400px" autoplay muted playsinline></video>
    <p id="my-id">{{myId}}</p>
    <textarea id="their-id" v-model="theirId"></textarea>
    <button id="make-call" @click="makeCall(theirId);">発信</button>
    <video id="their-video" width="400px" autoplay muted playsinline></video>
  </div>
</template>

<script>
import skywayManager from "@/mixins/skywayManager";


export default {
  name: 'App',
  data() {
    return {
      myId: "",
      theirId: "",
    }
  },
  components: {
  },
  methods: {
    onGetMediaConnection(mediaConnection) {
      mediaConnection.on('stream', stream => {
        const videoElm = document.getElementById('their-video');
        videoElm.srcObject = stream;
        videoElm.play();
      });
    },
    onPeerOpen() {
      this.myId = this.peer.id;
    },
    onPeerCall(mediaConnection) {
      mediaConnection.answer(this.localStream);
      this.onGetMediaConnection(mediaConnection);
    },
    onGetUserMediaStream(stream) {
      const videoElm = document.getElementById('my-video')
      videoElm.srcObject = stream;
      videoElm.play();
    },
  },
  mixins: [skywayManager],
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
