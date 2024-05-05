<script setup lang="ts">
import { ref } from 'vue'
import MainBtn from './components/MainBtn.vue'
import MusicPlayer from './components/MusicPlayer.vue'

const showPlayer = ref(false)
const audioUrl = ref<string>()
const mscPlayer = ref<any | null>(null)
const mainBtn = ref<any | null>(null)

function togglePlayerVisibility(newUrl: string): void {
  showPlayer.value = !showPlayer.value
  audioUrl.value = newUrl
}
</script>

<template>
  <main>
    <transition name="fade">
      <div class="wrapper" v-show="!showPlayer">
        <MainBtn @confirmed="togglePlayerVisibility" ref="mainBtn" />
      </div>
    </transition>
    <transition name="fade">
      <div v-show="showPlayer"><MusicPlayer ref="mscPlayer" :audioUrl="audioUrl" /></div>
    </transition>
  </main>
</template>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}

.wrapper {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  font-size: 53px;
  transition: 0.3s;
  font-family: 'Montserrat', sans-serif;
}

.wrapper:hover {
  cursor: pointer;
}

@media (max-width: 1036px) {
  .wrapper {
    font-size: 24px;
    padding: 10px;
  }
}

@media (max-width: 508px) {
  .wrapper {
    font-size: 18px;
    padding: 5px;
    position: absolute;
    transform: none;
    text-align: center;
    top: 50%;
    left: 34%;
  }
}
</style>
