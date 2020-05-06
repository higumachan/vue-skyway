<template>
  <div id="app">
    <video id="my-video" width="400px" autoplay muted playsinline></video>
    <p id="my-id">{{myId}}</p>
    <textarea id="their-id" v-model="theirId"></textarea>
    <button id="make-call" @click="makeCall">発信</button>
    <video id="their-video" width="400px" autoplay muted playsinline></video>
  </div>
</template>

<script>
import Peer from 'skyway-js';
import {skywayApiKey} from "../config/skyway-config";


export default {
  name: 'App',
  data() {
    return {
      localStream: null,
      peer: null,
      myId: "",
      theirId: "",
    }
  },
  components: {
  },
  methods: {
    makeCall() {
      const mediaConnection = this.peer.call(this.theirId, this.localStream);
      this.setEventListener(mediaConnection);
    },
    setEventListener(mediaConnection) {
      mediaConnection.on('stream', stream => {
        const videoElm = document.getElementById('their-video');
        videoElm.srcObject = stream;
        videoElm.play();
      });
    }
  },
  mounted() {
    // カメラ映像取得
    navigator.mediaDevices.getUserMedia({video: true, audio: true})
      .then( stream => {
        // 成功時にvideo要素にカメラ映像をセットし、再生
        const videoElm = document.getElementById('my-video')
        videoElm.srcObject = stream;
        videoElm.play();
        // 着信時に相手にカメラ映像を返せるように、グローバル変数に保存しておく
        this.localStream = stream;
      }).catch( error => {
      // 失敗時にはエラーログを出力
      console.error('mediaDevice.getUserMedia() error:', error);
      return;
    });
    this.peer = new Peer({
      key: skywayApiKey,
      debug: 3
    });
    this.peer.on('open', () => {
      this.myId = this.peer.id;
    });
    this.peer.on('call', mediaConnection => {
      mediaConnection.answer(this.localStream);
      this.setEventListener(mediaConnection);
    });
  }
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
