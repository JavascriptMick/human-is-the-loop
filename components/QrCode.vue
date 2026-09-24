<script setup lang="ts">
import QRCode from 'qrcode'
import { ref, watchEffect } from 'vue'

const props = withDefaults(
  defineProps<{ url: string; size?: number; caption?: string }>(),
  { size: 160 },
)

const dataUrl = ref('')

watchEffect(async () => {
  dataUrl.value = await QRCode.toDataURL(props.url, {
    width: props.size * 2,
    margin: 1,
    color: { dark: '#0f1f19', light: '#eef5ef' },
  })
})
</script>

<template>
  <figure class="qr">
    <img v-if="dataUrl" :src="dataUrl" :style="{ width: `${props.size}px` }" alt="" />
    <figcaption v-if="props.caption">{{ props.caption }}</figcaption>
  </figure>
</template>

<style scoped>
.qr {
  margin: 0;
  display: inline-flex;
  flex-direction: column;
  align-items: center;
  gap: 0.4rem;
}
.qr img {
  border-radius: 10px;
}
figcaption {
  font-family: var(--mono);
  font-size: 0.7rem;
  color: var(--ink-dim);
}
</style>
