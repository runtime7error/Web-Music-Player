<template v-show="isVisible">
  <section class="container">
    <div class="elements">
      <h1>Web Music Player</h1>
    </div>
    <div class="player elements">
      <img class="player-image play" src="../assets/vinil.png" alt="Vinyl" />
    </div>
    <div class="elements">
      <div id="compteur">
        <div class="progress-container">
          <div id="myBarBuffer" class="progress-bar"></div>
          <div id="myBar" class="buffer-bar" :style="{ width: progress + '%' }"></div>
        </div>
        <span>{{ currentTimeFormatted }}</span> :
        <span>{{ durationFormatted }}</span>
      </div>
      <audio @loadedmetadata="updateDuration" @timeupdate="updateProgress" ref="audioPlayer" controls v-if="audioUrl" style="display: none">
        <source :src="audioUrl" type="audio/mpeg" controls />
        Your browser does not support the audio element.
      </audio>
      <div class="player-controls">
        <button id="backwardBtn" aria-label="Retroceder" class="control-button" @click="rewind">
          &#9664;&#9664;
        </button>
        <button id="playBtn" aria-label="Play/Pause" class="control-button" @click="playBtn">
          <span v-html="isPlaying ? '&#10074;&#10074;' : '&#9654;'"></span>
        </button>
        <button id="forwardBtn" aria-label="Avançar" class="control-button" @click="forward">&#9654;&#9654;</button>
      </div>
    </div>
  </section>
  <footer>
    <p>&copy; 2024 | By <a>Lucas Henrique</a> | Audio Player</p>
  </footer>
</template>

<script setup lang="ts">
import { ref, computed, watch, nextTick } from 'vue'

const props = defineProps({
  audioUrl: String
})

const isPlaying = ref(false);
const currentTime = ref(0)
const duration = ref(0)
const audioPlayer = ref<HTMLAudioElement | null>(null)

const currentTimeFormatted = computed(() => formatTime(currentTime.value))
const durationFormatted = computed(() => formatTime(duration.value))
const progress = computed(() => {
  if (duration.value === 0) return 0;
  return (currentTime.value / duration.value) * 100;
});

function formatTime(time: number) {
  const mins = Math.floor(time / 60)
  const secs = Math.floor(time % 60)
  return `${mins}:${secs < 10 ? '0' + secs : secs}`
}

function togglePlay() {
  if (audioPlayer.value) {
    audioPlayer.value.play()
    isPlaying.value = true;
  }
}

function playBtn() {
  if (audioPlayer.value?.paused) {
    audioPlayer.value.play()
    isPlaying.value = true;
  } else {
    audioPlayer.value?.pause()
    isPlaying.value = false;
  }
}

function updateDuration() {
  if (audioPlayer.value) {
    duration.value = audioPlayer.value.duration
  }
}

function updateProgress() {
  if (audioPlayer.value) {
    currentTime.value = audioPlayer.value.currentTime
  }
}
function forward() {
  if (audioPlayer.value) {
    audioPlayer.value.currentTime = Math.min(audioPlayer.value.currentTime + 10, audioPlayer.value.duration);
  }
}

function rewind() {
  if (audioPlayer.value) {
    audioPlayer.value.currentTime = Math.max(audioPlayer.value.currentTime - 10, 0);
  }
}

watch(
  () => props.audioUrl,
  (newSource) => {
    if (newSource) {
      nextTick(() => {
        if (audioPlayer.value) {
          audioPlayer.value.src = newSource
          audioPlayer.value.load()
          togglePlay()
        }
      })
    }
  },
  { immediate: true }
)
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Montserrat:ital,wght@0,100..900;1,100..900&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  text-decoration: none;
  border: none;
  outline: none;
  scroll-behavior: smooth;
  font-family: 'Montserrat', sans-serif;
}

button {
  background: none;
  border-radius: 50%;
}

h1 {
  font-weight: 400;
  font-size: 30px;
  letter-spacing: 7px;
  padding-bottom: 10px;
}

.container {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}

.elements {
  text-align: center;
  width: 100%;
}

.player-image {
  height: 400px;
  width: 400px;
}

.play {
  animation: spin 4s infinite linear;
}

.progress-container {
  width: 100%;
  height: 10px;
  background-color: #ddd;
  position: relative;
  overflow: hidden;
  border-radius: 5px;
}

.buffer-bar,
.progress-bar {
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
}

.buffer-bar {
  background-color: #666666;
}

.progress-bar {
  background-color:#cecccc;
  width: 0%;
}

.player-controls {
  display: flex;
  justify-content: center;
  align-items: center;
}

.control-button {
  width: 50px;
  height: 50px;
  border: none;
  color: #7f7f7f;
  font-size: 24px;
  padding: 10px 20px;
  margin: 10px;
  cursor: pointer;
  transition:
    background-color 0.3s,
    color 0.3s;
  border-radius: 50%;
  display: inline-flex;
  align-items: center;
  justify-content: center;
}

.control-button:hover {
  color: #ffffff;
}

@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

footer {
  position: fixed;
  bottom: 10px;
  width: 100%;
  text-align: center;
  right: 0%;
}

footer a {
  color: #96eaff;
  transition: 1.2s;
}

footer a:hover {
  color: #96eaff;
}
</style>
