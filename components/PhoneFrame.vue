<script setup lang="ts">
import { onSlideEnter, onSlideLeave, useSlideContext } from '@slidev/client'
import { ref } from 'vue'

// A phone bezel around a screenshot or a screen recording.
// A video sets the bezel's aspect ratio from its own dimensions, so it is never cropped.
// Without `sound` a video is a silent background loop. With `sound` it plays once
// with audio from the start each time the slide is entered, and pauses on leave.
// Audio only plays in the audience view, so presenter mode doesn't double it up.
const props = withDefaults(
  defineProps<{
    src: string
    video?: boolean
    sound?: boolean
    width?: number
    caption?: string
  }>(),
  { video: false, sound: false, width: 220 },
)

const { $renderContext } = useSlideContext()
const videoEl = ref<HTMLVideoElement>()
const aspectRatio = ref('1080 / 2400')
const audible = props.sound && $renderContext.value === 'slide'

function onVideoMetadata(e: Event) {
  const v = e.target as HTMLVideoElement
  if (v.videoWidth && v.videoHeight)
    aspectRatio.value = `${v.videoWidth} / ${v.videoHeight}`
}

if (props.video && props.sound) {
  onSlideEnter(() => {
    const v = videoEl.value
    if (!v)
      return
    v.currentTime = 0
    v.muted = !audible
    // Unmuted playback is blocked until the page has had a user gesture: fall back to muted.
    v.play().catch(() => {
      v.muted = true
      v.play().catch(() => {})
    })
  })
  onSlideLeave(() => videoEl.value?.pause())
}
</script>

<template>
  <figure class="phone-wrap" :style="{ width: `${props.width}px` }">
    <div class="phone" :style="{ aspectRatio }">
      <video
        v-if="props.video && props.sound"
        ref="videoEl"
        :src="props.src"
        muted
        playsinline
        preload="auto"
        @loadedmetadata="onVideoMetadata"
      />
      <video v-else-if="props.video" :src="props.src" autoplay muted loop playsinline @loadedmetadata="onVideoMetadata" />
      <img v-else :src="props.src" alt="" />
    </div>
    <figcaption v-if="props.caption">{{ props.caption }}</figcaption>
  </figure>
</template>

<style scoped>
.phone-wrap {
  margin: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.phone {
  width: 100%;
  border-radius: 26px;
  border: 7px solid #0a0f0d;
  outline: 1px solid var(--border);
  overflow: hidden;
  box-shadow: 0 20px 50px rgba(0, 0, 0, 0.55);
  background: #000;
}
.phone img,
.phone video {
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: top;
  display: block;
}
figcaption {
  margin-top: 0.5rem;
  font-family: var(--mono);
  font-size: 0.65rem;
  color: var(--ink-dim);
  text-align: center;
}
</style>
