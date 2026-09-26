<script setup lang="ts">
import { computed } from 'vue'

// Walk through of the recipes4me in-app agent loop (real tool and flow
// names), one click per step.
// Edit the `steps` timeline to change the story; everything else is derived.
const props = defineProps<{ stage: number }>()

type Speaker = 'user' | 'agent' | 'interrupt'
interface Line { who: Speaker; text: string }
type Flow = { name: string; state: 'current' | 'background' | 'inactive'; summary?: string }

interface Step { say?: Line[]; fired?: string; armed: string[]; flows: Flow[] }

const idleFlows: Flow[] = [
  { name: 'cooking', state: 'inactive' },
  { name: 'shopping', state: 'inactive' },
  { name: 'whats for dinner', state: 'inactive' },
]

const cookingStep1 = 'Cooking Quick Pork Satay Stir-Fry, step 1 of 8'

const steps: Step[] = [
  { armed: [], flows: idleFlows },
  {
    say: [
      { who: 'user', text: 'Hey Recipes, let\'s cook the satay stir-fry' },
      { who: 'agent', text: 'Let\'s cook Quick Pork Satay Stir-Fry. Read out the ingredients, or ready to cook?' },
    ],
    fired: 'startCooking',
    armed: ['readIngredients', 'readyToCook', 'cancelFlow'],
    flows: [
      { name: 'cooking', state: 'current', summary: 'Gathering ingredients' },
      { name: 'shopping', state: 'inactive' },
      { name: 'whats for dinner', state: 'inactive' },
    ],
  },
  {
    say: [
      { who: 'user', text: 'I\'m ready' },
      { who: 'agent', text: 'First step: cook 2 cups of rice, simmer 12-15 minutes. 15 minute timer set.' },
    ],
    fired: 'readyToCook',
    armed: ['stepComplete', 'cancelFlow'],
    flows: [
      { name: 'cooking', state: 'current', summary: cookingStep1 },
      { name: 'shopping', state: 'inactive' },
      { name: 'whats for dinner', state: 'inactive' },
    ],
  },
  {
    say: [{ who: 'interrupt', text: 'That\'s time - the step was: simmer for about 12-15 minutes. How\'s it going?' }],
    armed: ['stepComplete', 'cancelFlow'],
    flows: [
      { name: 'cooking', state: 'current', summary: cookingStep1 },
      { name: 'shopping', state: 'inactive' },
      { name: 'whats for dinner', state: 'inactive' },
    ],
  },
  {
    say: [
      { who: 'user', text: 'Oh - add satay sauce to the shopping list' },
      { who: 'agent', text: 'Added satay sauce to your shopping list.' },
    ],
    fired: 'addShoppingListItem',
    armed: ['switchToFlow', 'cancelFlow'],
    flows: [
      { name: 'shopping', state: 'current', summary: '1 item added' },
      { name: 'cooking', state: 'background', summary: cookingStep1 },
      { name: 'whats for dinner', state: 'inactive' },
    ],
  },
  {
    say: [
      { who: 'user', text: 'OK, back to cooking' },
      { who: 'agent', text: 'We\'re on step 1: cook the rice. Tell me when it\'s done.' },
    ],
    fired: 'switchToFlow',
    armed: ['stepComplete', 'cancelFlow'],
    flows: [
      { name: 'cooking', state: 'current', summary: cookingStep1 },
      { name: 'shopping', state: 'inactive' },
      { name: 'whats for dinner', state: 'inactive' },
    ],
  },
]

// One click past the last step shows the verdict.
const LAST = steps.length - 1
const i = computed(() => Math.max(0, Math.min(props.stage, LAST)))
const showVerdict = computed(() => props.stage > LAST)

const current = computed(() => steps[i.value])
const upTo = computed(() => steps.slice(0, i.value + 1))

const transcript = computed(() => upTo.value.flatMap(s => s.say ?? []).slice(-4))
</script>

<template>
  <div class="sim">
    <section class="panel">
      <header>
        <span class="kicker">In-app agent · recipes4me</span>
        <span v-if="current.fired" class="fired">
          <span class="i-carbon-flash-filled" /> {{ current.fired }}
        </span>
      </header>

      <div class="body">
        <div class="transcript">
          <div v-if="transcript.length === 0" key="idle" class="bubble idle">"Hey Recipes..."</div>
          <div v-for="l in transcript" :key="l.text" class="bubble appear" :class="l.who">
            <span v-if="l.who === 'interrupt'" class="int-tag">⏰ interrupt - no user input</span>
            {{ l.text }}
          </div>
        </div>

        <div class="side">
          <div class="side-label">attention</div>
          <AttentionStack :flows="current.flows" compact />
        </div>
      </div>

      <div class="registry-wrap">
        <div class="side-label">tools the LLM can call right now</div>
        <ToolRegistryPanel :armed="current.armed" :fired="current.fired" compact />
      </div>

      <div class="verdict good-v" :class="{ on: showVerdict }">
        The app's tools · the app's data · code runs the workflow · the user leads
      </div>
    </section>
  </div>
</template>

<style scoped>
.sim {
  height: 100%;
}
.panel {
  position: relative;
  border: 1px solid var(--border);
  border-radius: 14px;
  padding: 0.7rem 0.9rem;
  background: var(--bg-2);
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  height: 100%;
  overflow: hidden;
}
header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.fired {
  font-family: var(--mono);
  font-size: 0.7rem;
  color: var(--bg);
  background: var(--accent);
  padding: 0.1rem 0.5rem;
  border-radius: 999px;
  display: inline-flex;
  align-items: center;
  gap: 0.25rem;
}
.body {
  display: grid;
  grid-template-columns: 1.35fr 1fr;
  gap: 0.7rem;
  flex: 1;
}
.transcript {
  display: flex;
  flex-direction: column;
  gap: 0.35rem;
  min-height: 12rem;
}
.bubble {
  font-size: 0.68rem;
  line-height: 1.3;
  padding: 0.35rem 0.6rem;
  border-radius: 10px;
  max-width: 92%;
}
.bubble.user {
  align-self: flex-end;
  background: #1c3b5a;
  border: 1px solid #2d5a85;
}
.bubble.agent {
  align-self: flex-start;
  background: var(--surface);
  border: 1px solid var(--border);
}
.bubble.interrupt {
  align-self: flex-start;
  background: #3a2a14;
  border: 1px solid var(--warm);
}
.bubble.idle {
  color: var(--ink-faint);
  font-style: italic;
}
.int-tag {
  display: block;
  font-family: var(--mono);
  font-size: 0.52rem;
  color: var(--warm);
  margin-bottom: 0.1rem;
}
.side-label {
  font-family: var(--mono);
  font-size: 0.52rem;
  letter-spacing: 0.14em;
  text-transform: uppercase;
  color: var(--ink-faint);
  margin-bottom: 0.3rem;
}
.registry-wrap {
  border-top: 1px solid var(--border);
  padding-top: 0.4rem;
}

/* verdicts */
.verdict {
  font-size: 0.72rem;
  font-weight: 700;
  padding: 0.4rem 0.6rem;
  border-radius: 8px;
  text-align: center;
  opacity: 0;
  transform: translateY(8px);
  transition: all 0.4s ease;
  margin-top: auto;
}
.verdict.on {
  opacity: 1;
  transform: none;
}
.good-v {
  background: rgba(181, 227, 107, 0.12);
  color: var(--accent);
  border: 1px solid var(--accent-dim);
}

/* new lines fade in as they mount */
.appear {
  animation: appear 0.35s ease both;
}
@keyframes appear {
  from {
    opacity: 0;
    transform: translateY(6px);
  }
}
</style>
