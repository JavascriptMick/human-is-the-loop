<script setup lang="ts">
import { computed } from 'vue'

// The agent loop as a ring of four nodes around a hub, drawn in two variants:
//  autonomous    - the LLM sits in the middle, the human waits outside
//  human-centred - the user sits in the middle, the app is the boundary
const props = withDefaults(
  defineProps<{
    variant?: 'autonomous' | 'human-centred'
    stage?: number
  }>(),
  { variant: 'autonomous', stage: 99 },
)

const CX = 300
const CY = 215
const R = 150

const autonomous = computed(() => props.variant === 'autonomous')

const nodes = computed(() =>
  autonomous.value
    ? ['Plan', 'Act', 'Observe', 'Reflect']
    : ['Listen', 'Understand intent', 'Act via app tools', 'Show & speak'],
)

function pos(i: number) {
  const a = (i / 4) * Math.PI * 2 - Math.PI / 2
  return { x: CX + R * Math.cos(a), y: CY + R * Math.sin(a) }
}

// Things an autonomous agent reaches for once it runs out of road.
const sprawl = [
  { label: 'shell', x: 560, y: 40 },
  { label: 'browser', x: 590, y: 120 },
  { label: 'web search', x: 575, y: 330 },
  { label: 'file system', x: 90, y: 370 },
  { label: 'network', x: 80, y: 50 },
  { label: 'sub-agents x 1000', x: 330, y: 415 },
]
</script>

<template>
  <svg viewBox="0 0 640 440" class="loop" :class="props.variant">
    <defs>
      <marker id="arrow" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
        <path d="M0,0 L10,5 L0,10 z" class="arrow-head" />
      </marker>
    </defs>

    <!-- app boundary (human-centred only) -->
    <rect
      v-if="!autonomous"
      x="70" y="18" width="460" height="394" rx="36"
      class="boundary" :class="{ on: props.stage >= 3 }"
    />
    <text v-if="!autonomous" x="92" y="46" class="boundary-label" :class="{ on: props.stage >= 3 }">
      THE APP
    </text>

    <!-- ring -->
    <circle :cx="CX" :cy="CY" :r="R" class="ring" />

    <!-- travelling pulse -->
    <g v-if="props.stage >= 1" class="spinner" :style="{ transformOrigin: `${CX}px ${CY}px` }">
      <circle :cx="CX" :cy="CY - R" r="9" class="pulse" />
    </g>

    <!-- nodes -->
    <g v-for="(n, i) in nodes" :key="n">
      <rect
        :x="pos(i).x - 72" :y="pos(i).y - 20" width="144" height="40" rx="20"
        class="node"
      />
      <text :x="pos(i).x" :y="pos(i).y + 5" class="node-label">{{ n }}</text>
    </g>

    <!-- hub -->
    <circle :cx="CX" :cy="CY" r="52" class="hub" />
    <text v-if="autonomous" :x="CX" :y="CY + 6" class="hub-label">LLM</text>
    <g v-else>
      <text :x="CX" :y="CY - 2" class="hub-label">USER</text>
      <text :x="CX" :y="CY + 18" class="hub-sub">attention + intent</text>
    </g>

    <!-- autonomous: the human is outside, approving -->
    <g v-if="autonomous" class="outsider" :class="{ on: props.stage >= 2 }">
      <circle cx="560" cy="230" r="26" class="human" />
      <text x="560" y="236" class="human-label">you</text>
      <rect x="505" y="268" width="110" height="30" rx="8" class="approve" />
      <text x="560" y="288" class="approve-label">☐ Approve?</text>
      <line x1="534" y1="230" x2="460" y2="222" class="dashed" marker-end="url(#arrow)" />
    </g>

    <!-- autonomous: tools sprawl outwards -->
    <g v-if="autonomous">
      <g
        v-for="(s, i) in sprawl" :key="s.label"
        class="sprawl" :class="{ on: props.stage >= 3 }"
        :style="{ transitionDelay: `${i * 90}ms` }"
      >
        <line :x1="CX" :y1="CY" :x2="s.x" :y2="s.y" class="sprawl-line" />
        <rect :x="s.x - 55" :y="s.y - 14" width="110" height="28" rx="6" class="sprawl-box" />
        <text :x="s.x" :y="s.y + 5" class="sprawl-label">{{ s.label }}</text>
      </g>
    </g>

    <!-- human-centred: the user can see the app state -->
    <g v-if="!autonomous" class="outsider" :class="{ on: props.stage >= 2 }">
      <text :x="CX" :y="CY + 92" class="caption">spins <tspan class="accent-fill">alongside</tspan> the user,</text>
      <text :x="CX" :y="CY + 110" class="caption">not towards a prompt</text>
    </g>
  </svg>
</template>

<style scoped>
.loop {
  width: 100%;
  height: auto;
  font-family: Inter, sans-serif;
}
.ring {
  fill: none;
  stroke: var(--border);
  stroke-width: 3;
  stroke-dasharray: 6 8;
}
.spinner {
  animation: spin 3.2s linear infinite;
}
.autonomous .spinner {
  animation-duration: 1.4s;
}
@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}
.pulse {
  fill: var(--accent);
  filter: drop-shadow(0 0 8px var(--accent));
}
.autonomous .pulse {
  fill: var(--danger);
  filter: drop-shadow(0 0 8px var(--danger));
}
.node {
  fill: var(--surface);
  stroke: var(--border);
  stroke-width: 1.5;
}
.node-label {
  fill: var(--ink);
  font-size: 14px;
  font-weight: 600;
  text-anchor: middle;
}
.hub {
  fill: var(--bg-2);
  stroke: var(--accent);
  stroke-width: 2.5;
}
.autonomous .hub {
  stroke: var(--danger);
}
.hub-label {
  fill: var(--ink);
  font-size: 20px;
  font-weight: 800;
  text-anchor: middle;
  letter-spacing: 0.08em;
}
.hub-sub {
  fill: var(--ink-dim);
  font-size: 10px;
  text-anchor: middle;
}
.outsider,
.sprawl {
  opacity: 0;
  transition: opacity 0.4s ease;
}
.outsider.on,
.sprawl.on {
  opacity: 1;
}
.human {
  fill: var(--bg-2);
  stroke: var(--ink-dim);
  stroke-width: 2;
}
.human-label {
  fill: var(--ink-dim);
  font-size: 13px;
  text-anchor: middle;
}
.approve {
  fill: #f4f1ea;
}
.approve-label {
  fill: #1a1a1a;
  font-size: 13px;
  font-weight: 600;
  text-anchor: middle;
}
.dashed {
  stroke: var(--ink-faint);
  stroke-width: 1.5;
  stroke-dasharray: 4 4;
}
.arrow-head {
  fill: var(--ink-faint);
}
.sprawl-line {
  stroke: var(--danger);
  stroke-width: 1.2;
  stroke-dasharray: 3 5;
  opacity: 0.6;
}
.sprawl-box {
  fill: #2a1416;
  stroke: var(--danger);
  stroke-width: 1.2;
}
.sprawl-label {
  fill: #ffb4b6;
  font-family: var(--mono);
  font-size: 11px;
  text-anchor: middle;
}
.boundary {
  fill: rgba(181, 227, 107, 0.03);
  stroke: var(--ink-faint);
  stroke-width: 2;
  transition: all 0.5s ease;
}
.boundary.on {
  stroke: var(--accent);
}
.boundary-label {
  fill: var(--ink-faint);
  font-family: var(--mono);
  font-size: 11px;
  letter-spacing: 0.2em;
  transition: fill 0.5s ease;
}
.boundary-label.on {
  fill: var(--accent);
}
.caption {
  fill: var(--ink-dim);
  font-size: 13px;
  text-anchor: middle;
}
.accent-fill {
  fill: var(--accent);
  font-weight: 700;
}
</style>
