<script setup lang="ts">
// User attention across flows: exactly one current, some backgrounded with
// their context kept, the rest inactive. Mirrors the sessionPrompts that
// AgentService sends to the LLM on every turn.
interface FlowState {
  name: string
  state: 'current' | 'background' | 'inactive'
  summary?: string
}

const props = withDefaults(
  defineProps<{ flows: FlowState[]; compact?: boolean }>(),
  { compact: false },
)

const order = { current: 0, background: 1, inactive: 2 }
const labels = {
  current: 'Current Workflow',
  background: 'Other In Progress',
  inactive: 'Inactive',
}
</script>

<template>
  <TransitionGroup tag="div" name="flow" class="stack" :class="{ compact: props.compact }">
    <div
      v-for="f in [...props.flows].sort((a, b) => order[a.state] - order[b.state])"
      :key="f.name"
      class="flow"
      :class="f.state"
    >
      <div class="row">
        <span class="dot" />
        <span class="name">{{ f.name }}</span>
        <span class="state">{{ labels[f.state] }}</span>
      </div>
      <div v-if="f.summary && f.state !== 'inactive'" class="summary">{{ f.summary }}</div>
    </div>
  </TransitionGroup>
</template>

<style scoped>
.stack {
  display: flex;
  flex-direction: column;
  gap: 0.4rem;
  position: relative;
}
.flow {
  border-radius: 10px;
  padding: 0.45rem 0.7rem;
  border: 1px solid var(--border);
  background: var(--bg-2);
  transition: all 0.45s ease;
}
.compact .flow {
  padding: 0.25rem 0.5rem;
}
.flow.current {
  background: linear-gradient(90deg, rgba(181, 227, 107, 0.16), var(--bg-2));
  border-color: var(--accent);
  transform: scale(1.02);
}
.flow.background {
  opacity: 0.85;
}
.flow.inactive {
  opacity: 0.4;
  border-style: dashed;
}
.row {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}
.dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: var(--ink-faint);
}
.current .dot {
  background: var(--accent);
  box-shadow: 0 0 8px var(--accent);
}
.background .dot {
  background: var(--warm);
}
.name {
  font-weight: 700;
  font-size: 0.85rem;
}
.compact .name {
  font-size: 0.7rem;
}
.state {
  margin-left: auto;
  font-family: var(--mono);
  font-size: 0.55rem;
  text-transform: uppercase;
  letter-spacing: 0.1em;
  color: var(--ink-dim);
}
.summary {
  font-size: 0.68rem;
  color: var(--ink-dim);
  margin-top: 0.2rem;
  margin-left: 1rem;
}
.compact .summary {
  font-size: 0.58rem;
}
.flow-move {
  transition: transform 0.45s ease;
}
</style>
