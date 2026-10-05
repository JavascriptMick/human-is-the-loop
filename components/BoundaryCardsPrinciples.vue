<script setup lang="ts">
// The four principles for engaging Agentic UX, revealed one per click.
const props = defineProps<{ stage: number }>();

const cards = [
  {
    icon: "i-carbon-user-favorite",
    title: "① Understand the user's intent",
    body: "Understand what problem the user is trying to solve, and what they want to achieve",
    not: "don't make assumptions about what the user is thinking",
  },
  {
    icon: "i-carbon-execution-history",
    title: "② Respond to changes but take notes",
    body: "React to changes in intent with relevant tools and routing but hold state for backgrounded intents",
    not: "don't force the user to start-from-scratch when returning to a forgotten task",
  },
  {
    icon: "i-carbon-education",
    title: "③ Be an Expert",
    body: "Be expert in a problem domain and understand concepts and processes that work inside it",
    not: "don't be a generalist, it adds no value",
  },
  {
    icon: "i-carbon-touch-interaction",
    title: "④ No Magic",
    body: "Operate through the same functional and visual surfaces available to the user",
    not: "don't use hidden functions or data sources that the user can't access themselves",
  },
];
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
