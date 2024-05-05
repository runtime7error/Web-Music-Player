<template>
  <div>
    <input
      type="file"
      ref="fileInput"
      @change="handleFileUpload"
      accept="audio/*"
      style="display: none"
    />
    <div>
      <span
        v-for="(char, index) in chars"
        :key="index"
        class="char"
        @mouseover="setColor(index)"
        @mouseout="removeColor(index)"
        @click="triggerFileUpload"
        :style="{ color: charColors[index] || 'white' }"
      >
        {{ char }}
      </span>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, nextTick } from 'vue'

export default defineComponent({
  name: 'InteractiveText',
  props: {
    initialText: String
  },
  setup(props, { emit }) {
    const text: string = "Let's Get Started It !"
    const charColors = ref<Array<string | null>>(Array(text.length).fill(null))
    const chars = ref<Array<string>>(text.split(''))
    const audioUrl = ref<string | null>(null)
    const fileInput = ref<HTMLInputElement | null>(null)

    function setColor(index: number): void {
      charColors.value[index] = '#00ffff'
    }

    function generateRandomColor(): string {
      return (
        '#' +
        Math.floor(Math.random() * 16777215)
          .toString(16)
          .padStart(6, '0')
      )
    }

    function removeColor(index: number): void {
      charColors.value[index] = generateRandomColor()
    }

    function handleFileUpload(event: Event) {
      const files = (event.target as HTMLInputElement).files
      if (files && files[0]) {
        const file = files[0]
        audioUrl.value = URL.createObjectURL(file)
        playAudio();
      }
    }

    function playAudio() {
      emit('confirmed', audioUrl.value as string)
    }

    const triggerFileUpload = () => {
      fileInput.value?.click()
    }

    return {
      chars,
      charColors,
      setColor,
      removeColor,
      triggerFileUpload,
      handleFileUpload,
      audioUrl,
      fileInput
    }
  }
})
</script>

<style scoped></style>
