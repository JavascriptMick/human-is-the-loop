<script setup lang="ts">
// SVG rebuild of flutter_agent_framework/docs/current_architecture.png,
// revealed one layer per click. Stage 0 shows the full picture dimmed.
const props = withDefaults(defineProps<{ stage?: number }>(), { stage: 99 })

interface Box {
  id: string
  x: number
  y: number
  w: number
  h: number
  title: string
  lines?: string[]
  stage: number
  kind?: 'core' | 'app' | 'ext' | 'user'
}

interface Edge {
  d: string
  label?: string
  lx?: number
  ly?: number
  anchor?: 'start' | 'middle' | 'end'
  stage: number
}

const boxes: Box[] = [
  { id: 'user', x: 10, y: 180, w: 90, h: 88, title: 'User', lines: ['voice', 'touch', 'text'], stage: 1, kind: 'user' },
  { id: 'audio', x: 140, y: 150, w: 200, h: 150, title: 'AudioCoordinator', lines: ['wake word (VAD + KWS)', 'speech_to_text', 'flutter_tts', 'input / output queues', 'barge-in'], stage: 1 },
  { id: 'agent', x: 400, y: 180, w: 170, h: 90, title: 'AgentService', lines: ['runs each turn', 'executes tools', 'owns attention'], stage: 2 },
  { id: 'llmsvc', x: 400, y: 330, w: 170, h: 46, title: 'LLMService', lines: ['CompletionProvider'], stage: 2 },
  { id: 'llm', x: 420, y: 412, w: 130, h: 40, title: 'LLM (Gemini)', stage: 2, kind: 'ext' },
  { id: 'registry', x: 380, y: 20, w: 210, h: 100, title: 'IntentRegistry', lines: ['flows · current flow', 'global tools', 'armed intent tools', 'interrupt queue'], stage: 3 },
  { id: 'flow', x: 650, y: 20, w: 290, h: 170, title: 'CookingAssistant implements IntentFlow', lines: ['@IntentTool startCooking(recipe_id)', '@IntentTool stepComplete()', '_context  (flow state)', '_orchestrate()  → messages + next tools'], stage: 4, kind: 'app' },
  { id: 'signals', x: 650, y: 250, w: 130, h: 56, title: 'Signals stores', lines: ['app state'], stage: 5, kind: 'app' },
  { id: 'router', x: 810, y: 250, w: 130, h: 56, title: 'GoRouter', lines: ['appRouter.go()'], stage: 5, kind: 'app' },
  { id: 'screen', x: 700, y: 350, w: 190, h: 90, title: 'Screens', lines: ['Watch.builder(...)', 'same UI the user taps'], stage: 5, kind: 'app' },
]

const edges: Edge[] = [
  { d: 'M100,215 L140,215', stage: 1 },
  { d: 'M140,240 L100,240', stage: 1 },
  { d: 'M340,210 L400,210', label: 'text in', lx: 370, ly: 202, stage: 2 },
  { d: 'M400,245 L340,245', label: 'speak', lx: 370, ly: 260, stage: 2 },
  { d: 'M470,270 L470,330', label: 'msgs + tools', lx: 440, ly: 304, stage: 2 },
  { d: 'M505,330 L505,270', label: 'tool calls', lx: 540, ly: 304, stage: 2 },
  { d: 'M485,376 L485,412', stage: 2 },
  { d: 'M445,180 L445,120', label: 'armed tools', lx: 405, ly: 152, stage: 3 },
  { d: 'M485,120 L485,180', label: 'set attention', lx: 527, ly: 152, stage: 3 },
  { d: 'M570,200 L650,120', label: 'execute tool', lx: 604, ly: 134, anchor: 'end', stage: 4 },
  { d: 'M650,160 L570,230', label: 'IntentResult', lx: 628, ly: 226, stage: 4 },
  { d: 'M715,190 L715,250', stage: 5 },
  { d: 'M875,190 L875,250', stage: 5 },
  { d: 'M715,306 L760,350', stage: 5 },
  { d: 'M875,306 L830,350', stage: 5 },
]

const visible = (s: number) => props.stage >= s
</script>

<template>
  <svg viewBox="0 0 960 470" class="arch">
    <defs>
      <marker id="arch-arrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6" markerHeight="6" orient="auto">
        <path d="M0,0 L10,5 L0,10 z" fill="var(--accent-dim)" />
      </marker>
    </defs>

    <g v-for="e in edges" :key="e.d" class="edge" :class="{ on: visible(e.stage) }">
      <path :d="e.d" marker-end="url(#arch-arrow)" />
      <text v-if="e.label" :x="e.lx" :y="e.ly" :text-anchor="e.anchor ?? 'middle'">{{ e.label }}</text>
    </g>

    <g v-for="b in boxes" :key="b.id" class="box" :class="[b.kind ?? 'core', { on: visible(b.stage) }]">
      <rect :x="b.x" :y="b.y" :width="b.w" :height="b.h" rx="10" />
      <text :x="b.x + 10" :y="b.y + 20" class="title">{{ b.title }}</text>
      <text
        v-for="(l, i) in b.lines ?? []" :key="l"
        :x="b.x + 10" :y="b.y + 38 + i * 16" class="line"
      >{{ l }}</text>
    </g>

    <g class="legend">
      <rect x="10" y="420" width="12" height="12" rx="3" class="lg-core" />
      <text x="28" y="430">framework</text>
      <rect x="110" y="420" width="12" height="12" rx="3" class="lg-app" />
      <text x="128" y="430">your app</text>
    </g>
  </svg>
</template>

<style scoped>
.arch {
  width: 100%;
  height: auto;
  font-family: Inter, sans-serif;
}
.box rect {
  fill: var(--bg-2);
  stroke: var(--border);
  stroke-width: 1.5;
  transition: all 0.4s ease;
}
.box.core rect {
  fill: var(--surface);
}
.box.app rect {
  fill: #1c2f3d;
  stroke: #2d5a85;
}
.box.ext rect {
  fill: #2b2440;
  stroke: #6b5bb5;
}
.box.user rect {
  fill: var(--bg-2);
  stroke: var(--ink-dim);
}
.box {
  opacity: 0.12;
  transition: opacity 0.45s ease;
}
.box.on {
  opacity: 1;
}
.title {
  fill: var(--ink);
  font-size: 13px;
  font-weight: 700;
}
.box.core.on .title {
  fill: var(--accent);
}
.line {
  fill: var(--ink-dim);
  font-family: var(--mono);
  font-size: 10.5px;
}
.edge {
  opacity: 0.08;
  transition: opacity 0.45s ease;
}
.edge.on {
  opacity: 1;
}
.edge path {
  fill: none;
  stroke: var(--accent-dim);
  stroke-width: 1.6;
}
.edge text {
  fill: var(--ink-dim);
  font-family: var(--mono);
  font-size: 9.5px;
}
.legend text {
  fill: var(--ink-dim);
  font-size: 11px;
}
.lg-core {
  fill: var(--surface);
  stroke: var(--border);
}
.lg-app {
  fill: #1c2f3d;
  stroke: #2d5a85;
}
</style>
