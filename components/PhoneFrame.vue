<script setup lang="ts">
// A phone bezel around a screenshot or a screen recording.
const props = withDefaults(
  defineProps<{
    src: string
    video?: boolean
    width?: number
    caption?: string
  }>(),
  { video: false, width: 220 },
)
</script>

<template>
  <figure class="phone-wrap" :style="{ width: `${props.width}px` }">
    <div class="phone">
      <video v-if="props.video" :src="props.src" autoplay muted loop playsinline />
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
  aspect-ratio: 1080 / 2400;
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
