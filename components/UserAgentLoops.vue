<script setup lang="ts">
import { computed } from "vue";

// The user's own reason/act/observe loop, with the in-app agent's loop running
// inside one of its steps. One step per click. Left: the real world (the
// fridge). Centre: the user and their internal monologue. Right: the agent.
// Colours match ReActAgent: reason purple, act red, observe teal, answer lime.
const props = withDefaults(defineProps<{ stage?: number }>(), { stage: 99 });

type Phase =
  | "trigger"
  | "reason"
  | "act"
  | "obs"
  | "final"
  | "meanwhile"
  | "idle";
interface Say {
  phase: Phase;
  lines: string[];
  mono?: boolean;
}

const tags: Record<Phase, string> = {
  trigger: "TRIGGER",
  reason: "REASON",
  act: "ACT",
  obs: "OBSERVE",
  final: "FINISH",
  meanwhile: "MEANWHILE",
  idle: "",
};

const waitingSlip: Say = {
  phase: "meanwhile",
  lines: ['"...did I sign that permission slip?"'],
};
const waitingDog: Say = {
  phase: "meanwhile",
  lines: ['"...is the dog fed?"'],
};

// Index = click. Each entry is what the user is reasoning or doing at that point.
const user: Say[] = [
  { phase: "idle", lines: ["A lot going on."] },
  {
    phase: "trigger",
    lines: ['"I need to eat more vegetables.', 'Carrots are nice."'],
  },
  {
    phase: "reason",
    lines: ['"Not sure I have carrots in the fridge.', 'Better check."'],
  },
  { phase: "act", lines: ["Opens the fridge."] },
  {
    phase: "obs",
    lines: ["Looks for carrots. No carrots,", "just eggplant and milk."],
  },
  { phase: "reason", lines: ['"Better buy some."'] },
  { phase: "act", lines: ['"Hey Recipes, add carrots to the list."'] },
  waitingSlip,
  waitingSlip,
  waitingSlip,
  waitingSlip,
  {
    phase: "obs",
    lines: ['"Oh, there are two kinds of carrots', 'in my favourites."'],
  },
  {
    phase: "reason",
    lines: ['"I prefer the baby carrots.', 'They\'re tender."'],
  },
  { phase: "act", lines: ['"The baby carrots."'] },
  waitingDog,
  waitingDog,
  waitingDog,
  waitingDog,
  { phase: "obs", lines: ['"Carrots are on the list."'] },
  { phase: "reason", lines: ['"Now I need some carrot recipes..."'] },
];

// Index = click. The agent only exists in the user's loop from click 6, and
// runs two short loops: one ends in a question, the other in the result.
const agent: (Say | null)[] = [
  null,
  null,
  null,
  null,
  null,
  null,
  { phase: "trigger", lines: ["A request from the user."] },
  {
    phase: "reason",
    lines: [
      "The user wants carrots on the list.",
      "There's a tool that adds an item",
      "by name.",
    ],
  },
  { phase: "act", lines: ['addShoppingListItem("carrots")'], mono: true },
  {
    phase: "obs",
    lines: ["Two matching products in the", "user's favourites."],
  },
  {
    phase: "reason",
    lines: ["No way of knowing which one.", "Better ask the user."],
  },
  {
    phase: "final",
    lines: ['"There are two carrots in your', 'favourites. Which should I add?"'],
  },
  null,
  { phase: "obs", lines: ["The user picked the baby carrots."] },
  {
    phase: "reason",
    lines: ["There's a tool to add a specific", "product by id."],
  },
  { phase: "act", lines: ["addProductToCartById(4523)"], mono: true },
  { phase: "obs", lines: ["Returned true."] },
  {
    phase: "reason",
    lines: ["Tool call is good, looks like", "we are done."],
  },
  { phase: "final", lines: ['"Carrots have been added', 'to the list."'] },
  { phase: "idle", lines: ["Done. Back to waiting for the user."] },
];

const captions = [
  "Meet our hero: the user. Busy, multitasking, with a lot on their mind.",
  "Trigger: something starts the user's own loop.",
  "Reason: the internal monologue.",
  "Act: in the real world.",
  "Observe: no carrots. Update the plan.",
  "Reason: time to add to the shopping list. This is where the app can help.",
  "Act: the user asks the in-app agent. That's the trigger for the agent's loop.",
  "The agent reasons: it knows which of the app's tools fits the request.",
  "The agent acts: it calls one of the app's own tools.",
  "The agent observes: two products match.",
  "The agent reasons: it can't know which one the user wants, so it doesn't guess.",
  "Finish: the agent asks. Its question becomes an observation in the user's loop.",
  "The user reasons with something only they know: their own preference.",
  "Act: the user answers, which starts a second short agent loop.",
  "The agent reasons: now there's a specific product, and a tool for that.",
  "The agent acts: it adds the exact product by id.",
  "The agent observes: success.",
  "The agent reasons: the task is complete, so it can wrap up.",
  "Finish: the result lands back in the user's loop as an observation.",
  "The user runs the big loop and moves on. The agent ran two short loops inside it.",
];

const LAST = captions.length - 1;
const s = computed(() => Math.max(0, Math.min(props.stage, LAST)));
const u = computed(() => user[s.value]);
const a = computed(() => agent[s.value]);
const caption = computed(() => captions[s.value]);

// Everything else on the user's mind: always there, never the agent's business.
const noise = [
  { text: "school pickup 3:15", x: 238, y: 40 },
  { text: "reply to work email", x: 470, y: 36 },
  { text: "is the dog fed?", x: 548, y: 88 },
  { text: "permission slip!", x: 236, y: 92 },
  { text: "dentist Tuesday?", x: 236, y: 250 },
];
const pillW = (t: string) => t.length * 6.1 + 18;

// Reason/act/observe ring: three badges clockwise from the top.
interface Ring {
  cx: number;
  cy: number;
  r: number;
}
const userRing: Ring = { cx: 430, cy: 172, r: 72 };
const agentRing: Ring = { cx: 815, cy: 150, r: 50 };
const stations = [
  { phase: "reason" as Phase, label: "REASON", deg: -90 },
  { phase: "act" as Phase, label: "ACT", deg: 30 },
  { phase: "obs" as Phase, label: "OBSERVE", deg: 150 },
];
const rad = (d: number) => (d * Math.PI) / 180;
const at = (g: Ring, deg: number) => ({
  x: g.cx + g.r * Math.cos(rad(deg)),
  y: g.cy + g.r * Math.sin(rad(deg)),
});
function arc(g: Ring, from: number, to: number) {
  const p = at(g, from);
  const q = at(g, to);
  return `M${p.x},${p.y} A${g.r},${g.r} 0 0 1 ${q.x},${q.y}`;
}
const arcs = (g: Ring, gap: number) =>
  stations.map((st) => arc(g, st.deg + gap, st.deg + 120 - gap));

// Arrows between the three zones. A link can fire on several clicks; its
// label follows the latest one.
interface Link {
  steps: { n: number; label: string }[];
  kind: string;
  d: string;
  lx: number;
  ly: number;
}
const links: Link[] = [
  {
    steps: [{ n: 3, label: "open the fridge" }],
    kind: "act",
    d: "M356,156 L190,156",
    lx: 273,
    ly: 148,
  },
  {
    steps: [{ n: 4, label: "look for carrots" }],
    kind: "obs",
    d: "M190,200 L328,200",
    lx: 262,
    ly: 216,
  },
  {
    steps: [
      { n: 6, label: '"Hey Recipes..."' },
      { n: 13, label: '"the baby carrots"' },
    ],
    kind: "act",
    d: "M504,160 L744,160",
    lx: 586,
    ly: 152,
  },
  {
    steps: [
      { n: 11, label: "a question" },
      { n: 18, label: "done" },
    ],
    kind: "final",
    d: "M684,236 L480,236",
    lx: 586,
    ly: 252,
  },
];
const linkStep = (l: Link) =>
  [...l.steps].reverse().find((st) => st.n <= s.value);
function linkState(l: Link) {
  const st = linkStep(l);
  if (!st) return "off";
  return st.n === s.value ? "current" : "past";
}
const live = computed(() => !!a.value && a.value.phase !== "idle");
</script>

<template>
  <svg viewBox="0 0 960 436" class="loops">
    <defs>
      <marker
        v-for="k in ['act', 'obs', 'final', 'user', 'agent']"
        :id="`ual-${k}`"
        :key="k"
        viewBox="0 0 10 10"
        refX="8"
        refY="5"
        markerWidth="6"
        markerHeight="6"
        orient="auto-start-reverse"
      >
        <path d="M0,0 L10,5 L0,10 z" :class="`head-${k}`" />
      </marker>
    </defs>

    <!-- headings -->
    <text x="0" y="16" class="heading">THE REAL WORLD</text>
    <text x="430" y="16" class="heading" text-anchor="middle">THE USER</text>
    <text x="684" y="16" class="heading">THE APP</text>

    <!-- the real world: a fridge with no carrots in it -->
    <g class="fridge" :class="{ open: s >= 3, looked: s === 4 }">
      <rect x="40" y="70" width="140" height="230" rx="12" class="body" />
      <line x1="40" y1="130" x2="180" y2="130" class="shelf" />
      <line x1="40" y1="200" x2="180" y2="200" class="shelf inner" />
      <g class="food">
        <g v-for="(e, i) in [[78, 182], [122, 180], [76, 270]]" :key="i">
          <ellipse
            :cx="e[0]"
            :cy="e[1]"
            rx="15"
            ry="9"
            :transform="`rotate(-18 ${e[0]} ${e[1]})`"
            class="egg"
          />
          <path :d="`M${e[0] - 16},${e[1] + 2} l-6,2 l3,-6 z`" class="cap" />
        </g>
        <path d="M122,238 L134,226 L146,238 Z" class="milk" />
        <rect x="122" y="238" width="24" height="46" rx="2" class="milk" />
        <rect x="122" y="252" width="24" height="12" class="milk-band" />
      </g>
      <text x="110" y="294" class="no-carrots">no carrots</text>
      <rect x="40" y="130" width="140" height="170" rx="0" class="door" />
      <line x1="164" y1="150" x2="164" y2="190" class="handle door-part" />
      <line x1="164" y1="90" x2="164" y2="116" class="handle" />
      <rect x="18" y="130" width="22" height="170" rx="4" class="door-open" />
    </g>

    <!-- the user's mental load -->
    <g v-for="p in noise" :key="p.text" class="noise">
      <rect :x="p.x" :y="p.y - 13" :width="pillW(p.text)" height="20" rx="10" />
      <text :x="p.x + 9" :y="p.y + 1">{{ p.text }}</text>
    </g>

    <!-- the user and their loop -->
    <g class="ring user-ring">
      <path
        v-for="(d, i) in arcs(userRing, 16)"
        :key="i"
        :d="d"
        marker-end="url(#ual-user)"
      />
    </g>
    <g class="person">
      <circle cx="430" cy="152" r="17" />
      <path d="M398,206 a32,30 0 0 1 64,0 z" />
    </g>
    <g
      v-for="st in stations"
      :key="`u-${st.phase}`"
      class="badge"
      :class="[st.phase, { on: u.phase === st.phase }]"
    >
      <rect
        :x="at(userRing, st.deg).x - 34"
        :y="at(userRing, st.deg).y - 10"
        width="68"
        height="20"
        rx="10"
      />
      <text :x="at(userRing, st.deg).x" :y="at(userRing, st.deg).y + 4">
        {{ st.label }}
      </text>
    </g>
    <g class="badge trigger" :class="{ on: s === 1, shown: s >= 1 }">
      <rect x="296" y="116" width="72" height="20" rx="10" />
      <text x="332" y="130">TRIGGER</text>
    </g>

    <!-- internal monologue -->
    <rect x="240" y="286" width="380" height="92" rx="12" class="mono-box" />
    <text x="256" y="306" class="box-label">INTERNAL MONOLOGUE</text>
    <g :key="`u${s}`" class="say appear" :class="u.phase">
      <text
        v-if="tags[u.phase]"
        x="604"
        y="306"
        class="say-tag"
        text-anchor="end"
      >
        {{ tags[u.phase] }}
      </text>
      <text
        v-for="(l, i) in u.lines"
        :key="i"
        x="256"
        :y="332 + i * 20"
        class="say-line user-line"
      >
        {{ l }}
      </text>
    </g>

    <!-- the app and its agent -->
    <g class="app" :class="{ live }">
      <rect x="670" y="26" width="290" height="352" rx="16" class="app-frame" />
      <text x="686" y="50" class="app-title">Recipes4Me agent</text>
      <rect x="686" y="60" width="96" height="20" rx="10" class="expert" />
      <text x="734" y="74" class="expert-label">recipe expert</text>

      <g class="ring agent-ring">
        <path
          v-for="(d, i) in arcs(agentRing, 20)"
          :key="i"
          :d="d"
          marker-end="url(#ual-agent)"
        />
      </g>
      <text x="815" y="155" class="llm">LLM</text>
      <g
        v-for="st in stations"
        :key="`a-${st.phase}`"
        class="badge small"
        :class="[st.phase, { on: a?.phase === st.phase }]"
      >
        <rect
          :x="at(agentRing, st.deg).x - 30"
          :y="at(agentRing, st.deg).y - 9"
          width="60"
          height="18"
          rx="9"
        />
        <text :x="at(agentRing, st.deg).x" :y="at(agentRing, st.deg).y + 3.5">
          {{ st.label }}
        </text>
      </g>

      <rect x="684" y="226" width="262" height="140" rx="10" class="log-box" />
      <text x="698" y="246" class="box-label">AGENT LOOP</text>
      <g v-if="a" :key="`a${s}`" class="say appear" :class="a.phase">
        <text
          v-if="tags[a.phase]"
          x="932"
          y="246"
          class="say-tag"
          text-anchor="end"
        >
          {{ tags[a.phase] }}
        </text>
        <text
          v-for="(l, i) in a.lines"
          :key="i"
          x="698"
          :y="274 + i * 19"
          class="say-line agent-line"
          :class="{ code: a.mono }"
        >
          {{ l }}
        </text>
      </g>
      <text v-else x="698" y="274" class="say-line agent-line idle">
        Waiting for the user.
      </text>
    </g>

    <!-- links between the zones -->
    <g
      v-for="l in links"
      :key="l.d"
      class="link"
      :class="[l.kind, linkState(l)]"
    >
      <path :d="l.d" :marker-end="`url(#ual-${l.kind})`" />
      <text :x="l.lx" :y="l.ly" text-anchor="middle">
        {{ linkStep(l)?.label }}
      </text>
    </g>

    <!-- caption strip -->
    <rect x="0" y="394" width="960" height="38" rx="10" class="caption-box" />
    <text x="16" y="418" class="caption">{{ caption }}</text>
  </svg>
</template>

<style scoped>
.loops {
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

/* fridge */
.fridge .body {
  fill: #1a2c34;
  stroke: var(--ink-dim);
  stroke-width: 2;
}
.fridge .shelf {
  stroke: var(--ink-dim);
  stroke-width: 2;
}
.fridge .shelf.inner {
  stroke: var(--border);
  stroke-width: 1.5;
}
.fridge .handle {
  stroke: var(--ink-dim);
  stroke-width: 4;
  stroke-linecap: round;
}
.fridge .door {
  fill: #22363f;
  stroke: var(--ink-dim);
  stroke-width: 2;
  transition: opacity 0.5s ease;
}
.fridge .door-part {
  transition: opacity 0.5s ease;
}
.fridge .door-open {
  fill: #22363f;
  stroke: var(--ink-dim);
  stroke-width: 2;
  opacity: 0;
  transition: opacity 0.5s ease;
}
.fridge.open .door,
.fridge.open .door-part {
  opacity: 0;
}
.fridge.open .door-open {
  opacity: 1;
}
.egg {
  fill: #6b3fa0;
  stroke: #8d5ec4;
  stroke-width: 1;
}
.cap {
  fill: #5f9e3a;
}
.milk {
  fill: #e8eef0;
  stroke: #b9c6cc;
  stroke-width: 1;
}
.milk-band {
  fill: var(--info);
}
.food {
  transition: filter 0.4s ease;
}
.fridge.looked .food {
  filter: drop-shadow(0 0 6px var(--teal));
}
.no-carrots {
  fill: var(--teal);
  font-size: 11px;
  font-weight: 700;
  text-anchor: middle;
  opacity: 0;
  transition: opacity 0.4s ease;
}
.fridge.looked .no-carrots {
  opacity: 1;
}

/* mental load */
.noise {
  animation: drift 6s ease-in-out infinite alternate;
}
.noise:nth-of-type(2n) {
  animation-duration: 7.5s;
  animation-delay: -2s;
}
.noise rect {
  fill: var(--bg-2);
  stroke: var(--ink-faint);
  stroke-width: 1;
  stroke-dasharray: 3 3;
}
.noise text {
  fill: var(--ink-faint);
  font-size: 11px;
  font-style: italic;
}
@keyframes drift {
  to {
    transform: translateY(-4px);
  }
}

/* loops */
.ring path {
  fill: none;
  stroke-width: 2;
}
.user-ring path {
  stroke: var(--warm);
}
.agent-ring path {
  stroke: var(--purple);
  opacity: 0.6;
}
.app.live .agent-ring path {
  opacity: 1;
}
.head-user {
  fill: var(--warm);
}
.head-agent {
  fill: var(--purple);
}
.head-act {
  fill: var(--danger);
}
.head-obs {
  fill: var(--teal);
}
.head-final {
  fill: var(--accent);
}
.person circle,
.person path {
  fill: var(--warm);
}
.llm {
  fill: var(--purple);
  font-size: 15px;
  font-weight: 800;
  text-anchor: middle;
}

/* phase badges */
.badge {
  --c: var(--info);
}
.badge.reason {
  --c: var(--purple);
}
.badge.act {
  --c: var(--danger);
}
.badge.obs {
  --c: var(--teal);
}
.badge rect {
  fill: var(--bg);
  stroke: var(--c);
  stroke-width: 1.3;
  opacity: 0.55;
  transition: all 0.35s ease;
}
.badge text {
  fill: var(--c);
  font-size: 10px;
  font-weight: 800;
  letter-spacing: 0.08em;
  text-anchor: middle;
  opacity: 0.55;
  transition: opacity 0.35s ease;
}
.badge.small text {
  font-size: 9px;
}
.badge.on rect {
  fill: var(--c);
  opacity: 1;
  filter: drop-shadow(0 0 6px var(--c));
}
.badge.on text {
  fill: var(--bg);
  opacity: 1;
}
.badge.trigger {
  opacity: 0;
  transition: opacity 0.35s ease;
}
.badge.trigger.shown {
  opacity: 1;
}

/* monologue + agent log */
.mono-box,
.log-box {
  fill: #0a1612;
  stroke: var(--border);
  stroke-width: 1.5;
}
.box-label {
  fill: var(--ink-faint);
  font-family: var(--mono);
  font-size: 9.5px;
  letter-spacing: 0.14em;
}
.say {
  --c: var(--ink);
}
.say.trigger {
  --c: var(--info);
}
.say.reason {
  --c: var(--purple);
}
.say.act {
  --c: var(--danger);
}
.say.obs {
  --c: var(--teal);
}
.say.final {
  --c: var(--accent);
}
.say.meanwhile {
  --c: var(--warm);
}
.say-tag {
  fill: var(--c);
  font-family: var(--mono);
  font-size: 10px;
  font-weight: 800;
  letter-spacing: 0.12em;
}
.say-line {
  fill: var(--ink);
  white-space: pre;
}
.user-line {
  font-size: 15px;
  font-style: italic;
}
.say.meanwhile .user-line,
.say.idle .user-line {
  fill: var(--ink-dim);
}
.agent-line {
  font-size: 12.5px;
}
.agent-line.code {
  fill: var(--danger);
  font-family: var(--mono);
  font-size: 12px;
}
.say.final .agent-line {
  fill: var(--accent);
  font-weight: 600;
}
.agent-line.idle,
.say.idle .agent-line {
  fill: var(--ink-faint);
  font-style: italic;
}

/* app frame */
.app-frame {
  fill: var(--bg-2);
  stroke: var(--border);
  stroke-width: 1.5;
  transition: all 0.4s ease;
}
.app.live .app-frame {
  stroke: var(--purple);
  filter: drop-shadow(0 0 8px rgba(180, 140, 242, 0.35));
}
.app-title {
  fill: var(--ink);
  font-size: 15px;
  font-weight: 800;
}
.expert {
  fill: rgba(181, 227, 107, 0.12);
  stroke: var(--accent-dim);
  stroke-width: 1;
}
.expert-label {
  fill: var(--accent);
  font-family: var(--mono);
  font-size: 10.5px;
  text-anchor: middle;
}

/* links */
.link {
  --c: var(--danger);
  transition: opacity 0.4s ease;
}
.link.obs {
  --c: var(--teal);
}
.link.final {
  --c: var(--accent);
}
.link.off {
  opacity: 0;
}
.link.past {
  opacity: 0.45;
}
.link path {
  fill: none;
  stroke: var(--c);
  stroke-width: 2;
}
.link.current path {
  stroke-width: 2.6;
  filter: drop-shadow(0 0 4px var(--c));
}
.link text {
  fill: var(--c);
  font-size: 11.5px;
  font-weight: 600;
}

/* caption strip */
.caption-box {
  fill: var(--bg-2);
  stroke: var(--border);
  stroke-width: 1;
}
.caption {
  fill: var(--ink);
  font-size: 14px;
}

.appear {
  animation: appear 0.35s ease both;
}
@keyframes appear {
  from {
    opacity: 0;
  }
}
</style>
