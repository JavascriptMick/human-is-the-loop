---
theme: default
colorSchema: dark
title: Human IS the Loop
favicon: /favicon.svg
info: |
  ## Human IS the Loop
  Embedding user first, voice enabled agents into mobile with Flutter and Gemini.
  Google DevFest 2026.
fonts:
  sans: Inter
  mono: JetBrains Mono
  serif: Source Serif 4
  weights: '400,600,700,800'
layout: image-right
image: /img/title-phone.jpg
backgroundSize: cover
transition: slide-left
duration: 30min
drawings:
  persist: false
comark: true
---

<div class="kicker">Google DevFest 2026</div>

# Human <span class="accent">IS</span> the Loop

Embedding user first, voice enabled agents into mobile with Flutter and Gemini

<div class="mt-14 flex items-center gap-6">
  <img src="/img/michael-headshot.jpg" alt="Michael Dausmann" class="w-32 h-32 rounded-full object-cover border-3 border-[var(--accent)]" />
  <div class="dim text-lg">
    Michael Dausmann
    <div class="text-sm mt-1 accent">Founder and CTO - Recipes4Me</div>
  </div>
</div>

<div class="abs-bl m-3 text-[7px] opacity-40">Photo: Karthik Balakrishnan / Unsplash</div>

<!--
[0:30] Hi, I'm Michael. This talk is about building agents that live inside your app and work alongside the user, instead of running off on their own. But first, three stories from the last three months.
-->

---
clicks: 3
---

<div class="kicker">The last three months</div>

# Autonomous agents are \#fun

<div class="mt-6 w-4/5 mx-auto">
  <HeadlineStack :stage="$clicks" />
</div>

<!--
[1:15]
[click] July: OpenAI's own evaluation agents, thousands of them coordinating over a hidden message board with about 70,000 messages, escaped a sandbox and got into Hugging Face.
[click] Anthropic went back through its own eval transcripts and found three incidents where Claude models reached the internet and got into real systems at three organisations. To their credit, they found and disclosed these themselves.
[click] And this week: an OpenAI agent researching public medicine spending got into the Medicare Statistics Reporting Service back in June and wrote files to it. The government wasn't told until September 10.
None of these agents were "evil". They did what they were built to do: take a goal and keep looping until it's met. Nobody was in their loop.
-->

---
clicks: 4
---

# "Human in the loop" is not enough

<div class="grid grid-cols-[1fr_1.15fr] gap-8 mt-8 items-start">
  <div class="card">
    <div class="kicker">Definition</div>
    <blockquote class="hitl-quote font-serif mt-3">
      In the context of AI, HITL means that humans are involved <strong>at some point</strong> in the AI workflow to ensure accuracy, safety, accountability or ethical decision-making.
    </blockquote>
    <div class="mt-3 text-xs dim">IBM, <a href="https://www.ibm.com/think/topics/human-in-the-loop" target="_blank">ibm.com/think/topics/human-in-the-loop</a></div>
  </div>
  <div class="flex flex-col gap-4">
    <div v-click="1" class="reaction">"I thought I was getting a smart collaborative assistant, not a difficult to control intern who doesn't sleep"</div>
    <div v-click="2" class="reaction">"What's it doing when i'm not watching? Do I need to guardrail everything?"</div>
    <div v-click="3" class="reaction">"The agent does the interesting part, the thinking, the deciding, the learning, and I just check its work? That's the tedious part"</div>
  </div>
</div>

<div v-click="4" class="mt-8 text-2xl text-center">
  HITL doesn't feel very good to normal humans.
</div>

<style>
.hitl-quote {
  border: none;
  padding: 0;
  background: none;
  border-radius: 0;
  font-size: 1.15rem;
  line-height: 1.5;
  color: var(--ink);
}
.reaction {
  border-left: 3px solid var(--warm);
  background: var(--bg-2);
  border-radius: 0 10px 10px 0;
  padding: 0.6rem 0.9rem;
  font-style: italic;
  color: var(--ink);
}
</style>

<!--
[1:00] The usual answer is "put a human in the loop". Here's IBM's definition: humans are involved at some point in the workflow, to ensure accuracy, safety and accountability. The human is a point of escalation. An auditor.
[click] Put yourself in that seat. The agent is doing all the interesting stuff, making the decisions, doing the learning. I'm just meant to check and correct? That feels like work.
[click] I want the agent to work with me. If I'm only involved at points, I'm working for the agent.
[click] And what happens between my points? If the agent goes rogue and starts hacking websites to get what it needs, like the stories we just saw. I would never do that. It's not how I work.
[click] In a professional setting that can be fine. Efficiency is the goal, and the human is the accountable backstop. But in a consumer app it doesn't feel responsive or personal. Nobody wants to audit their dinner.
-->

---

# TLDR

<div class="grid grid-cols-3 gap-5 mt-10">
  <div class="card">
    <div class="kicker">Quick Demo</div>
    <div class="text-xl font-700 mt-2">A real in-app agent</div>
    <div class="mt-2 dim">recipes4me: hands-free cooking, shopping and meal planning by voice</div>
  </div>
  <div class="card">
    <div class="kicker">Part 1 · Principles</div>
    <div class="text-xl font-700 mt-2">Two loops, four principles</div>
    <div class="mt-2 dim">How we can build agents that support users and reduce cognitive load</div>
  </div>
  <div class="card">
    <div class="kicker">Part 2 · Implementation</div>
    <div class="text-xl font-700 mt-2">Flutter · Signals · Gemini</div>
    <div class="mt-2 dim">Real Dart from a shipping app: tools, flows, attention, voice</div>
  </div>
</div>

<div class="mt-10 text-center text-lg">
  You'll leave with a flexible pattern that works for <strong class="accent">real</strong> users.
</div>

<!--
[0:30] Here's the plan. First I'll show you the thing working. Then the idea: two loops and four principles. Then the code. And the payoff: this runs on a small, cheap model, because the model only ever sees a handful of tools.
-->

---
layout: center
---

<div class="grid grid-cols-[auto_1fr] gap-12 items-center">
  <PhoneFrame src="https://pub-26aad13248394af7b8b24b494f0ed211.r2.dev/landing/Acedant_launch_demo_final.mp4" video sound :width="200" caption="demo video" />
  <div>
    <div class="kicker">Quick Demo</div>
    <h1>Cooking with recipes4me</h1>
    <ul class="mt-6 dim">
      <li> "Can we add tomatoes to the list" - Global actions and routing"</li>
      <li> "Lets do the weekly Meal Plan" - Wake word & Orchestrated flows</li>
      <li> "Cook this recipe?" - Contextual flow suggestions</li>
      <li> "Thats done, next step" - Interruptions</li>
      <li> "Lets add milk to the shopping list" - Flow Switching and return</li>
    </ul>
  </div>
</div>

<!--
[2:30] Recorded demo. Point out: the screen follows the agent (the router and signals move), the flow chip, the timer interrupt, switching away and back. Remember that switch: we'll replay it from the agent's side in a few minutes.
-->

---
layout: center
---

<SectionCard
  kicker="Part 1 - Establishing principles for Agentic UX"
  title="Two loops, four principles"
  subtitle="How we can build agents that support users and reduce cognitive load"
/>

<!--
[0:10] So how does that work? Start with the loop every agent runs.
-->

---
clicks: 14
---

<div class="kicker">A prompt hack developed in 2022</div>

# The Agentic Loop


<ReActAgent :stage="$clicks" class="-mt-2" />

<!--
[1:30] ReAct, reason and act. The pattern behind basically every agent framework, including the ones in those headlines. Five parts: app, harness, model API, LLM, tool. On the right is what the LLM actually sees.
[click] A question comes in from the app.
[click] The harness POSTs system prompt, tool definitions and the question.
[click] The API invokes the LLM.
[click] It reasons, and decides it needs a tool.
[click] That comes back as a structured tool call...
[click] ...the harness runs it...
[click] ...and gets an observation.
[click] Append it all and POST again.
[click] Now the LLM sees question, reasoning, action and observation.
[click] Enough to answer.
[click] Back to the harness...
[click] ...and back to the app.
[click] If it wants more tools, steps 5 to 9 repeat until it decides it's done. Nothing in here asks the user anything.
[click] From the harness side it's tiny: call the model, execute its tools, append, repeat until it responds.
-->

---
clicks: 19
---

<div class="kicker">A survival mechanism developed over the last 600 million years</div>

# The Cognitive Loop

<UserAgentLoops :stage="$clicks" class="-mt-2" />

<!--
[1:45] Now meet the person it's for. Recipes4Me is B2C, and our key users are busy people running a home, very often mums. They multitask hard. Look at everything else on their mind.
[click] They're running a loop of their own. Trigger: I need to eat more vegetables. Carrots are nice.
[click] Reason: not sure I have carrots in the fridge, better check.
[click] Act, in the real world: open the fridge.
[click] Observe: no carrots. Eggplant and milk, but no carrots.
[click] Reason: better buy some. This is where the app can help.
[click] Act: "Hey Recipes, add carrots to the list." That's the trigger for the agent's loop.
[click] The agent reasons. The user wants carrots on the list, and there's a tool that adds an item by name. Meanwhile the user has moved on.
[click] It acts: addShoppingListItem("carrots").
[click] It observes: two matching products in the user's favourites.
[click] It reasons: there's no way of knowing which one, so it doesn't guess. Better ask.
[click] It finishes with a question, which lands in the user's loop as an observation. Oh, two kinds of carrots.
[click] The user reasons with something only they know. I prefer the baby carrots, they're tender.
[click] Act: "The baby carrots." That starts a second, short agent loop. The agent observes the choice.
[click] It reasons: now there's a specific product, and a tool to add a product by id.
[click] It acts: addProductToCartById.
[click] It observes: success.
[click] It reasons: tool call is good, looks like we are done. The model decides the task is complete before it returns.
[click] And it finishes: carrots have been added to the list. Back into the user's loop.
[click] And the user is already onto the next thing: now I need some carrot recipes. The user runs the big loop. The agent runs short loops inside it, sharing the cognitive load, as an expert in the app's domain.
-->

---
clicks: 4
---
<div class="kicker">Bearing cognitive load, not producing it</div>

# Four principles for engaging Agentic UX

<div class="mt-6">
  <BoundaryCardsPrinciples :stage="$clicks" />
</div>

<!--
[1:30] So what keeps the recipes4me loop from going the way of those headlines, and makes it useful at the same time? Four principles. Everything in part 2 is labelled with one of these.
[click] Understand the user's intent: work out what problem the user is trying to solve, and only arm the tools for that. Don't make assumptions about what they're thinking.
[click] Respond to changes but take notes: when the user's intent changes, follow it, but keep the state of what they had going so they don't start from scratch when they come back.
[click] Be an expert: know the domain and the processes inside it. Known processes like cooking a recipe are written in code, so the LLM doesn't decide the next step. A generalist adds no value.
[click] No magic: the tools are the app's own functions, over the app's data, driving the same screens. If the user can't do it in the app, the agent can't either. So what code makes that carrots conversation happen?
-->

---
layout: center
---

<SectionCard
  kicker="Part 2"
  title="The code"
  subtitle="flutter_agent_framework · Flutter · Signals · Gemini"
/>

<!--
[0:15] Now the code. All of this is real Dart from the framework and the recipes app, cut down to fit. We'll replay the carrots conversation from the cognitive loop, then the cooking and shopping switch from the demo. Each slide is tagged with the principle it implements.
-->

---
clicks: 5
---

<div class="kicker">Architecture</div>

# Four layers, and the LLM is just one call

<ArchDiagram :stage="$clicks" />

<!--
[1:15]
[click] Voice: the AudioCoordinator is one state machine covering wake word, speech to text and text to speech, with queues so listening and speaking never overlap.
[click] AgentService runs each turn. It calls the LLM through LLMService with only the tools that are armed.
[click] The IntentRegistry is a deliberately simple, synchronous store: flows, global tools, armed tools, current flow, interrupts.
[click] Your app code: flows like CookingAssistant, with annotated tool methods and an orchestrator.
[click] Flows change app state through the same Signals stores and GoRouter the UI uses, so the screen shows what the agent did.
-->

---
clicks: 1
class: dense
---

<div class="kicker">Principle 4 · No Magic</div>

# The way in: a global tool

<div class="grid grid-cols-[0.75fr_1.25fr] gap-6 mt-2">
<div>
  <UserAgentLoops compact :stage="[6, 8][$clicks]" />
  <div class="text-xs dim mt-2">Slide 8, click {{ [6, 8][$clicks] }}: "Hey Recipes, add carrots to the list."</div>
</div>

````md magic-move {lines: true}
```dart
// shopping_list_assistant.dart - you write this
@IntentTool(description: 'Add an item to the shopping list', isGlobal: true)
Future<IntentResult> addShoppingListItem(
  @Param('The item to add') String item,
) async {
  ...
}

@IntentTool(
  description: 'Add a specific offered product to the cart by its id',
  isGlobal: false,
)
Future<IntentResult> addProductToCartById(
  @Param('The tm_product_id of the chosen product') int tmProductId,
) async {
  ...
}
```

```dart
// shopping_list_assistant.intent.g.dart - build_runner writes this
agent.registerTool(
  IntentToolRegistration(
    toolName: 'addShoppingListItem',
    description: '''Add an item to the shopping list''',
    parametersSchema: {
      'type': 'object',
      'properties': <String, dynamic>{
        'item': <String, dynamic>{
          'type': 'string',
          'description': '''The item to add''',
        },
      },
      'required': ['item'],
    },
    handler: (args) => addShoppingListItem(args['item'] as String),
    isGlobal: true,
  ),
);
```
````
</div>

<!--
[1:15] Back to the carrots. "Hey Recipes, add carrots to the list." What can the agent actually do with that? It can only call tools, and a tool is just a method on the app's own class. The description is the prompt. isGlobal: true means it's always offered, like a main menu item. That's the only way into the agent. The second tool, addProductToCartById, is isGlobal: false. The LLM can't see it yet. Hold that thought.
[click] build_runner turns the Dart signature into JSON Schema and generates the registration, so there are no hand-written schemas. This description is exactly what the agent "reasoned" over on slide 8: there's a tool that adds an item by name. So it calls it.
-->

---
clicks: 6
class: dense
---

<div class="kicker">Principle 3 · Be an expert</div>

# The code asked, not the LLM

<div class="grid grid-cols-[0.75fr_1.25fr] gap-6 mt-2">
<div>
  <UserAgentLoops compact :stage="[8, 9, 9, 10, 11, 11, 11][$clicks]" />
  <div class="text-xs dim mt-2">Slide 8, click {{ [8, 9, 9, 10, 11, 11, 11][$clicks] }}: two kinds of carrots, so ask.</div>
</div>

```dart {all|4-6|8-11|13-25|15-18|19-23|24}
Future<IntentResult> addShoppingListItem(String item) async {
  _goToShopping(); // show the user the cart while acting

  // favourites first: what the user actually buys
  final candidates = _matchByName(item, favourites);
  // ...falls back to product search, handles no match

  if (candidates.length == 1) {
    await _cartStore.addProductToCart(candidates.first);
    return IntentResult.done(['Added ${_friendlyName(candidates.first)}.']);
  }

  _addCandidates = {for (final p in candidates) p.tm_product_id: p};
  return IntentResult.withLLMContext(
    userMessages: [
      'I found a few options for $item: $spokenOptions. '
      'Which would you like?',
    ],
    llmMessages: [
      'Products found for "$item". Call addProductToCartById '
      'with the chosen tm_product_id:',
      for (final p in candidates) _describeCandidate(p),
    ],
    tools: ['addProductToCartById'], // arm exactly one follow-up
  );
}
```
</div>

<!--
[1:30] Here's the tool the agent called. It navigates to the cart first, using the same router the UI uses, so the user sees what's happening.
[click] It looks in the user's favourites first: what they actually buy.
[click] One match? Just add it. No conversation needed.
[click] Two matches. On slide 8 I told you the agent reasoned "no way of knowing which one, better ask the user". That was a white lie. The LLM didn't decide that. This code did. The expert in the app knows that two products means a question, so it doesn't leave that to the model. It also takes notes: the candidates it offered.
[click] The user hears friendly names.
[click] The LLM hears the ids it will need, and an instruction.
[click] And exactly one follow-up tool gets armed: addProductToCartById. That's the tool we couldn't see a minute ago.
-->

---
clicks: 4
class: dense
---

<div class="kicker">Principle 1 · Understand the user's intent</div>

# Only what's armed can run

<div class="grid grid-cols-[0.75fr_1.25fr] gap-6 mt-2">
<div>
  <UserAgentLoops compact :stage="[13, 16, 16, 17, 19][$clicks]" />
  <div class="text-xs dim mt-2">Slide 8, click {{ [13, 16, 16, 17, 19][$clicks] }}: "The baby carrots."</div>
</div>

```dart {all|4-5|15-20|21-23|24-26}
// AgentService: every tool call from the LLM lands here
Future<IntentResult?> _executeIntentTool(String toolName, Map args) async {
  final registration = _registry.registrationFor(toolName);
  // Availability gate: only currently-advertised tools may execute
  if (!_registry.isToolArmed(toolName)) return null;

  final owningFlow = _registry.flowNameForTool(toolName);
  final result = await registration!.handler(args);
  await _applyIntentResult(result, owningFlow); // attention + next tools
  return result;
}

// ShoppingListAssistant: the follow-up that addShoppingListItem armed
Future<IntentResult> addProductToCartById(int tmProductId) async {
  final product = _addCandidates[tmProductId];
  if (product == null) {
    return IntentResult.done([
      "Sorry, that wasn't one of the options I offered.",
    ]);
  }
  await _cartStore.addProductToCart(product);
  await _settleCartContains(product.tm_product_id);
  _addCandidates = {};
  return IntentResult.done([
    'Added ${_friendlyName(product)}. You have $_itemCountPhrase in your cart.',
  ]);
}
```
</div>

<!--
[1:15] The user says "the baby carrots". Out of context that means nothing. But the model now has the global tools plus one armed tool, and the ids of two products.
[click] Every tool call goes through this gate in AgentService. If it isn't armed, it doesn't run, even if the model hallucinates a name or a stale turn arrives late.
[click] And the tool checks its own notes: it only accepts a product it actually offered.
[click] It adds the exact product, through the same CartStore the UI uses, and waits for the store to settle so the count is right.
[click] IntentResult.done: say the result and disarm. That's the "Carrots have been added" on slide 8. Every turn of the agent's loop on slide 8 is one of these code paths.
-->

---
clicks: 6
class: '!py-6'
---

<h2 class="!mb-2 !font-800" style="color: var(--ink)">The same mechanics, with a long-running flow</h2>

<div class="h-[440px]">
  <LoopSimulator :stage="$clicks" />
</div>

<!--
[1:30] Carrots was a short loop: one question, one answer. Cooking is long. Let's replay the demo from the agent's side.
[click] "let's cook the satay stir-fry". startCooking is a global tool, just like addShoppingListItem. The cooking flow now holds attention and arms exactly two tools.
[click] "I'm ready". readyToCook runs, the flow reads step 1 and sets a timer. Only stepComplete is armed now.
[click] 15 minutes later, the app itself interrupts. No user input, but it's still code in the app deciding to speak.
[click] I get distracted and ask to add satay sauce to the shopping list. Shopping takes my attention, and cooking is backgrounded with its state kept.
[click] "back to cooking", switchToFlow, and we pick up at step 1.
[click] Three moments here need code: the flow driving the steps, the interrupt, and the switch away and back.
-->

---
clicks: 4
class: dense
---

<div class="kicker">Principle 3 · Be an expert</div>

# A flow drives the steps and the screen

<div class="grid grid-cols-[0.75fr_1.25fr] gap-6 mt-2">
<div class="h-[380px]">
  <LoopSimulator compact :stage="[1, 1, 1, 2, 2][$clicks]" />
</div>

```dart {all|2-4|6-13|15-23|24-27}
IntentResult _orchestrate(CookingAssistantContext ctx, List<String> messages) {
  // show the user the cooking assistant screen while orchestrating
  final path = appRouter.routerDelegate.currentConfiguration.uri.path;
  if (path != '/recipes/assistant') appRouter.go('/recipes/assistant');

  if (ctx.is_in_pre_cook) {
    orchestratedStepIndex.value = -1; // signal: scroll to ingredients
    return IntentResult.withNextTools(
      [...messages, "Let's cook ${ctx.recipe_name}.",
       'Would you like me to read out the ingredients, or are you ready?'],
      ['readIngredients', 'readyToCook'],
    );
  }

  orchestratedStepIndex.value = ctx.current_step_index; // highlight step
  final stepAtSet = ctx.current_step;
  if (!identical(_timerStep, stepAtSet)) {
    _stepTimer?.cancel(); // previous step's timer is obsolete
    if (stepAtSet.timerDuration != null) {
      _stepTimer = Timer(stepAtSet.timerDuration!,
          () => _onStepTimerFired(ctx, stepAtSet));
    }
  }
  return IntentResult.withNextTools(
    [...messages, 'next step', ctx.current_step.prompt],
    ['stepComplete'],
  );
}
```
</div>

<!--
[1:30] Carrots needed one question. Cooking needs a process, and a process is something the expert writes in code. Every cooking tool handler changes the context and then calls this orchestrator. It's re-entrant: it looks at the context and works out where we are.
[click] It navigates with the same router the UI uses.
[click] Before cooking: update a signal so the screen scrolls, ask one question, and arm two tools. That's "read out the ingredients, or ready to cook?".
[click] "I'm ready": highlight the step, and set a timer if the step has one.
[click] Then say the step and arm stepComplete. The LLM never decides what step comes next.
-->

---
clicks: 2
class: dense
---

<div class="kicker">Principle 3 · Be an expert</div>

# The app speaks first

<div class="grid grid-cols-[0.75fr_1.25fr] gap-6 mt-2">
<div class="h-[380px]">
  <LoopSimulator compact :stage="3" />
</div>

<div>

```dart {all|3|4-15}
void _onStepTimerFired(CookingAssistantContext ctx, CookingStep firingStep) {
  // Ignore irrelevant or superseded timer events
  if (identical(ctx, _context) && identical(firingStep, ctx.current_step)) {
    _timerInterruptStream.add(
      InterruptIntent(
        flowName: flowName,
        result: IntentResult.withNextTools(
          [firingStep.timerPrompt ??
              "It's been ${_speakDuration(firingStep.timerDuration!)} "
              '- have you checked the ${firingStep.name}?'],
          ['stepComplete'], // re-arm the same follow-up
        ),
        marker: '[timer elapsed: ${firingStep.name}]',
      ),
    );
  }
}
```

<div class="mt-4 card text-sm">
Proactive, but <strong>not autonomous</strong>: the flow decides when to speak, and AgentService drains the interrupt queue when it's safe to.
</div>

</div>
</div>

<!--
[1:00] The timer from the last slide fires. There was no user input, but it's still code in the app deciding to speak.
[click] Only if the timer still belongs to the current step. If the user has moved on, it stays quiet.
[click] An interrupt is just an IntentResult that arrived without a tool call. AgentService applies it the same way, which moves attention back to cooking and re-arms stepComplete.
-->

---
clicks: 3
class: dense
---

<div class="kicker">Principle 2 · Respond to changes but take notes</div>

# Switch away, keep notes, come back

<div class="grid grid-cols-[0.75fr_1.25fr] gap-6 mt-2">
<div class="h-[380px]">
  <LoopSimulator compact :stage="[4, 4, 4, 5][$clicks]" />
</div>

<div>

```dart {all|1-6|8-14|16-22}
// AgentService: the ONLY place attention moves
Future<void> _transferAttention(String? newFlowName) async {
  if (newFlowName == null || newFlowName == _registry.currentFlowName) return;
  await _registry.currentFlow?.backgroundThisFlow(); // context is kept
  _registry.setCurrentFlow(newFlowName);
}

// every LLM turn: each flow summarises itself
final sessionPrompts = [
  if (flows.currentFlow case final flow?)
    'Current Workflow: ${flow.currentFlowContextSummary}',
  ...flows.otherActiveFlows.map((flow) =>
      'Other In Progress Workflow: ${flow.switchToFlowContextSummary}'),
];

// "back to cooking": switchToFlow('cooking') calls enterFlow
Future<IntentResult> enterFlow(IntentFlow flow) async {
  await _transferAttention(flow.flowName);
  final result = await flow.resumeThisFlow(); // cooking: _orchestrate(ctx, [])
  _registry.setIntentTools(result.requestedTools);
  return result;
}
```

<div class="text-xs dim mt-2">
Every flow implements <code>IntentFlow</code>: a name, two summaries, and <code>resumeThisFlow</code> / <code>backgroundThisFlow</code> / <code>cancelThisFlow</code>.
</div>

</div>
</div>

<!--
[1:30] Mid-recipe, "add satay sauce to the list". That's addShoppingListItem again, the same global tool as the carrots. It's owned by the shopping flow, so attention moves.
[click] Attention moves in exactly one place. The outgoing flow is backgrounded exactly once, and cooking keeps its context: recipe, step and timer.
[click] How does the model know cooking is still going while we shop? Every turn, every flow writes its own one-line summary into the prompt.
[click] "Back to cooking" is the switchToFlow system tool. It moves attention and calls resumeThisFlow, and for cooking that's the same re-entrant orchestrator. We pick up at step 1 because the notes were kept. Every flow implements this small IntentFlow interface: a name, two summaries, and resume, background and cancel.
-->

---

# Learnings

<div class="grid grid-cols-2 gap-4 mt-6">
  <div v-click class="card"><strong>You need a killer use case</strong><br/><span class="dim">Users are wary of AI. It has to earn its place. For me that was hands-free cooking.</span></div>
  <div v-click class="card"><strong>Agent UX is hard to get right</strong><br/><span class="dim">Keeping it fluid, arming the right tools, and deciding when to orchestrate vs leave it in the agent loop.</span></div>
  <div v-click class="card"><strong>On-device models didn't make it</strong><br/><span class="dim">Gemini was accurate but too slow, Function Gemma and Needle 2 were fast enough but inaccurate.</span></div>
  <div v-click class="card"><strong>Cross platform wake word is tricky</strong><br/><span class="dim">The good solutions are paid. I rolled my own with sherpa_onnx.</span></div>
</div>

<!--
[1:30] A few honest lessons from building this.
[click] You need a killer use case to justify the hassle. Users are wary of AI. For me, hands-free cooking was the one that made it worth it.
[click] UX with agents is hard to get right. Making it fluid, giving it the right tool calls, and knowing when to orchestrate in code vs leave it in the agent loop is tricky.
[click] I couldn't get on-device models to work. Tried Gemini on device, tried tiny models - they were too dumb.
[click] Wake word integration is tricky. You have to pay for the good solutions. I rolled my own in the end, and it's ok.
[click] Next, I'll definitely look at integrating Jev-style models for workflows with simple decision points.
-->

---
layout: center
class: text-center
---

# Thank you

<div class="dim mt-4">Human <span class="accent">IS</span> the loop</div>

<div class="mt-10 flex justify-center">
  <QrCode url="https://acedant.ai" :size="140" />
</div>

<!--
[0:30] Questions.
-->

---
class: dense
---

<div class="kicker">Appendix</div>

# On-device agent proof of concept

<table class="poc-table mt-3">
  <thead>
    <tr><th>#</th><th>Model / runtime</th><th>Accuracy</th><th>s/turn</th><th>Notes</th></tr>
  </thead>
  <tbody>
    <tr><td>1</td><td>Gemma 4 E2B, flutter_gemma, shared session</td><td>62%</td><td>6.2</td><td class="dim">Late turns stopped calling tools; "10 min" became 9</td></tr>
    <tr><td>2</td><td>Gemma 4 E2B, native LiteRT-LM, per-turn</td><td class="accent">100%</td><td>11</td><td class="dim">16.8 s cold load</td></tr>
    <tr><td>3</td><td>Gemma 4 E2B, flutter_gemma, per-turn</td><td class="accent">100%</td><td>10.2</td><td class="dim">Run 1's 62% was mostly the shared session, not the wrapper</td></tr>
    <tr class="faint"><td>4</td><td>Gemma 4 E4B on iPhone</td><td>-</td><td>-</td><td>Never run</td></tr>
    <tr><td>5</td><td>Gemma 4 E2B, native, shared session</td><td>95%</td><td>5.8</td><td class="dim">Best Gemma latency, still about 3x the bar</td></tr>
    <tr><td>6</td><td>LFM2.5-2.6B, LEAP SDK</td><td>76%</td><td class="danger">74</td><td class="dim">LEAP on Android runs on CPU only, with no prefix cache</td></tr>
    <tr><td>7</td><td>Needle 2 (45M)</td><td>62%</td><td>1.3</td><td class="dim">21 MB RAM, but --serve wedges and the LoRA tune made it worse</td></tr>
    <tr class="best"><td>8</td><td>Qwen3.5-0.8B, llama-server over adb</td><td>81%</td><td class="accent">1.95</td><td class="dim">The only run under 2 s. Inside the app: 76% at 18.4 s</td></tr>
    <tr><td>9</td><td>Granite 4.0 Nano 1B, llama.cpp in-app</td><td>81%</td><td class="danger">30</td><td class="dim">Cleanest output (0 malformed), far too slow</td></tr>
    <tr><td>10</td><td>FunctionGemma 270M base</td><td class="danger">33%</td><td>0.98</td><td class="dim">Fast and poor</td></tr>
    <tr><td>11</td><td>FunctionGemma 270M, LoRA-tuned</td><td class="danger">25%</td><td>3.7</td><td class="dim">App suite</td></tr>
    <tr><td>12</td><td>Same tune at F16</td><td class="danger">25%</td><td>2.4</td><td class="dim">Quantisation wasn't the problem; the model's ceiling was</td></tr>
  </tbody>
</table>

<div class="mt-3 text-sm dim">Nothing hit accurate <em>and</em> fast enough. The models that were accurate were too slow, and the fast ones weren't accurate.</div>

<style>
.poc-table { width: 100%; font-size: 0.68rem; border-collapse: collapse; }
.poc-table th { text-align: left; font-family: var(--mono); font-size: 0.6rem; letter-spacing: 0.1em; text-transform: uppercase; color: var(--accent); border-bottom: 1px solid var(--border); padding: 0.3rem 0.5rem; }
.poc-table td { padding: 0.22rem 0.5rem; border-bottom: 1px solid var(--bg-2); }
.poc-table td:nth-child(3), .poc-table td:nth-child(4), .poc-table th:nth-child(3), .poc-table th:nth-child(4) { text-align: right; font-family: var(--mono); white-space: nowrap; }
.poc-table tr.best { background: rgba(181, 227, 107, 0.08); }
.poc-table tr.faint td { color: var(--ink-faint); }
</style>

<!--
Appendix, for Q&A. Results from trying to run the agent fully on device.
Gemma 4 E2B got to 100% accuracy with a fresh session per turn, but at 10-11 s per turn. Sharing a session made it faster but less accurate.
The only run under 2 s per turn was Qwen3.5-0.8B over adb, and inside the app that dropped to 76% at 18.4 s.
Tiny function-calling models were fast but too dumb, and LoRA tuning didn't fix it.
-->

---
clicks: 2
---

<div class="kicker">Principle 2 · Respond to changes but take notes</div>

# What does the user need <span class="accent">now</span>?

<div class="grid grid-cols-[1fr_1.1fr] gap-10 mt-6 items-start">
  <div class="text-lg leading-relaxed">
    <div>One flow is <strong>current</strong>: it owns the conversation and chooses which tools are armed.</div>
    <div v-click="1" class="mt-4">Others are <span class="warm">backgrounded</span>: paused with their context kept, and the LLM gets a one-line summary.</div>
    <div v-click="2" class="mt-4">The agent follows the user's attention. It doesn't pull the user towards its own goal.</div>
  </div>
  <AttentionStack
    :flows="$clicks < 1
      ? [
          { name: 'cooking', state: 'current', summary: 'Quick Pork Satay, step 3 of 8' },
          { name: 'shopping', state: 'inactive' },
          { name: 'meal planning', state: 'inactive' },
          { name: 'whats for dinner', state: 'inactive' },
        ]
      : [
          { name: 'meal planning', state: 'current', summary: 'Planning next week, 3 recipes chosen' },
          { name: 'cooking', state: 'background', summary: 'Quick Pork Satay, step 3 of 8' },
          { name: 'shopping', state: 'background', summary: '4 items on the list' },
          { name: 'whats for dinner', state: 'inactive' },
        ]"
  />
</div>

<!--
[1:00] People don't do one thing at a time, especially in a kitchen.
[click] While the rice simmers, the user starts planning next week. Cooking goes to the background, still at step 3.
[click] The agent's job is to keep up with the user.
-->

---

<div class="kicker">Principle 1 · Understand the user's intent</div>

# The flow, not the LLM, decides what comes next

```dart {all|3-4|5-17|19}
FutureOr<IntentResult> startCooking(@Param('The Recipe Id to cook') int recipe_id) {
  final ctx = _context;
  if (ctx != null) {
    ctx.pending_restart_recipe_id = recipe_id;
    return IntentResult.withLLMContext(
      userMessages: [
        "You're partway through ${ctx.recipe_name}. Would you like to "
        'continue where you left off or start from scratch?',
      ],
      llmMessages: [
        'A cooking session for ${ctx.recipe_name} is in progress. The user '
        'must choose to continue it or start the new recipe from scratch.',
        'Call continueCooking to resume, or restartCooking to start the '
        'newly requested recipe.',
      ],
      tools: ['continueCooking', 'restartCooking'],   // ← arm exactly two tools
    );
  }
  return _startRecipe(recipe_id, []);
}
```

<div class="flex gap-3 mt-3">
  <span class="chip">IntentResult.done(msgs)</span>
  <span class="chip">.withNextTools(msgs, tools)</span>
  <span class="chip">.withLLMContext(user, llm, tools)</span>
  <span class="chip">.handingOffTo(flow)</span>
</div>

<!--
[1:00] Every tool returns an IntentResult.
[click] Already cooking something? Don't let the LLM guess.
[click] Say one thing to the user and something else to the LLM, and arm exactly two tools. Whatever the user says next, the model can only continue or restart.
[click] Otherwise, start the recipe.
-->

---

<div class="kicker">Principle 4 · No Magic</div>

# A flow can only touch what you hand it

```dart {all|22-25}
_provider = openAI(
  baseUrl: Environment.agentBaseUrl,          // proxy → Gemini
  tokenProvider: _accessToken,                // user session, refreshed per request
  headersProvider: () async => {'X-Account-Id': '${activeAccountId.value}'},
);

await FlutterAgentFramework.initialize(
  AgentConfig.voice(
    provider: _provider!,
    toolScoping: ToolScopingStrategy.narrowToolList,
    systemPrompt: '''
You are a helpful cooking, shopping and meal planning assistant inside the recipes4me app.
The user speaks and their words are transcribed automatically, so the text you receive
may contain transcription errors...
When the user asks you to perform an action, call the appropriate function.
You can manage several different workflows simultaneously...
''',
    wakeKeywordId: 'hey_recipes',
  ),
);

ShoppingListAssistant.instance.initialize(cartStore: cartStore, searchStore: searchStore, ...);
CookingAssistant.instance.initialize(apiClient: apiClient, activeAccountId: activeAccountId);
MealPlanAssistant.instance.initialize(mealPlanStore: mealPlanStore, myRecipesStore: myRecipesStore);
WhatsForDinnerAssistant.instance.initialize(...);
```

<!--
[0:30] Bootstrapping, lib/voice_agent/voice_agent.dart. A Gemini proxy with no API key in the bundle, a short system prompt, a wake word.
[click] The bit that matters: each assistant gets the app stores it needs. That's the boundary. Whatever you hand a flow is all it can touch.
-->

---
clicks: 6
---

<div class="kicker">Hands-free</div>

# Voice: one state machine, two queues

<div class="mt-6">
  <AudioStateMachine :stage="$clicks" />
</div>

<div class="mt-6 grid grid-cols-3 gap-4 text-sm">
  <div class="card"><strong>Wake word on device</strong><br/><span class="dim">sherpa VAD + keyword spotting. "Hey Recipes"</span></div>
  <div class="card"><strong>expectsReply</strong><br/><span class="dim">each spoken item says whether to open the mic when it finishes</span></div>
  <div class="card"><strong>Barge-in</strong><br/><span class="dim"><code>interrupt()</code> stops TTS, clears the queue and starts listening</span></div>
</div>

<!--
[1:00] Hands-free in a kitchen is hard. Listening and speaking can never overlap, so the AudioCoordinator is one state machine with an input queue and an output queue. Step through the "cook beans" exchange.
-->

---

<div class="kicker">Principle 4 · No Magic</div>

# The same agent, three ways in

<div class="grid grid-cols-[1fr_auto_auto_auto] gap-4 items-start mt-2">
<div class="text-sm">

```dart
// AgentService - the only type the app names
agent.tapMic();                              // talk
agent.processUserInput(text,                 // type
    fromVoice: false);
agent.switchToFlow('cooking');               // tap a flow chip
agent.directExecuteTool('startCooking',      // contextual action,
    {'recipe_id': recipe.id});               // no LLM needed

// mirror it into Signals for the UI
agent.stateStream;       // idle · listening · thinking · speaking
agent.transcriptStream;
agent.registryChanges;
```

</div>
  <PhoneFrame src="/img/agent_contextual_suggestion_cok.png" :width="120" caption="contextual launch" />
  <PhoneFrame src="/img/agent_slide_menu_cook.png" :width="120" caption="cook · talk · type" />
  <PhoneFrame src="/img/agent_chat_slide_menu.png" :width="120" caption="transcript + flow chip" />
</div>

<!--
[1:00] Voice is one way in, not the only way. A "Cook this recipe?" button calls directExecuteTool, so it runs the same flow with no LLM call and no tokens. Typing and flow chips go into the same turn loop.
-->
