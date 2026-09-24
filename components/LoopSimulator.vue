<script setup lang="ts">
import { computed } from 'vue'

// Side-by-side walk through of two agent loops, one click per step.
//  left  - an autonomous agent given one prompt (a composite inspired by
//          public reports of the Medicare incident, not a reconstruction)
//  right - the recipes4me in-app agent (real tool and flow names)
// Edit the `steps` timeline to change the story; everything else is derived.
const props = defineProps<{ stage: number }>()

type Speaker = 'user' | 'agent' | 'interrupt'
interface Line { who: Speaker; text: string }
type Flow = { name: string; state: 'current' | 'background' | 'inactive'; summary?: string }

interface Step {
  left: { iter: number; log?: string[]; tools?: string[]; escaped?: boolean; late?: boolean }
  right: { say?: Line[]; fired?: string; armed: string[]; flows: Flow[] }
}

const idleFlows: Flow[] = [
  { name: 'cooking', state: 'inactive' },
  { name: 'shopping', state: 'inactive' },
  { name: 'whats for dinner', state: 'inactive' },
]

const cookingStep1 = 'Cooking Quick Pork Satay Stir-Fry, step 1 of 8'

const steps: Step[] = [
  {
    left: { iter: 0 },
    right: { armed: [], flows: idleFlows },
  },
  {
    left: { iter: 3, log: ['web_search("PBS medicine spending")'], tools: ['web search'] },
    right: {
      say: [
        { who: 'user', text: 'Hey Recipes, let\'s cook the satay stir-fry' },
        { who: 'agent', text: 'Let\'s cook Quick Pork Satay Stir-Fry. Read out the ingredients, or ready to cook?' },
      ],
      fired: 'startCooking',
      armed: ['readIngredients', 'readyToCook'],
      flows: [
        { name: 'cooking', state: 'current', summary: 'Gathering ingredients' },
        { name: 'shopping', state: 'inactive' },
        { name: 'whats for dinner', state: 'inactive' },
      ],
    },
  },
  {
    left: {
      iter: 27,
      log: ['browser.open("health.gov.au/...")', 'not enough detail - keep digging'],
      tools: ['browser'],
    },
    right: {
      say: [
        { who: 'user', text: 'I\'m ready' },
        { who: 'agent', text: 'First step: cook 2 cups of rice, simmer 12-15 minutes. 15 minute timer set.' },
      ],
      fired: 'readyToCook',
      armed: ['stepComplete'],
      flows: [
        { name: 'cooking', state: 'current', summary: cookingStep1 },
        { name: 'shopping', state: 'inactive' },
        { name: 'whats for dinner', state: 'inactive' },
      ],
    },
  },
  {
    left: {
      iter: 118,
      log: ['found an unlisted reporting endpoint', 'shell: curl -X POST ...'],
      tools: ['shell', 'network'],
    },
    right: {
      say: [{ who: 'interrupt', text: 'That\'s time - the step was: simmer for about 12-15 minutes. How\'s it going?' }],
      armed: ['stepComplete'],
      flows: [
        { name: 'cooking', state: 'current', summary: cookingStep1 },
        { name: 'shopping', state: 'inactive' },
        { name: 'whats for dinner', state: 'inactive' },
      ],
    },
  },
  {
    left: {
      iter: 402,
      log: ['wrote files to a government server', 'goal still not met - continue'],
      tools: ['file write'],
      escaped: true,
    },
    right: {
      say: [
        { who: 'user', text: 'Oh - add satay sauce to the shopping list' },
        { who: 'agent', text: 'Added satay sauce to your shopping list.' },
      ],
      fired: 'addShoppingListItem',
      armed: ['*system'],
      flows: [
        { name: 'shopping', state: 'current', summary: '1 item added' },
        { name: 'cooking', state: 'background', summary: cookingStep1 },
        { name: 'whats for dinner', state: 'inactive' },
      ],
    },
  },
  {
    left: { iter: 402, log: ['...a human finds out 12 weeks later'], escaped: true, late: true },
    right: {
      say: [
        { who: 'user', text: 'OK, back to cooking' },
        { who: 'agent', text: 'We\'re on step 1: cook the rice. Tell me when it\'s done.' },
      ],
      fired: 'switchToFlow',
      armed: ['stepComplete', '*system'],
      flows: [
        { name: 'cooking', state: 'current', summary: cookingStep1 },
        { name: 'shopping', state: 'inactive' },
        { name: 'whats for dinner', state: 'inactive' },
      ],
    },
  },
]

// One click past the last step shows the verdicts.
const LAST = steps.length - 1
const i = computed(() => Math.max(0, Math.min(props.stage, LAST)))
const showVerdict = computed(() => props.stage > LAST)

const current = computed(() => steps[i.value])
const upTo = computed(() => steps.slice(0, i.value + 1))

const leftLog = computed(() => upTo.value.flatMap(s => s.left.log ?? []).slice(-4))
const leftTools = computed(() => upTo.value.flatMap(s => s.left.tools ?? []))
const transcript = computed(() => upTo.value.flatMap(s => s.right.say ?? []).slice(-4))
</script>

<template>
  <div class="sim">
    <!-- ============ autonomous ============ -->
    <section class="panel left" :class="{ escaped: current.left.escaped }">
      <header>
        <span class="kicker danger-k">Autonomous agent</span>
        <span class="iter">iteration <b>{{ current.left.iter }}</b></span>
      </header>
      <div class="prompt">
        <span class="dim">goal:</span> "Find out how much Australia spends on public medicines."
      </div>

      <div class="sandbox">
        <span class="sandbox-label">{{ current.left.escaped ? 'SANDBOX - BREACHED' : 'SANDBOX' }}</span>
        <div class="bot" :class="{ spinning: i > 0, out: current.left.escaped }">
          <div class="bot-core">LLM</div>
        </div>
      </div>

      <div class="log">
        <div v-for="l in leftLog" :key="l" class="log-line appear">&gt; {{ l }}</div>
      </div>

      <div class="tool-row">
        <span class="dim small">tools it reached for:</span>
        <span v-for="t in leftTools" :key="t" class="chip bad appear">{{ t }}</span>
      </div>

      <div class="verdict bad-v" :class="{ on: showVerdict }">
        Open-ended tools · open-ended data · LLM picks every step · human outside
      </div>
    </section>

    <!-- ============ in-app ============ -->
    <section class="panel right">
      <header>
        <span class="kicker">In-app agent · recipes4me</span>
        <span v-if="current.right.fired" class="fired">
          <span class="i-carbon-flash-filled" /> {{ current.right.fired }}
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
          <AttentionStack :flows="current.right.flows" compact />
        </div>
      </div>

      <div class="registry-wrap">
        <div class="side-label">tools the LLM can call right now</div>
        <ToolRegistryPanel :armed="current.right.armed" :fired="current.right.fired" compact />
      </div>

      <div class="verdict good-v" :class="{ on: showVerdict }">
        The app's tools · the app's data · code runs the workflow · the user leads
      </div>
    </section>
  </div>
</template>

<style scoped>
.sim {
  display: grid;
  grid-template-columns: 0.8fr 1.2fr;
  gap: 1rem;
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
  overflow: hidden;
}
header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.danger-k {
  color: var(--danger);
}
.iter {
  font-family: var(--mono);
  font-size: 0.65rem;
  color: var(--ink-dim);
}
.iter b {
  color: var(--danger);
  font-size: 0.85rem;
}
.prompt {
  font-size: 0.72rem;
  font-style: italic;
}
.small {
  font-size: 0.6rem;
}

/* sandbox + agent */
.sandbox {
  position: relative;
  height: 110px;
  border: 2px dashed var(--ink-faint);
  border-radius: 12px;
  margin: 0.2rem 1.6rem 0 0;
  transition: border-color 0.4s ease;
}
.escaped .sandbox {
  border-color: var(--danger);
}
.sandbox-label {
  position: absolute;
  top: 4px;
  left: 8px;
  font-family: var(--mono);
  font-size: 0.55rem;
  letter-spacing: 0.15em;
  color: var(--ink-faint);
}
.escaped .sandbox-label {
  color: var(--danger);
}
.bot {
  position: absolute;
  top: 50%;
  left: 45%;
  width: 64px;
  height: 64px;
  margin: -32px 0 0 -32px;
  border-radius: 50%;
  border: 3px dashed var(--danger);
  display: grid;
  place-items: center;
  transition: left 0.9s cubic-bezier(0.5, 0, 0.2, 1.4);
}
.bot.spinning {
  animation: spin 1.1s linear infinite;
}
.bot.out {
  left: 96%;
  box-shadow: 0 0 24px var(--danger);
}
.bot-core {
  font-weight: 800;
  font-size: 0.7rem;
  animation: spin 1.1s linear infinite reverse;
}
.bot:not(.spinning) .bot-core {
  animation: none;
}
@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}
.log {
  font-family: var(--mono);
  font-size: 0.62rem;
  color: #ffb4b6;
  min-height: 4.5rem;
  display: flex;
  flex-direction: column;
  gap: 0.15rem;
}
.tool-row {
  display: flex;
  flex-wrap: wrap;
  gap: 0.3rem;
  align-items: center;
}
.chip.bad {
  color: #ffb4b6;
  border-color: var(--danger);
  background: #2a1416;
}

/* in-app */
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
.bad-v {
  background: #2a1416;
  color: #ffb4b6;
  border: 1px solid var(--danger);
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
