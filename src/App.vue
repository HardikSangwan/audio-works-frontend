<template>
  <ul>
  <li>
    <div style="text-align: left;">
      <a href="https://emotech.ai" target="_blank" rel="noopener"><img alt="EmoTech logo" src="./assets/logo.png" width = 200 height = 150 id ="log"></a>
      <h1>EmoTech Interview Assessment: <br> Audio Demo App</h1>
      <h3>Getting Started</h3>
      <p>
        Choose between creating a live audio recording or uploading an audio file. <br>
        For all files, you can view the recording's metadata, play it or delete it. <br>
      </p>
      <h3>Sources</h3>
      <div>
        <ul>
          <li><a href="https://webrtc.github.io/samples/" target="_blank" rel="noopener">webrtc samples</a></li>
          <li><a href="https://gitlab.com/milan.wittpohl/full-stack-web-app-demo/" target="_blank" rel="noopener">full stack app demo</a></li>
          <li><a href="https://emotech.ai" target="_blank" rel="noopener">emotech.ai</a></li>
        </ul>
      </div>
      <br><br><p>For feedback/issues, email the developer</p>
      <a href="mailto:hardik.sngn@gmail.com?subject=EmoTech Interview Next Steps" target="_blank" rel="noopener"> <img alt="Mail logo" src="./assets/mail_logo.png" width="30" height="22" style="vertical-align: bottom;"> </a>
    </div>
  </li>
  <li>
    <div id='stream'><AudioRecording/></div>
    <div id='upload'><AudioWorks/></div>
  </li>
  </ul>
</template>

<script>
import AudioWorks from './components/AudioWorks.vue'
import AudioRecording from './components/AudioRecording.vue'

export default {
  name: 'App',
  components: {
    AudioWorks,
    AudioRecording
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #ecf5ff;
  background-color: #091623;
  margin-top: 0px;
  margin-left:10px;
}
img
{
  margin: 10px;
}
#logo
{
  float: left;    
  margin: 0 0 0 20px;
}
li {
  display: block;
  float: left;
  margin: 10px 10px 0 0;
}
#stream {
  margin: 50px 0px 0px 150px;
}
#upload {
  margin: 50px 0px 0px 150px;
  width: 320px;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #ecf5ff;
  background-color: #091623;
}
h1 {
  margin: 0px;
}
h3 {
  margin: 15px 0 0 0;
}
ul {
  list-style-type: none;
  padding: 0px;
}
a {
  color: #42b983;
}
body {
  background-color: #091623;
}
button {
  margin: 10px 10px 10px 10px
}
</style>
