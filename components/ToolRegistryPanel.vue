<script setup lang="ts">
// What the LLM is allowed to call this turn: global tools are always offered,
// flow and system tools only while armed. Tool names are the real ones from recipes4me.
const props = withDefaults(
  defineProps<{
    armed?: string[]
    fired?: string
    compact?: boolean
  }>(),
  { armed: () => [], compact: false },
)

const globals = ['startCooking', 'startChoosingDinner', 'startPlanning', 'addShoppingListItem', 'listCartItems']

const flowTools: Record<string, string[]> = {
  cooking: ['readIngredients', 'readyToCook', 'stepComplete', 'continueCooking', 'restartCooking'],
  shopping: ['confirmClearAll', 'cancelClearAll', 'removeCartItemById'],
}

const system = ['switchToFlow', 'cancelFlow']

function state(tool: string, isGlobal = false) {
  if (tool === props.fired) return 'fired'
  if (isGlobal || props.armed.includes(tool)) return 'armed'
  return 'off'
}
</script>

<template>
  <div class="registry" :class="{ compact: props.compact }">
    <div class="group">
      <div class="label">global</div>
      <div class="tools">
        <span v-for="t in globals" :key="t" class="tool" :class="state(t, true)">{{ t }}</span>
      </div>
    </div>
    <div v-for="(tools, flow) in flowTools" :key="flow" class="group">
      <div class="label">{{ flow }}</div>
      <div class="tools">
        <span v-for="t in tools" :key="t" class="tool" :class="state(t)">{{ t }}</span>
      </div>
    </div>
    <div class="group">
      <div class="label">system</div>
      <div class="tools">
        <span v-for="t in system" :key="t" class="tool" :class="state(t)">{{ t }}</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.registry {
  display: flex;
  flex-direction: column;
  gap: 0.35rem;
}
.group {
  display: flex;
  gap: 0.5rem;
  align-items: baseline;
}
.label {
  flex: 0 0 4.2rem;
  font-family: var(--mono);
  font-size: 0.55rem;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--ink-faint);
  text-align: right;
}
.tools {
  display: flex;
  flex-wrap: wrap;
  gap: 0.25rem;
}
.tool {
  font-family: var(--mono);
  font-size: 0.58rem;
  padding: 0.08rem 0.4rem;
  border-radius: 4px;
  border: 1px solid transparent;
  transition: all 0.35s ease;
}
.compact .tool {
  font-size: 0.5rem;
}
.tool.off {
  color: var(--ink-faint);
  border-color: #1f3a30;
  opacity: 0.55;
}
.tool.armed {
  color: var(--accent);
  border-color: var(--accent-dim);
  background: rgba(181, 227, 107, 0.08);
}
.tool.fired {
  color: #0f1f19;
  background: var(--accent);
  border-color: var(--accent);
  box-shadow: 0 0 12px var(--accent);
}
</style>
