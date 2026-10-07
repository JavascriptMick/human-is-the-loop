<script setup lang="ts">
import { computed } from "vue";

// The user's own reason/act/observe loop, with the in-app agent's loop running
// inside one of its steps. One step per click. Left: the real world (the
// fridge). Centre: the user, with a chat of what they think, say and do, and
// what the agent says back. Right: the agent.
// Colours match ReActAgent: reason purple, act red, observe teal, answer lime.
// `compact` is the thumbnail used on the code slides: it crops away the real
// world and the caption so the two loops fill the frame.
// `hideAgentWorking` covers the app with a black box and a cog, so the agent
// is a magic box: the cog spins while the agent is working, and the captions
// for the agent's own steps are replaced.
const props = withDefaults(
  defineProps<{
    stage?: number;
    compact?: boolean;
    hideAgentWorking?: boolean;
  }>(),
  { stage: 99, compact: false, hideAgentWorking: false },
);

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

// The user's mind is elsewhere while the agent works: nothing goes in the chat.
const waiting: Say = { phase: "meanwhile", lines: [] };

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
  waiting,
  waiting,
  waiting,
  waiting,
  waiting,
  {
    phase: "obs",
    lines: ['"Oh, there are two kinds of carrots', 'in my favourites."'],
  },
  {
    phase: "reason",
    lines: ['"I prefer the baby carrots.', "They're tender.\""],
  },
  { phase: "act", lines: ['"The baby carrots."'] },
  waiting,
  waiting,
  waiting,
  waiting,
  waiting,
  { phase: "obs", lines: ['"Carrots are on the list."'] },
  { phase: "reason", lines: ['"Now I need some carrot recipes..."'] },
  {
    phase: "act",
    lines: [
      '"Hey Recipes, find me some good',
      'carrot recipes for next week."',
    ],
  },
  waiting,
  waiting,
  waiting,
  waiting,
  waiting,
  waiting,
  waiting,
  waiting,
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
    lines: [
      "Two matching products in the",
      "user's favourites: Carrots 1kg",
      "and Baby Carrots 500g.",
    ],
  },
  {
    phase: "reason",
    lines: ["No way of knowing which one.", "Better ask the user."],
  },
  {
    phase: "final",
    lines: [
      '"Two carrots in your favourites:',
      "Carrots 1kg and Baby Carrots",
      '500g. Which should I add?"',
    ],
  },
  null,
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
  { phase: "final", lines: ['"Baby carrots have been added', 'to the list."'] },
  null,
  { phase: "idle", lines: ["Done. Back to waiting for the user."] },
  { phase: "trigger", lines: ["A request from the user."] },
  {
    phase: "reason",
    lines: [
      "The user wants recipes. No tool for",
      "that here, but I can start the",
      "recipe planning process.",
    ],
  },
  { phase: "act", lines: ["startPlanning()"], mono: true },
  { phase: "obs", lines: ["startPlanning returned OK."] },
  { phase: "reason", lines: ["I have a tool for recipes."] },
  {
    phase: "act",
    lines: ["searchForRecipeByIngredient(", '  "Carrots")'],
    mono: true,
  },
  { phase: "obs", lines: ["There are two recipes."] },
  {
    phase: "reason",
    lines: ["Not clear which one to add.", "Better ask the user."],
  },
  {
    phase: "final",
    lines: ['"I found carrot soup', 'and carrot pie."'],
  },
];

const captions = [
  "The Human brain runs it's own cognitive loop",
  "Trigger: Health and hunger",
  "Reason: the internal monologue.",
  "Act: in the real world.",
  "Observe: no carrots. Update the plan.",
  "Reason: time to add to the shopping list. This is where the app can help.",
  "Act: the user asks the in-app agent. That's the trigger for the agent's loop.",
  "The agent reasons: it knows which of the app's tools fits the request.",
  "The agent acts: it calls one of the app's own tools.",
  "The agent observes: two products match, regular carrots and baby carrots.",
  "The agent reasons: it can't know which one the user wants, so it doesn't guess.",
  "Finish: the agent can't go on without the user, so it asks.",
  "Observe: the agent's question becomes an observation in the user's loop.",
  "The user reasons with something only they know: their own preference.",
  "Act: the user answers, which starts a second short agent loop.",
  "The agent reasons: now there's a specific product, and a tool for that.",
  "The agent acts: it adds the exact product by id.",
  "The agent observes: success.",
  "The agent reasons: the task is complete, so it can wrap up.",
  "Finish: the agent reports back.",
  "Observe: the result lands back in the user's loop.",
  "The user runs the big loop and moves on. The agent ran two short loops inside it.",
  "Act: a new request, and a new intent. Not shopping this time, meal planning.",
  "The agent reasons: no armed tool fits, but a global tool starts the planning flow.",
  "The agent acts: it starts the meal planning flow.",
  "The agent observes: planning has started, and its tools are armed.",
  "The agent reasons: there's now a tool to search recipes.",
  "The agent acts: it searches for carrot recipes.",
  "The agent observes: two recipes match.",
  "The agent reasons: it can't know which one the user wants, so it asks.",
  "Finish: the agent asks the user to choose.",
];

const LAST = captions.length - 1;
const s = computed(() => Math.max(0, Math.min(props.stage, LAST)));
const u = computed(() => user[s.value]);
const a = computed(() => agent[s.value]);
// How many steps the agent has taken in its current run: every reason, act
// and observe since it was last triggered.
const agentStep = computed(() => {
  let n = 0;
  for (let i = s.value; i >= 0; i--) {
    const step = agent[i];
    if (!step || step.phase === "idle" || step.phase === "final") break;
    if (step.phase !== "trigger") n++;
  }
  return n;
});

// While the user's mind is elsewhere the agent is mid-loop; with the agent
// hidden, its step-by-step captions would describe what nobody can see.
const caption = computed(() =>
  props.hideAgentWorking &&
  u.value.phase === "meanwhile" &&
  a.value?.phase !== "final"
    ? `The agent is working... step ${agentStep.value}`
    : captions[s.value],
);

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
  world?: boolean;
  kind: string;
  d: string;
  lx: number;
  ly: number;
}
const links: Link[] = [
  {
    steps: [{ n: 3, label: "open the fridge" }],
    world: true,
    kind: "act",
    d: "M356,156 L190,156",
    lx: 273,
    ly: 148,
  },
  {
    steps: [{ n: 4, label: "look for carrots" }],
    world: true,
    kind: "obs",
    d: "M190,200 L328,200",
    lx: 262,
    ly: 216,
  },
  {
    steps: [
      { n: 6, label: '"Hey Recipes..."' },
      { n: 14, label: '"the baby carrots"' },
      { n: 22, label: '"Hey Recipes..."' },
    ],
    kind: "act",
    d: "M504,160 L744,160",
    lx: 586,
    ly: 152,
  },
  {
    steps: [
      { n: 11, label: "a question" },
      { n: 19, label: "done" },
      { n: 30, label: "a question" },
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
// The chat: every step the user takes, plus what the agent says back.
type Kind = "thinks" | "says" | "does" | "agent";
interface Message {
  key: number;
  kind: Kind;
  phase: Phase;
  text: string;
}
const flags: Record<Kind, string> = {
  thinks: "USER THINKS",
  says: "USER SAYS",
  does: "USER DOES",
  agent: "AGENT SAYS",
};
const unquote = (lines: string[]) => lines.join(" ").replace(/^"|"$/g, "");
function userMessage(say: Say, key: number): Message {
  const quoted = say.lines[0].startsWith('"');
  const kind: Kind = !quoted ? "does" : say.phase === "act" ? "says" : "thinks";
  return { key, kind, phase: say.phase, text: unquote(say.lines) };
}
const chat = computed(() => {
  const out: Message[] = [];
  for (let i = 0; i <= s.value; i++) {
    const said = agent[i];
    if (said?.phase === "final")
      out.push({
        key: i * 2,
        kind: "agent",
        phase: "final",
        text: unquote(said.lines),
      });
    if (user[i].lines.length && user[i].phase !== "idle")
      out.push(userMessage(user[i], i * 2 + 1));
  }
  return out.slice(-3);
});

const shownLinks = computed(() =>
  props.compact ? links.filter((l) => !l.world) : links,
);
const live = computed(() => !!a.value && a.value.phase !== "idle");
// Mid-loop: live, but not yet handing back to the user.
const working = computed(() => live.value && a.value?.phase !== "final");

// The registry's currentFlowName: which flow holds the user's attention. Each
// entry takes effect from click n. Part of the agent's inner workings, so the
// magic box hides it.
const flowChanges = [
  { n: 8, flow: "shopping" },
  { n: 19, flow: "" },
  { n: 24, flow: "meal_planning" },
];
const currentFlowName = computed(
  () => [...flowChanges].reverse().find((f) => f.n <= s.value)?.flow ?? "",
);
// mono glyphs are ~0.6em wide: label at 9.5px, value at 10.5px
const flowChipW = computed(
  () => 16 * 5.7 + currentFlowName.value.length * 6.3 + 22,
);
</script>

<template>
  <svg
    :viewBox="compact ? '230 22 730 362' : '0 0 960 436'"
    class="loops"
    :class="{ compact }"
  >
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
    <g v-if="!compact">
      <text x="0" y="16" class="heading">THE REAL WORLD</text>
      <text x="430" y="16" class="heading" text-anchor="middle">THE USER</text>
      <text x="684" y="16" class="heading">THE APP</text>
    </g>

    <!-- the real world: a fridge with no carrots in it -->
    <g
      v-if="!compact"
      class="fridge"
      :class="{ open: s >= 3, looked: s === 4 }"
    >
      <rect x="40" y="70" width="140" height="230" rx="12" class="body" />
      <line x1="40" y1="130" x2="180" y2="130" class="shelf" />
      <line x1="40" y1="200" x2="180" y2="200" class="shelf inner" />
      <g class="food">
        <g
          v-for="(e, i) in [
            [78, 182],
            [122, 180],
            [76, 270],
          ]"
          :key="i"
        >
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

    <!-- the chat -->
    <rect x="240" y="262" width="380" height="122" rx="12" class="mono-box" />
    <foreignObject x="248" y="266" width="364" height="114">
      <div class="chat">
        <div
          v-for="(m, i) in chat"
          :key="m.key"
          class="msg"
          :class="[m.kind, m.phase, { latest: i === chat.length - 1 }]"
        >
          <span class="flag">{{ flags[m.kind] }}</span>
          <span class="text">{{ m.text }}</span>
        </div>
      </div>
    </foreignObject>

    <!-- the app and its agent -->
    <g class="app" :class="{ live }">
      <rect x="670" y="26" width="290" height="352" rx="16" class="app-frame" />
      <text x="686" y="50" class="app-title">Recipes4Me agent</text>

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

    <!-- the agent as a magic box -->
    <g v-if="hideAgentWorking" class="magic-box" :class="{ working }">
      <rect x="670" y="26" width="290" height="352" rx="16" class="cover" />
      <g class="cog">
        <circle cx="815" cy="202" r="36" class="teeth" />
        <circle cx="815" cy="202" r="31" class="wheel" />
        <circle cx="815" cy="202" r="10" class="hole" />
      </g>
    </g>

    <!-- which flow holds attention -->
    <g
      v-if="currentFlowName && !hideAgentWorking"
      :key="currentFlowName"
      class="current-flow appear"
    >
      <rect x="686" y="60" :width="flowChipW" height="20" rx="10" />
      <text x="697" y="74">
        <tspan class="cf-label">currentFlowName:</tspan>
        <tspan class="cf-value">{{ currentFlowName }}</tspan>
      </text>
    </g>

    <!-- links between the zones -->
    <g
      v-for="l in shownLinks"
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
    <g v-if="!compact">
      <rect x="0" y="394" width="960" height="38" rx="10" class="caption-box" />
      <text x="16" y="418" class="caption">{{ caption }}</text>
    </g>
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

/* chat */
.chat {
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  gap: 4px;
  overflow: hidden;
  font-family: Inter, sans-serif;
}
.msg {
  --c: var(--warm);
  flex-shrink: 0;
  max-width: 84%;
  padding: 3px 9px 4px;
  border-radius: 9px;
  border: 1px solid var(--c);
  align-self: flex-end;
  opacity: 0.45;
  transition: opacity 0.35s ease;
}
.msg.latest {
  opacity: 1;
  animation: appear 0.35s ease both;
}
.msg.thinks {
  --c: var(--purple);
  border-style: dashed;
}
.msg.thinks.trigger {
  --c: var(--info);
}
.msg.does {
  --c: var(--danger);
}
.msg.does.obs {
  --c: var(--teal);
}
.msg.says {
  background: rgba(240, 160, 80, 0.12);
}
.msg.agent {
  --c: var(--accent);
  align-self: flex-start;
  background: rgba(181, 227, 107, 0.1);
}
.flag {
  display: block;
  color: var(--c);
  font-family: var(--mono);
  font-size: 7.5px;
  font-weight: 800;
  letter-spacing: 0.12em;
}
.text {
  display: block;
  color: var(--ink);
  font-size: 11.5px;
  line-height: 1.3;
}
.msg.thinks .text {
  font-style: italic;
}
.msg.agent .text {
  color: var(--accent);
  font-weight: 600;
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

/* magic box */
.magic-box .cover {
  fill: #000;
  stroke: var(--border);
  stroke-width: 1.5;
}
.magic-box .teeth {
  fill: none;
  stroke: var(--ink-dim);
  stroke-width: 12;
  /* 12 teeth round a circumference of 2π·36 */
  stroke-dasharray: 10.5 8.35;
}
.magic-box .wheel {
  fill: var(--ink-dim);
}
.magic-box .hole {
  fill: #000;
}
.magic-box .cog {
  transform-box: fill-box;
  transform-origin: center;
  animation: spin 3s linear infinite;
  animation-play-state: paused;
}
.magic-box.working .cog {
  animation-play-state: running;
}
@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}

/* current flow */
.current-flow rect {
  fill: rgba(181, 227, 107, 0.12);
  stroke: var(--accent-dim);
  stroke-width: 1;
}
.cf-label {
  fill: var(--ink-faint);
  font-family: var(--mono);
  font-size: 9.5px;
}
.cf-value {
  fill: var(--accent);
  font-family: var(--mono);
  font-size: 10.5px;
  font-weight: 700;
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
