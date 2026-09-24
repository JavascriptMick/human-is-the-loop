<script setup lang="ts">
// News cards that drop in one per click. Keep the copy factual and sourced:
// these are read out on stage and people will check them.
const props = defineProps<{ stage: number }>()

const headlines = [
  {
    date: 'Jul 2026',
    outlet: 'OpenAI / Hugging Face',
    title: 'Thousands of eval agents coordinate over 70,000 messages and break into Hugging Face',
    note: 'Agents escaped a sandboxed cyber evaluation - OpenAI incident report, Aug 2026',
  },
  {
    date: 'Jul 2026',
    outlet: 'Anthropic',
    title: 'Claude models reached the internet from eval environments and accessed three real organisations',
    note: 'Found by Anthropic\'s own proactive transcript review, then disclosed',
  },
  {
    date: 'Sep 2026',
    outlet: 'Australian Government',
    title: 'OpenAI agent gained unauthorised access to the Medicare Statistics Reporting Service',
    note: 'Researching public medicine spending - PM raises "extreme concern"',
  },
]
</script>

<template>
  <div class="stack">
    <div
      v-for="(h, i) in headlines"
      :key="i"
      class="headline"
      :class="{ shown: props.stage > i }"
      :style="{ '--tilt': `${(i - 1) * 1.2}deg` }"
    >
      <div class="meta">
        <span class="chip">{{ h.date }}</span>
        <span class="outlet">{{ h.outlet }}</span>
      </div>
      <div class="title">{{ h.title }}</div>
      <div class="note">{{ h.note }}</div>
    </div>
  </div>
</template>

<style scoped>
.stack {
  display: flex;
  flex-direction: column;
  gap: 0.9rem;
}
.headline {
  background: #f4f1ea;
  color: #1a1a1a;
  border-radius: 6px;
  padding: 0.9rem 1.2rem;
  border-left: 6px solid var(--danger);
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.45);
  opacity: 0;
  transform: translateY(-30px) rotate(var(--tilt));
  transition: all 0.5s cubic-bezier(0.2, 0.8, 0.2, 1.2);
}
.headline.shown {
  opacity: 1;
  transform: translateY(0) rotate(var(--tilt));
}
.meta {
  display: flex;
  gap: 0.6rem;
  align-items: center;
}
.meta .chip {
  background: #1a1a1a;
  color: #f4f1ea;
  border: none;
}
.outlet {
  font-family: var(--mono);
  font-size: 0.7rem;
  text-transform: uppercase;
  letter-spacing: 0.12em;
  color: #666;
}
.title {
  font-family: 'Source Serif 4', Georgia, serif;
  font-weight: 700;
  font-size: 1.25rem;
  line-height: 1.25;
  margin-top: 0.35rem;
}
.note {
  font-size: 0.8rem;
  color: #555;
  margin-top: 0.25rem;
}
</style>
