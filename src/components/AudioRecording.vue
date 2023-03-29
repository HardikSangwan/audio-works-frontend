<template>
  <div id="container">
  <!---<audio id="gum-local" controls></audio>-->
  <div>
      <span id="errorMsg"></span>
  </div>
    <h2>STREAM</h2>
    <div @click="startRecording">
     <img v-if=showRecorder src="../assets/sound-recording-logo.png" width = 50 height = 50 margin = 0>
    </div>
    <div @click="stopRecording">
      <img v-if=!showRecorder src="../assets/stop-recording-logo.png" width = 50 height = 50 margin = 0>
    </div>
    <!--<button v-on:click="startRecording()"> Start Streaming </button>
    <button v-on:click="stopRecording()">Stop Streaming </button>-->
    <div>Streaming Status: <span id="streamingStatus">Not Streaming</span></div>
    <div>WebSocket: <span id="webSocketStatus">Not Connected</span></div>
    <!--<button v-on:click="sendMessage('hello')">Send Message</button>-->
  </div>
</template>

<script>
  export default {
    name: 'AudioRecording',
    data () {
      return {
        mp3: '../assets/example.mp3',
        showRecorder: true,
        headers: {
          'X-Custom-Header': 'some data'
        }
      }
    },
    created:function() {
      this.websocket_uri = 'ws://localhost:8080/socket1'
      this.bufferSize = 2048
      console.log("Starting connection to WebSocket Server")
      this.connection = new WebSocket("ws://localhost:8080/socket1")

      this.connection.onmessage = function(e) {
        console.log(e.data);
        var result
    
        try {
          result = JSON.parse(e.data);
        }  catch (e) {
          console.log(e)
        }
    
        if (typeof(result) !== 'undefined' && typeof(result.error) !== 'undefined') {
          console.log(e)
        }
        else {
          console.log('Fine')
        }
      }
      this.connection.onopen = function(e) {
        console.log(e)
        console.log("connected to server");
        //websocket.send("CONNECTED TO YOU");
        document.getElementById("webSocketStatus").innerHTML = 'Connected';
      }
      this.connection.onclose = function(e) {
        console.log("connection closed (" + e.code + ")");
        console.log(e)
        document.getElementById("webSocketStatus").innerHTML = 'Not Connected';
      }
    },
    methods: {
      sendMessage: function(message) {
        message = {name:'STOP', id:1234, 
        data:[1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230, 230]}
        console.log(this.connection);
        console.log(message)
        this.connection.send(JSON.stringify(message));
        console.log('This')
        console.log(JSON.stringify(message))
      },
      startRecording: function() {
        this.showRecorder = false
        if (document.getElementById("webSocketStatus").innerHTML == 'Connected') {
          document.getElementById("streamingStatus").innerHTML = 'Live Streaming Audio Now';
        }
        else {
          document.getElementById("streamingStatus").innerHTML = 'WebSocket Issue. Audio Streaming Unavailable';
        }
        
        this.streamStreaming = true;
        this.AudioContext = window.AudioContext || window.webkitAudioContext;
        this.context = new AudioContext({
          // if Non-interactive, use 'playback' or 'balanced' // https://developer.mozilla.org/en-US/docs/Web/API/AudioContextLatencyCategory
          latencyHint: 'interactive',
        });
        this.processor = this.context.createScriptProcessor(this.bufferSize, 1, 1);
        this.processor.connect(this.context.destination);
        this.context.resume();
      
        navigator.mediaDevices.getUserMedia({audio: true, video: false}).then(this.handleSuccess);
      }, // closes function startRecording() 
      handleSuccess: function (stream) {
        this.globalStream = stream;
        
        this.input = this.context.createMediaStreamSource(stream);
        this.input.connect(this.processor);
    
        this.processor.onaudioprocess = (e) => {
          //console.log(e)
          var left = e.inputBuffer.getChannelData(0);
          var left16 = downsampleBuffer(left, 44100, 16000);
          function downsampleBuffer(buffer, sampleRate, outSampleRate) {
            if (outSampleRate == sampleRate) {
              return buffer;
            }
            if (outSampleRate > sampleRate) {
              throw 'downsampling rate show be smaller than original sample rate';
            }
            var sampleRateRatio = sampleRate / outSampleRate;
            var newLength = Math.round(buffer.length / sampleRateRatio);
            var result = new Int16Array(newLength);
            var offsetResult = 0;
            var offsetBuffer = 0;
            while (offsetResult < result.length) {
              var nextOffsetBuffer = Math.round((offsetResult + 1) * sampleRateRatio);
              var accum = 0,
                count = 0;
              for (var i = offsetBuffer; i < nextOffsetBuffer && i < buffer.length; i++) {
                accum += buffer[i];
                count++;
              }
          
              result[offsetResult] = Math.min(1, accum / count) * 0x7fff;
              offsetResult++;
              offsetBuffer = nextOffsetBuffer;
            }
            return result.buffer;
          }
          var left16bufferarray = new Uint8Array( left16 );
          for ( var i = 0; i < left16; ++i ) {
            left16bufferarray[i] = left16[i];
          }
          var Buffer = require('buffer/').Buffer
          var audData = Buffer.from(left16bufferarray)
          var a = audData.values()
          let res = a.next();
          var byteAud = []
          while (!res.done) {
            byteAud.push(res.value); // 1 3 5 7 9
            res = a.next();
          }
          //var isLast = false
          this.connection.send(JSON.stringify({name:"test", id:123, data:byteAud}));
        };
      },
      stopRecording:function() {
        this.showRecorder = true
        if (document.getElementById("webSocketStatus").innerHTML == 'Connected') {
          document.getElementById("streamingStatus").innerHTML = 'Finished Streaming, audio file saved';        }
        else {
          document.getElementById("streamingStatus").innerHTML = 'WebSocket Issue. Audio Streaming Unavailable';
        }
        this.streamStreaming = false;
      
        let track = this.globalStream.getTracks()[0];
        //console.log(track)
        track.stop();
      
        this.input.disconnect(this.processor);
        this.processor.disconnect(this.context.destination);
        this.context.close().then(() => {
          this.input = null;
          this.processor = null;
          this.context = null;
          this.AudioContext = null;
        });
        
        this.connection.send(JSON.stringify({name:'STOP', id:1234, data:[]}))
        window.location.reload();
      } //closes function stopRecording()   
    }
  }
</script>