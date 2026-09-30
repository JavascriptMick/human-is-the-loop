<script setup lang="ts">
// The four rules for an in-app agent, revealed one per click.
// The same names are reused as kickers in part 2 and on the takeaways slide.
const props = defineProps<{ stage: number }>()

const cards = [
  {
    icon: 'i-carbon-tool-kit',
    title: '① Bounded by the app',
    body: 'Tools are the app\'s own functions, over the data the app already has, driving the screens the user sees. If the user can\'t do it, the agent can\'t either.',
    not: 'no code execution, no web search, no open-ended retrieval',
  },
  {
    icon: 'i-carbon-flow',
    title: '② Workflows in code',
    body: 'Known processes like cooking, shopping and planning are written in code. The LLM picks the path, it doesn\'t invent the steps.',
    not: 'no LLM deciding "what should I do next?"',
  },
  {
    icon: 'i-carbon-locked',
    title: '③ Arm and gate',
    body: 'Each turn the LLM sees only the few tools that make sense right now. Anything not armed is rejected in code.',
    not: 'no whole-app tool list, no trusting the model\'s choice',
  },
  {
    icon: 'i-carbon-user-favorite',
    title: '④ Follow attention',
    body: 'The loop follows what the user is focused on right now, and remembers what they set aside.',
    not: 'no spinning on one goal until it\'s "done"',
  },
]
</script>

<template>
  <div class="grid">
    <div
      v-for="(c, i) in cards"
      :key="c.title"
      class="card boundary"
      :class="{ shown: props.stage > i }"
    >
      <div class="head">
        <div :class="c.icon" class="icon" />
        <div class="title">{{ c.title }}</div>
      </div>
      <div class="body">{{ c.body }}</div>
      <div class="not"><span class="i-carbon-close-outline" /> {{ c.not }}</div>
    </div>
  </div>
</template>

<style scoped>
.grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
}
.boundary {
  opacity: 0.12;
  transform: scale(0.97);
  transition: all 0.4s ease;
}
.boundary.shown {
  opacity: 1;
  transform: scale(1);
}
.head {
  display: flex;
  align-items: center;
  gap: 0.6rem;
}
.icon {
  font-size: 1.6rem;
  color: var(--accent);
}
.title {
  font-weight: 800;
  font-size: 1.1rem;
}
.body {
  margin-top: 0.4rem;
  font-size: 0.95rem;
  line-height: 1.4;
}
.not {
  margin-top: 0.6rem;
  font-family: var(--mono);
  font-size: 0.7rem;
  color: var(--danger);
  display: flex;
  align-items: center;
  gap: 0.35rem;
}
</style>
