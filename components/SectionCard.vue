<script setup lang="ts">
defineProps<{
  kicker: string
  title: string
  subtitle?: string
}>()
</script>

<template>
  <div class="section-card">
    <div class="kicker">{{ kicker }}</div>
    <h1 class="title">{{ title }}</h1>
    <p v-if="subtitle" class="subtitle">{{ subtitle }}</p>
    <div class="rule" />
  </div>
</template>

<style scoped>
.section-card {
  display: flex;
  flex-direction: column;
  justify-content: center;
  height: 100%;
  padding-left: 1rem;
}
.title {
  font-size: 4rem !important;
  line-height: 1.05;
  margin-top: 0.6rem;
}
.subtitle {
  color: var(--ink-dim);
  font-size: 1.3rem;
  margin-top: 0.8rem;
}
.rule {
  width: 120px;
  height: 4px;
  background: var(--accent);
  border-radius: 2px;
  margin-top: 1.6rem;
}
</style>
