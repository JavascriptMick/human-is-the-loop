<script setup lang="ts">
import { useIsSlideActive, useSlideContext } from '@slidev/client'
import { ref, watch } from 'vue'

// A phone bezel around a screenshot or a screen recording.
// A video sets the bezel's aspect ratio from its own dimensions, so it is never cropped.
// Without `sound` a video is a silent background loop. With `sound` it waits at the start
// behind a play icon and plays with audio on the slide's next click (`playAt`, default 1),
// so the slide needs `clicks` set to at least that. Stepping back before `playAt` or leaving
// the slide stops and rewinds it. Clicks are shared between windows, so presenter mode
// can drive it. Audio only plays in the audience view, so presenter mode doesn't double it up.
const props = withDefaults(
  defineProps<{
    src: string
    video?: boolean
    sound?: boolean
    playAt?: number
    width?: number
    caption?: string
  }>(),
  { video: false, sound: false, playAt: 1, width: 220 },
)

const { $clicks, $renderContext } = useSlideContext()
const videoEl = ref<HTMLVideoElement>()
const aspectRatio = ref('1080 / 2400')
const playing = ref(false)
const audible = props.sound && $renderContext.value === 'slide'

function onVideoMetadata(e: Event) {
  const v = e.target as HTMLVideoElement
  if (v.videoWidth && v.videoHeight)
    aspectRatio.value = `${v.videoWidth} / ${v.videoHeight}`
}

if (props.video && props.sound) {
  const active = useIsSlideActive()
  // Slidev reports a passed slide as fully clicked, so `reached` alone would start the video
  // while skipping over this slide or arriving back on it. Only a click on the slide itself
  // starts it: the slide was already active and `reached` flipped on.
  watch(
    [active, () => $clicks.value >= props.playAt] as const,
    ([isActive, reached], [wasActive, wasReached]) => {
      const v = videoEl.value
      if (!v)
        return
      if (!isActive || !reached) {
        v.pause()
        v.currentTime = 0
        return
      }
      if (!wasActive || wasReached)
        return
      v.muted = !audible
      // Unmuted playback is blocked until this window has had a user gesture: fall back to muted.
      v.play().catch(() => {
        v.muted = true
        v.play().catch(() => {})
      })
    },
  )
}
</script>

<template>
  <figure class="phone-wrap" :style="{ width: `${props.width}px` }">
    <div class="phone" :style="{ aspectRatio }">
      <template v-if="props.video && props.sound">
        <video
          ref="videoEl"
          :src="props.src"
          muted
          playsinline
          preload="auto"
          @loadedmetadata="onVideoMetadata"
          @play="playing = true"
          @pause="playing = false"
        />
        <div v-if="!playing" class="play" aria-hidden="true">
          <svg viewBox="0 0 24 24"><path d="M8 5v14l11-7z" /></svg>
        </div>
      </template>
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
  position: relative;
}
.phone img,
.phone video {
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: top;
  display: block;
}
.play {
  position: absolute;
  inset: 0;
  margin: auto;
  width: 56px;
  height: 56px;
  border-radius: 50%;
  border: 1px solid rgba(255, 255, 255, 0.35);
  background: rgba(0, 0, 0, 0.55);
  color: #fff;
  display: grid;
  place-items: center;
  pointer-events: none;
}
.play svg {
  width: 26px;
  height: 26px;
  margin-left: 3px;
  fill: currentColor;
}
figcaption {
  margin-top: 0.5rem;
  font-family: var(--mono);
  font-size: 0.65rem;
  color: var(--ink-dim);
  text-align: center;
}
</style>
