<template>
  <div class="audio-works">
    <form id="audio-form">
      <h2>UPLOAD</h2>
      <input id="file" name="file" type="file" accept="application/mp3"><br>
      <label for="name">Name</label>
      <input id="name" name="name" v-model="filename" type="text">
      <button type="submit" :disabled="filename == ''">Save</button>
    </form>
    <div id="audio-list">
      <header>
          <h2>FILES</h2>
      </header>
      <ul id="your-audios">
      </ul>
    </div>
    <div id="audio-player">
        <header>
          <ul><li><h4 id="file-details"></h4></li></ul> 
        </header>
        <audio id="audio-screen" controls></audio>
    </div>
  </div>
</template>

<script>
export default {
  name: 'AudioWorks',
  props: {
    msg: String
  }, 
  data(){
    return {
      filename: '',
    };
  }
}
document.addEventListener("DOMContentLoaded", () =>
{
const form = document.querySelector('#audio-form');
const audioDev = document.querySelector('#audio-player');
const audioScreen = document.querySelector('#audio-screen');
var myAuds = []

const queryParams = Object.fromEntries(new URLSearchParams(window.location.search));

fetch('http://localhost:8080/api/audio/all')
    .then(result => result.json())
    .then(result => {
        console.log(result)

        myAuds = document.querySelector('#your-audios');
        myAuds.setAttribute("display", "block")
        myAuds.setAttribute("float", "left")
        if(result.length > 0){
            for(let aud of result){
                const li = document.createElement('LI');
                li.setAttribute("display", "block")
                li.setAttribute("float", "left")
                const link = document.createElement('A');
                link.innerText = aud;
                link.href = window.location.origin + window.location.pathname + '?audio=' + aud;
                li.appendChild(link);
                myAuds.appendChild(li);
            }
        }else{
            myAuds.innerHTML = 'No audio files found';
        }

    });

if(queryParams.audio){

    audioScreen.src = `http://localhost:8080/api/audio/${queryParams.audio}`;
    var audioDetails = `http://localhost:8080/api/audio/info/${queryParams.audio}`;
    audioDev.style.display = 'block';
    //document.querySelector('#now-playing')
    //    .innerText = 'Now playing ' + queryParams.audio;
    fetch(audioDetails).then(result => result.text()).then(r => {
        console.log(r)
        document.querySelector('#file-details')
        .innerText = 'Details of ' + queryParams.audio + ' : ' + r
    });

}

form.addEventListener('submit', ev => {
    ev.preventDefault();
    let data = new FormData(form);
    console.log(data)
    fetch('http://localhost:8080/api/audio', {
        method: 'POST',
        body: data
    }).then(result => result.text()).then(r => {
        console.log(r)
        //delete queryParams.audio
        //window.location.search = null
        window.location.reload();
    });

});
});
</script>
