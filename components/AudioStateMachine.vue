<script setup lang="ts">
import { computed } from 'vue'

// AudioCoordinatorService hardware states, stepped through the
// "Hey Recipes" exchange from the architecture diagram.
const props = defineProps<{ stage: number }>()

const states = [
  { id: 'listeningWake', label: 'listeningWake', hint: 'on-device VAD + keyword spotting' },
  { id: 'listeningSTT', label: 'listeningSTT', hint: 'speech_to_text → input queue' },
  { id: 'speaking', label: 'speaking', hint: 'output queue → flutter_tts' },
]

const script = [
  { state: 'listeningWake', who: '', text: '' },
  { state: 'listeningSTT', who: 'user', text: '"Hey Recipes"' },
  { state: 'listeningSTT', who: 'user', text: '"Show me how to cook beans"' },
  { state: 'speaking', who: 'agent', text: '"Start by opening a can..."   expectsReply: true' },
  { state: 'listeningSTT', who: 'user', text: '"Which can?"   (mic opened because a reply was expected)' },
  { state: 'speaking', who: 'agent', text: '"The can of beans."   expectsReply: false' },
  { state: 'listeningWake', who: '', text: 'queue drained, no reply expected → back to rest' },
]

const step = computed(() => script[Math.min(props.stage, script.length - 1)])
const history = computed(() => script.slice(1, Math.min(props.stage, script.length - 1) + 1))
</script>

<template>
  <div class="asm">
    <div class="states">
      <template v-for="(s, i) in states" :key="s.id">
        <div class="state" :class="{ active: step.state === s.id }">
          <div class="name">{{ s.label }}</div>
          <div class="hint">{{ s.hint }}</div>
        </div>
        <div v-if="i < states.length - 1" class="arrow">⇄</div>
      </template>
    </div>
    <div class="history">
      <div v-for="(h, i) in history" :key="i" class="line" :class="h.who || 'sys'">
        <span class="tag">{{ h.who || 'system' }}</span> {{ h.text }}
      </div>
    </div>
  </div>
</template>

<style scoped>
.asm {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}
.states {
  display: flex;
  align-items: stretch;
  gap: 0.5rem;
}
.state {
  flex: 1;
  border: 2px solid var(--border);
  border-radius: 12px;
  padding: 0.6rem 0.8rem;
  background: var(--bg-2);
  transition: all 0.35s ease;
}
.state.active {
  border-color: var(--accent);
  background: linear-gradient(180deg, rgba(181, 227, 107, 0.18), var(--bg-2));
  box-shadow: 0 0 22px rgba(181, 227, 107, 0.25);
}
.name {
  font-family: var(--mono);
  font-weight: 700;
  font-size: 0.9rem;
}
.state.active .name {
  color: var(--accent);
}
.hint {
  font-size: 0.65rem;
  color: var(--ink-dim);
  margin-top: 0.2rem;
}
.arrow {
  align-self: center;
  color: var(--ink-faint);
  font-size: 1.2rem;
}
.history {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
  font-family: var(--mono);
  font-size: 0.72rem;
}
.tag {
  display: inline-block;
  width: 4.5rem;
  color: var(--ink-faint);
  text-transform: uppercase;
  font-size: 0.6rem;
  letter-spacing: 0.1em;
}
.user {
  color: #9dcbf5;
}
.agent {
  color: var(--accent);
}
.sys {
  color: var(--ink-dim);
}
</style>
