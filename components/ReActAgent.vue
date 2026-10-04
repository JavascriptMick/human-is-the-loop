<script setup lang="ts">
import { computed } from 'vue'

// The ReAct agent, one message per click. Left: who talks to whom (the execution).
// Right: what the LLM sees (the context), filling up as the loop runs.
// Stage 13 shows the loop repeating; stage 14 swaps the execution for the
// harness loop as a flowchart.
const props = withDefaults(defineProps<{ stage?: number }>(), { stage: 99 })

type Kind = 'send' | 'ret' | 'act' | 'obs'

interface Box {
  id: string
  x: number
  y: number
  w: number
  h: number
  title: string
}

interface Edge {
  n: number
  kind: Kind
  x1: number
  y1: number
  x2: number
  y2: number
  bx: number
  by: number
  label: string[]
  lx: number
  ly: number
  anchor?: 'start' | 'middle' | 'end'
}

interface Slot {
  tag: string
  label: string
  kind: 'input' | 'think' | 'act' | 'obs' | 'final'
  stage: number
}

const boxes: Box[] = [
  { id: 'app', x: 0, y: 255, w: 72, h: 60, title: 'App' },
  { id: 'api', x: 150, y: 40, w: 200, h: 90, title: 'Model API' },
  { id: 'llm', x: 490, y: 40, w: 130, h: 90, title: 'LLM' },
  { id: 'harness', x: 150, y: 250, w: 200, h: 70, title: 'Harness' },
  { id: 'tool', x: 500, y: 250, w: 120, h: 70, title: 'Tool' },
]

const edges: Edge[] = [
  { n: 1, kind: 'ret', x1: 76, y1: 275, x2: 146, y2: 275, bx: 88, by: 275, label: ['Question'], lx: 114, ly: 265, anchor: 'middle' },
  { n: 2, kind: 'send', x1: 175, y1: 248, x2: 175, y2: 134, bx: 175, by: 160, label: ['POST', 'SP·T·Q'], lx: 167, ly: 184, anchor: 'end' },
  { n: 3, kind: 'send', x1: 356, y1: 55, x2: 484, y2: 55, bx: 372, by: 55, label: ['Model input'], lx: 388, ly: 50 },
  { n: 4, kind: 'ret', x1: 484, y1: 75, x2: 356, y2: 75, bx: 372, by: 75, label: ['ACT'], lx: 388, ly: 70 },
  { n: 5, kind: 'ret', x1: 210, y1: 134, x2: 210, y2: 248, bx: 210, by: 160, label: ['ACT'], lx: 219, ly: 184 },
  { n: 6, kind: 'act', x1: 354, y1: 275, x2: 496, y2: 275, bx: 372, by: 275, label: ['Execute call'], lx: 388, ly: 270 },
  { n: 7, kind: 'obs', x1: 496, y1: 300, x2: 354, y2: 300, bx: 372, by: 300, label: ['OBS'], lx: 388, ly: 314 },
  { n: 8, kind: 'send', x1: 270, y1: 248, x2: 270, y2: 134, bx: 270, by: 160, label: ['POST', 'SP·T·Q·', 'ACT·OBS'], lx: 279, ly: 184 },
  { n: 9, kind: 'send', x1: 356, y1: 100, x2: 484, y2: 100, bx: 372, by: 100, label: ['Updated context'], lx: 388, ly: 95 },
  { n: 10, kind: 'ret', x1: 484, y1: 120, x2: 356, y2: 120, bx: 372, by: 120, label: ['F'], lx: 388, ly: 115 },
  { n: 11, kind: 'ret', x1: 338, y1: 134, x2: 338, y2: 248, bx: 338, by: 160, label: ['F'], lx: 348, ly: 184 },
  { n: 12, kind: 'ret', x1: 146, y1: 300, x2: 76, y2: 300, bx: 88, by: 300, label: ['Answer'], lx: 114, ly: 318, anchor: 'middle' },
]

const slots: Slot[] = [
  { tag: 'SP', label: 'System prompt', kind: 'input', stage: 3 },
  { tag: 'T', label: 'Tool definitions', kind: 'input', stage: 3 },
  { tag: 'Q', label: 'User question', kind: 'input', stage: 3 },
  { tag: 'TH', label: 'Reasoning', kind: 'think', stage: 4 },
  { tag: 'ACT', label: 'Tool call', kind: 'act', stage: 4 },
  { tag: 'OBS', label: 'Tool result', kind: 'obs', stage: 9 },
  { tag: 'TH', label: 'Reasoning', kind: 'think', stage: 13 },
  { tag: 'ACT', label: 'Tool call', kind: 'act', stage: 13 },
  { tag: 'OBS', label: 'Tool result', kind: 'obs', stage: 13 },
  { tag: 'F', label: 'Answer', kind: 'final', stage: 10 },
]

const SLOT_X = 660
const SLOT_Y = 32
const SLOT_STEP = 33
const slotY = (i: number) => SLOT_Y + i * SLOT_STEP

const captions = [
  'Five parts: the App, the Harness, the Model API, the LLM and a Tool.',
  'A Question arrives from the App, triggering the Harness.',
  'The Harness sends System Prompt, Tool definitions and Question to the Model API (HTTP POST).',
  'The API prepares the context (SP + T + Q) and invokes the LLM.',
  'The LLM reasons (TH), finds it can\'t answer yet, and generates an Action (ACT): a tool call.',
  'The API returns the Action to the Harness as a structured tool call.',
  'The Harness invokes the requested Tool with the parameters from the tool call.',
  'The Tool returns its result to the Harness. This becomes an Observation (OBS).',
  'The Harness appends ACT + OBS to the history and POSTs it all again with SP and T.',
  'The API invokes the LLM with the updated context (SP + T + Q + TH + ACT + OBS).',
  'The context is now sufficient: the LLM generates a Final answer (F).',
  'The API returns the Final answer to the Harness.',
  'The Harness returns the Answer to the App.',
  'Needs another tool? Steps 5-9 repeat, adding TH · ACT · OBS each time, until the LLM gives a Final answer.',
  'Call the model. Execute its tools. Repeat until it responds.',
]

const caption = computed(() => captions[Math.min(Math.max(props.stage, 0), captions.length - 1)])
const harnessView = computed(() => props.stage >= 14)

function edgeState(e: Edge) {
  if (props.stage < e.n) return 'off'
  return props.stage === e.n ? 'current' : 'past'
}
</script>

<template>
  <svg viewBox="0 0 960 436" class="react">
    <defs>
      <marker
        v-for="k in ['send', 'ret', 'act', 'obs', 'think']" :id="`ra-${k}`" :key="k"
        viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse"
      >
        <path d="M0,0 L10,5 L0,10 z" :class="`head-${k}`" />
      </marker>
    </defs>

    <!-- section headings -->
    <text x="0" y="16" class="heading">{{ harnessView ? 'THE HARNESS LOOP' : 'THE EXECUTION' }}</text>
    <text :x="SLOT_X" y="16" class="heading">THE CONTEXT</text>
    <line x1="640" y1="4" x2="640" y2="382" class="divider" />

    <!-- execution -->
    <g class="swap exec" :class="{ on: !harnessView }">
      <g v-for="b in boxes" :key="b.id" class="box" :class="b.id">
        <rect :x="b.x" :y="b.y" :width="b.w" :height="b.h" rx="12" />
        <text :x="b.x + b.w / 2" :y="b.id === 'llm' ? b.y + 30 : b.y + b.h / 2 + 7" class="box-title">{{ b.title }}</text>
      </g>
      <rect x="505" y="82" width="100" height="36" rx="8" class="reason" />
      <text x="555" y="104" class="reason-label">Reason / decide</text>

      <g v-for="e in edges" :key="e.n" class="edge" :class="[e.kind, edgeState(e)]">
        <line :x1="e.x1" :y1="e.y1" :x2="e.x2" :y2="e.y2" :marker-end="`url(#ra-${e.kind})`" />
        <circle :cx="e.bx" :cy="e.by" r="9" class="badge" />
        <text :x="e.bx" :y="e.by + 3.5" class="badge-n">{{ e.n }}</text>
        <text
          v-for="(l, i) in e.label" :key="i"
          :x="e.lx" :y="e.ly + i * 13" :text-anchor="e.anchor ?? 'start'" class="edge-label"
        >{{ l }}</text>
      </g>
    </g>

    <!-- harness loop: flowchart (stage 14) -->
    <g class="swap flow" :class="{ on: harnessView }">
      <rect x="195" y="36" width="170" height="40" rx="8" class="fl call" />
      <text x="280" y="61" class="fl-label">Call model</text>
      <line x1="280" y1="76" x2="280" y2="100" class="fl-arrow ret" marker-end="url(#ra-ret)" />
      <rect x="195" y="104" width="170" height="40" rx="8" class="fl call" />
      <text x="280" y="129" class="fl-label">Append response</text>
      <line x1="280" y1="144" x2="280" y2="150" class="fl-arrow ret" marker-end="url(#ra-ret)" />
      <path d="M280,154 L344,190 L280,226 L216,190 Z" class="fl decide" />
      <text x="280" y="186" class="fl-label small">Tool</text>
      <text x="280" y="201" class="fl-label small">calls?</text>

      <line x1="280" y1="226" x2="280" y2="252" class="fl-arrow ret" marker-end="url(#ra-ret)" />
      <text x="270" y="245" class="fl-yn" text-anchor="end">Yes</text>
      <rect x="195" y="256" width="170" height="40" rx="8" class="fl exec-t" />
      <text x="280" y="281" class="fl-label">Execute tools</text>
      <line x1="280" y1="296" x2="280" y2="320" class="fl-arrow obs" marker-end="url(#ra-obs)" />
      <rect x="195" y="324" width="170" height="40" rx="8" class="fl results" />
      <text x="280" y="349" class="fl-label">Append results</text>

      <path d="M195,344 L160,344 L160,56 L191,56" class="fl-arrow obs" marker-end="url(#ra-obs)" />
      <text x="150" y="200" class="fl-repeat" transform="rotate(-90 150 200)">Repeat</text>

      <path d="M344,190 L470,190 L470,316" class="fl-arrow ret" marker-end="url(#ra-ret)" />
      <text x="358" y="181" class="fl-yn">No</text>
      <rect x="395" y="320" width="150" height="44" rx="8" class="fl final" />
      <text x="470" y="347" class="fl-label">Return response</text>
    </g>

    <!-- context -->
    <g
      v-for="(s, i) in slots" :key="i"
      class="slot" :class="[s.kind, { on: props.stage >= s.stage, fresh: props.stage === s.stage }]"
    >
      <rect :x="SLOT_X" :y="slotY(i)" width="205" height="28" rx="6" class="slot-body" />
      <rect :x="SLOT_X + 4" :y="slotY(i) + 4" width="40" height="20" rx="4" class="slot-tag" />
      <text :x="SLOT_X + 24" :y="slotY(i) + 18" class="slot-tag-label">{{ s.tag }}</text>
      <text :x="SLOT_X + 54" :y="slotY(i) + 18" class="slot-label">{{ s.label }}</text>
    </g>

    <g class="rao" :class="{ on: props.stage >= 13 }">
      <path
        :d="`M872,${slotY(3) + 14} C904,${slotY(3) + 20} 904,${slotY(8) + 8} 872,${slotY(8) + 14}`"
        marker-start="url(#ra-think)" marker-end="url(#ra-think)"
      />
      <text x="926" :y="slotY(5) - 2">Reason ·</text>
      <text x="926" :y="slotY(5) + 12">Act ·</text>
      <text x="926" :y="slotY(5) + 26">Observe</text>
    </g>

    <text :x="SLOT_X" y="378" class="footnote">Dotted = conceptual reasoning; may not be exposed</text>

    <!-- caption strip -->
    <rect x="0" y="394" width="960" height="38" rx="10" class="caption-box" />
    <text v-if="props.stage >= 1 && props.stage <= 13" x="16" y="418" class="caption-step">{{ props.stage <= 12 ? props.stage : '↻' }}</text>
    <text :x="props.stage >= 1 && props.stage <= 13 ? 40 : 16" y="418" class="caption">{{ caption }}</text>
  </svg>
</template>

<style scoped>
.react {
  --purple: #b48cf2;
  --teal: #3fd0b5;
  width: 100%;
  height: auto;
  font-family: Inter, sans-serif;
}
.heading {
  fill: var(--ink);
  font-size: 13px;
  font-weight: 800;
  letter-spacing: 0.12em;
}
.divider {
  stroke: var(--border);
  stroke-width: 1.5;
  stroke-dasharray: 4 5;
}

/* execution <-> harness loop crossfade */
.swap {
  opacity: 0;
  transition: opacity 0.5s ease;
  pointer-events: none;
}
.swap.on {
  opacity: 1;
}

/* boxes */
.box rect {
  stroke-width: 2;
}
.box.app rect,
.box.api rect {
  fill: #16303f;
  stroke: var(--info);
}
.box.llm rect {
  fill: #261f3d;
  stroke: var(--purple);
}
.box.harness rect {
  fill: #3a2a1a;
  stroke: var(--warm);
}
.box.tool rect {
  fill: #123a33;
  stroke: var(--teal);
}
.box-title {
  fill: var(--ink);
  font-size: 19px;
  font-weight: 800;
  text-anchor: middle;
}
.reason {
  fill: none;
  stroke: var(--purple);
  stroke-width: 1.3;
  stroke-dasharray: 4 4;
}
.reason-label {
  fill: var(--ink-dim);
  font-size: 11px;
  text-anchor: middle;
}

/* edges */
.edge {
  --c: var(--info);
  transition: opacity 0.4s ease;
}
.edge.send {
  --c: var(--accent);
}
.edge.act {
  --c: var(--danger);
}
.edge.obs {
  --c: var(--teal);
}
.edge.off {
  opacity: 0;
}
.edge.past {
  opacity: 0.5;
}
.edge.current {
  opacity: 1;
}
.edge line {
  stroke: var(--c);
  stroke-width: 2;
}
.edge.current line {
  stroke-width: 2.6;
  filter: drop-shadow(0 0 4px var(--c));
}
.badge {
  fill: var(--c);
}
.badge-n {
  fill: var(--bg);
  font-size: 10px;
  font-weight: 800;
  text-anchor: middle;
}
.edge-label {
  fill: var(--c);
  font-size: 11.5px;
  font-weight: 600;
}
.head-send {
  fill: var(--accent);
}
.head-ret {
  fill: var(--info);
}
.head-act {
  fill: var(--danger);
}
.head-obs {
  fill: var(--teal);
}
.head-think {
  fill: var(--purple);
}

/* harness loop */
.fl {
  stroke-width: 1.8;
}
.fl.call {
  fill: #16303f;
  stroke: var(--info);
}
.fl.decide {
  fill: var(--bg-2);
  stroke: var(--ink-dim);
}
.fl.exec-t {
  fill: #2a1416;
  stroke: var(--danger);
}
.fl.results {
  fill: #123a33;
  stroke: var(--teal);
}
.fl.final {
  fill: var(--surface);
  stroke: var(--accent);
}
.fl-label {
  fill: var(--ink);
  font-size: 14px;
  font-weight: 700;
  text-anchor: middle;
}
.fl-label.small {
  font-size: 13px;
}
.fl-arrow {
  fill: none;
  stroke-width: 1.8;
}
.fl-arrow.ret {
  stroke: var(--info);
}
.fl-arrow.obs {
  stroke: var(--teal);
}
.fl-yn {
  fill: var(--info);
  font-size: 13px;
  font-weight: 700;
}
.fl-repeat {
  fill: var(--teal);
  font-size: 13px;
  font-weight: 700;
  text-anchor: middle;
}

/* context slots */
.slot {
  --c: var(--info);
  --fill: #16303f;
}
.slot.think {
  --c: var(--purple);
  --fill: #261f3d;
}
.slot.act {
  --c: var(--danger);
  --fill: #3a1a1c;
}
.slot.obs {
  --c: var(--teal);
  --fill: #123a33;
}
.slot.final {
  --c: var(--accent);
  --fill: var(--surface);
}
.slot-body {
  fill: transparent;
  stroke: var(--ink-faint);
  stroke-width: 1;
  stroke-dasharray: 3 4;
  opacity: 0.5;
  transition: all 0.4s ease;
}
.slot-tag,
.slot-tag-label,
.slot-label {
  opacity: 0;
  transition: opacity 0.4s ease;
}
.slot.on .slot-body {
  fill: var(--fill);
  stroke: var(--c);
  stroke-width: 1.6;
  stroke-dasharray: none;
  opacity: 1;
}
.slot.think.on .slot-body {
  stroke-dasharray: 4 3;
}
.slot.on .slot-tag,
.slot.on .slot-tag-label,
.slot.on .slot-label {
  opacity: 1;
}
.slot.fresh .slot-body {
  filter: drop-shadow(0 0 6px var(--c));
}
.slot-tag {
  fill: var(--c);
  fill-opacity: 0.25;
}
.slot-tag-label {
  fill: var(--c);
  font-size: 11px;
  font-weight: 800;
  text-anchor: middle;
}
.slot-label {
  fill: var(--ink);
  font-size: 12.5px;
}
.slot.final .slot-label {
  font-weight: 800;
}
.rao {
  opacity: 0;
  transition: opacity 0.4s ease;
}
.rao.on {
  opacity: 1;
}
.rao path {
  fill: none;
  stroke: var(--purple);
  stroke-width: 1.8;
}
.rao text {
  fill: var(--purple);
  font-size: 11px;
  font-weight: 700;
  text-anchor: middle;
}
.footnote {
  fill: var(--ink-faint);
  font-size: 10px;
}

/* caption strip */
.caption-box {
  fill: var(--bg-2);
  stroke: var(--border);
  stroke-width: 1;
}
.caption-step {
  fill: var(--accent);
  font-family: var(--mono);
  font-size: 13px;
  font-weight: 800;
}
.caption {
  fill: var(--ink);
  font-size: 14px;
}
</style>
