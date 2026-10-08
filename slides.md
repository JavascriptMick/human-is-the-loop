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
[0:30] Hi, I'm Michael. This talk is about building agents that live inside your app and work alongside the user, instead of running off on their own.
-->

---

# TLDR

<div class="grid grid-cols-3 gap-5 mt-10">
  <div class="card">
    <div class="kicker">The Problem</div>
    <div class="text-xl font-700 mt-2">Agents loop without us</div>
    <div class="mt-2 dim">Autonomous agents only involve the user at the start and the end</div>
  </div>
  <div class="card">
    <div class="kicker">The Principles</div>
    <div class="text-xl font-700 mt-2">Two loops, four principles</div>
    <div class="mt-2 dim">How we can build agents that support users and reduce cognitive load</div>
  </div>
  <div class="card">
    <div class="kicker">The Solution</div>
    <div class="text-xl font-700 mt-2">Flutter · Signals · Gemini</div>
    <div class="mt-2 dim">A demo and real Dart from a shipping app: intent tools, flows and orchestration</div>
  </div>
</div>

<div class="mt-10 text-center text-lg">
  You'll leave with a flexible pattern that works for <strong class="accent">real</strong> users.
</div>

<!--
[0:30] Here's the plan. First the problem: agents that run off on their own. Then the idea: two loops and four principles. Then the solution: a demo, and the code that makes it work.
-->

---
clicks: 3
---

<div class="kicker">Public perception</div>

# Autonomous agents are out of control

<div class="mt-6 w-4/5 mx-auto">
  <HeadlineStack :stage="$clicks" />
</div>

<!--
[1:15] Three stories from the last three months.
[click] July: OpenAI's own evaluation agents, thousands of them coordinating over a hidden message board with about 70,000 messages, escaped a sandbox and got into Hugging Face.
[click] Anthropic went back through its own eval transcripts and found three incidents where Claude models reached the internet and got into real systems at three organisations. To their credit, they found and disclosed these themselves.
[click] And this week: an OpenAI agent researching public medicine spending got into the Medicare Statistics Reporting Service back in June and wrote files to it. The government wasn't told until September 10.
None of these agents were "evil". They did what they were built to do: take a goal and keep looping until it's met. Nobody was in their loop.
-->

---
clicks: 4
---

<div class="kicker">HITL</div>

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
    <div v-click="1" class="reaction">"I thought I was getting a smart assistant, not a sassy intern"</div>
    <div v-click="2" class="reaction">"Do I need to guardrail everything?"</div>
    <div v-click="3" class="reaction">"The agent does the interesting stuff, checking work is boring"</div>
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
clicks: 14
---

<div class="kicker">TLDR: it's a prompt hack</div>

# The Agentic Loop

<ReActAgent :stage="$clicks" class="-mt-2" />

<!--
[1:30] So why does this happen? ReAct, reason and act. The pattern behind basically every agent framework, including the ones in those headlines. Five parts: app, harness, model API, LLM, tool. On the right is what the LLM actually sees.
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
[click] From the harness side it's tiny: call the model, execute its tools, append, repeat until it responds. The user is only involved at the start and the end.
-->

---
clicks: 4
---
<div class="kicker">Do Better: Bearing cognitive load, not producing it</div>

# Four principles for engaging Agentic UX

<div class="mt-6">
  <BoundaryCardsPrinciples :stage="$clicks" />
</div>

<div class="mt-6 text-center text-xs dim">
  For a deeper dive into HCI research on multitasking, interruption and mixed-initiative interaction. See the <Link to="18">appendix</Link>.
</div>

<!--
[1:30] So how do we keep the user in the loop the whole way through, and make the agent useful at the same time? Four principles. Every code slide later is labelled with one of these.
[click] Understand the user's intent: work out what problem the user is trying to solve, and only arm the tools for that. Don't make assumptions about what they're thinking.
[click] Respond to changes but take notes: when the user's intent changes, follow it, but keep the state of what they had going so they don't start from scratch when they come back.
[click] Be an expert: know the domain and the processes inside it. Known processes like cooking a recipe are written in code, so the LLM doesn't decide the next step. A generalist adds no value.
[click] No magic: the tools are the app's own functions, over the app's data, driving the same screens. If the user can't do it in the app, the agent can't either. So how do I make this work in practice?
-->

---
layout: center
clicks: 1
---

<div class="grid grid-cols-[auto_1fr] gap-12 items-center">
  <PhoneFrame src="/HumanIsTheLoopDemoVideo.mp4" video sound :width="200" caption="demo video" />
  <div>
    <div class="kicker">Quick Demo</div>
    <h1>Cooking with recipes4me</h1>
    <ul class="mt-6 dim">
      <li> "Can we add tomatoes to the list" - Intent Tools and routing"</li>
      <li> "Lets do the weekly Meal Plan" - Wake word & Intent Flows</li>
    </ul>
  </div>
</div>

<!--
[2:30] Recorded demo. Point out: the screen follows the agent (the router and signals move), the flow chip, the timer interrupt, switching away and back. Keep an eye on cooking: that's the flow we'll look at in code.
-->

---
clicks: 7
---

<div class="kicker">Making it work in practice (one approach)</div>

# The Recipes4Me Solution

<div class="grid grid-cols-2 gap-6 mt-4 text-sm">
  <div class="card">
    <div class="kicker">Package</div>
    <div class="text-xl font-700 mt-1">Flutter Agent Framework</div>
    <div v-click="1" class="solution-item">
      <strong>Voice capable</strong>
      <div class="dim">On-device STT + TTS and wake words</div>
    </div>
    <div v-click="2" class="solution-item">
      <strong>Intent Tools</strong> <span class="chip">P1</span>
      <div class="dim">Global tools, available at all times. A bit like main menu actions: quick actions, or the way into a longer flow <em>- add an item to the cart, start meal planning.</em></div>
    </div>
    <div v-click="3" class="solution-item">
      <strong>Intent Flows</strong> <span class="chip">P1</span> <span class="chip">P2</span>
      <div class="dim">Multi-step, multi-conversation, stateful tasks. A bit like wizards. Switch in and out of the foreground and keep their state. <em>cook a recipe, plan my weekly meals.</em></div>
    </div>
  </div>
  <div class="card">
    <div class="kicker">Your code</div>
    <div class="text-xl font-700 mt-1">Agent Enhanced App</div>
    <div v-click="4" class="solution-item">
      <strong>An assistant class per area of expertise</strong> <span class="chip">P3</span>
      <div class="dim">A bit like skills but coded deterministically <em>- shopping, meal planning, cooking</em>.</div>
    </div>
    <div v-click="5" class="solution-item">
      <strong>Annotate public methods as Intent Tools</strong> <span class="chip">P1</span>
      <div class="dim">Actions that should be global <em>- addItemToCart</em></div>
    </div>
    <div v-click="6" class="solution-item">
      <strong>Optionally implement IntentFlow</strong> <span class="chip">P2</span> <span class="chip">P3</span>
      <div class="dim">State plus orchestration <em>- currentStep, requiredFields</em></div>
    </div>
    <div v-click="7" class="solution-item">
      <strong>Use the app's stores and router</strong> <span class="chip">P4</span>
      <div class="dim">Change state the way the UI does, and take the user to the screen that shows it</div>
    </div>
  </div>
</div>

<div class="principle-legend flex justify-center gap-6 mt-4 text-xs dim">
  <span><span class="chip">P1</span> Understand the user's intent</span>
  <span><span class="chip">P2</span> Respond to changes but take notes</span>
  <span><span class="chip">P3</span> Be an expert</span>
  <span><span class="chip">P4</span> No magic</span>
</div>

<style>
.solution-item { margin-top: 0.8rem; }
.solution-item .chip, .principle-legend .chip { font-size: 0.6rem; padding: 0.05em 0.5em; margin-left: 0.25em; }
</style>

<!--
[1:30] Two halves: a Flutter package, the framework, and your app code that uses it.
[click] The framework handles voice: speech to text and text to speech on device, plus wake words.
[click] It gives you two building blocks. Intent Tools are global tools the agent can always call, like main menu actions. Some are quick, like adding an item to the cart. Some kick off something longer, like meal planning.
[click] That longer thing is an Intent Flow: multi-step, multi-conversation and stateful, a bit like a wizard. You can switch away from it and come back without losing your place.
[click] The other half is the app. For each area of expertise there's an assistant class, a bit like a skill.
[click] You annotate its public methods as tools.
[click] If it runs a process, it implements IntentFlow and owns the state and the orchestration.
[click] And it changes things the same way the UI does: through the app's stores, and the router takes the user to the screen that shows what happened. No magic. Let's look at the code, starting with a tool.
-->

---
clicks: 1
class: dense
---

# Intent Tool

````md magic-move {lines: true}
```dart
// shopping_list_assistant.dart - you write this
@IntentTool(
  description: 'Add an item to the shopping list', 
  isGlobal: true,
)
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

<!--
[1:15] "Hey Recipes, add carrots to the list." What can the agent do with that? It can only call tools, and a tool is just a method on the app's own class. The description is the prompt. isGlobal: true means it's always offered, like a main menu item. The second tool, addProductToCartById, is isGlobal: false. The LLM can't see it until addShoppingListItem finds two kinds of carrots and arms it.
[click] build_runner turns the Dart signature into JSON Schema and generates the registration, so there are no hand-written schemas.
-->

---
class: dense
---

<div class="kicker">Flow Switching</div>

# Intent Flow

<div class="grid grid-cols-[1fr_1fr] gap-4 mt-2">

```dart {all|4-8|10-16|18-22}
class CookingAssistant implements IntentFlow {
  CookingAssistantContext? _context; // null = not cooking

  @override
  String get flowName => 'cooking';

  @override
  bool get flowIsActive => _context != null;

  // in the prompt while cooking holds attention
  @override
  String get currentFlowContextSummary {
    final ctx = _context!;
    return 'Cooking ${ctx.recipe_name}, on ${ctx.current_step.name}. '
        'Ingredients: ${ctx.ingredients_summary}.';
  }

  // in the prompt while another flow holds attention
  @override
  String get switchToFlowContextSummary =>
      'Also cooking ${_context!.recipe_name}, '
      'on ${_context!.current_step.name}.';
```

```dart {all|1-3|5-7|9-12}
  // "back to cooking": pick up where we left off
  @override
  IntentResult resumeThisFlow() => _orchestrate(_context!, []);

  // switched away: nothing to do, the context is kept
  @override
  void backgroundThisFlow() {}

  // abandoned: drop the context, timer and screen state
  @override
  void cancelThisFlow() => _clearSession();
}
```

</div>

<!--
[1:15] Cooking is a flow, so CookingAssistant implements IntentFlow. All of its notes live in one context object. Null means we're not cooking.
[click] A name, and whether it's active. Active flows show up as chips in the UI, and the agent can switch to them.
[click] Every turn, the current flow writes a summary into the prompt. That's how the model knows where we are without remembering the whole conversation.
[click] When another flow has the user's attention, cooking writes a shorter one-liner. That's how "back to cooking" makes sense to the model.
[click] Resume: "back to cooking" calls the same orchestrator we'll see in a minute, so we pick up exactly where we left off.
[click] Background: nothing to do. The notes are kept.
[click] Cancel: the user abandoned it, so drop the context, the timer and the screen state.
-->

---
class: dense
---

<div class="kicker">Flow Initiation & Tools</div>

# Intent Flow

```dart {all|1-9|11-20|8,19}
// global tool initiates the flow
@IntentTool(description: 'Start cooking a new recipe', isGlobal: true)
Future<IntentResult> startCooking(@Param('The Recipe Id to cook') int recipe_id) async {
  final recipe = await _apiClient.recipes
      .getRecipeById(recipe_id, _activeAccountId.untrackedValue);
  if (recipe == null) return IntentResult.done(["I couldn't find that recipe."]);
  _context = CookingAssistantContext(recipe);
  return _orchestrate(_context!, []);
}

// flow tool: only offered when the orchestrator arms it
@IntentTool(description: 'Step is complete')
IntentResult stepComplete() {
  final ctx = _context!;
  if (ctx.go_to_next_step() == null) {
    _clearSession();
    return IntentResult.done(['Enjoy your ${ctx.recipe_name}']);
  }
  return _orchestrate(ctx, []);
}
```

<!--
[1:00] Two kinds of tool on the same class.
[click] startCooking is global, so the agent can always call it: "let's cook the satay stir-fry". It loads the recipe and starts a fresh context.
[click] stepComplete is a flow tool. The LLM only sees it when the orchestrator arms it. It moves the context on a step, or finishes the recipe and clears the session.
[click] Neither tool decides what happens next. They update the notes and hand off to the orchestrator. Every tool returns an IntentResult: what to say, and which tools to arm next.
-->

---
class: dense
---

<div class="kicker">Orchestration</div>

# Intent Flow

```dart {all|2-4|6-13|15-23|25-28}
IntentResult _orchestrate(CookingAssistantContext ctx, List<String> messages) {
  // show the user the cooking assistant screen while orchestrating
  final path = appRouter.routerDelegate.currentConfiguration.uri.path;
  if (path != '/recipes/assistant') appRouter.go('/recipes/assistant');

  if (ctx.is_in_pre_cook) {
    orchestratedStepIndex.value = -1; // signal: scroll to the ingredients
    return IntentResult.withNextTools(
      [...messages, "Let's cook ${ctx.recipe_name}.",
       'Would you like me to read out the ingredients, or are you ready to cook?'],
      ['readIngredients', 'readyToCook'],
    );
  }

  orchestratedStepIndex.value = ctx.current_step_index; // signal: highlight the step
  final step = ctx.current_step;
  if (!identical(_timerStep, step)) {
    _stepTimer?.cancel(); // the previous step's timer is obsolete
    _timerStep = step;
    if (step.timerDuration != null) {
      _stepTimer = Timer(step.timerDuration!, () => _onStepTimerFired(ctx, step));
    }
  }

  return IntentResult.withNextTools(
    [...messages, 'Next step.', step.prompt],
    ['stepComplete'],
  );
}
```

<!--
[1:30] A process is something the expert writes in code. Every cooking tool updates the context and then calls this orchestrator. It's re-entrant: it looks at the notes and works out where we are, which is why resume can call it too.
[click] It navigates with the same router the UI uses, so the user sees what the agent is doing.
[click] Before cooking: update a signal so the screen scrolls to the ingredients, ask one question, and arm two tools. That's "read out the ingredients, or ready to cook?".
[click] "I'm ready": a signal highlights the step, and a timer is set if the step has one. When the timer fires, the flow sends an interrupt, and the app speaks first. Proactive, but not autonomous: it's still code in the app deciding to speak.
[click] Then say the step and arm stepComplete. The LLM never decides what step comes next.
-->

---
clicks: 5
---

<div class="kicker">Architecture</div>

# Dart, Flutter, Signals, Gemini

<ArchDiagram :stage="$clicks" />

<!--
[1:15] Here's how it all fits together.
[click] Voice: the AudioCoordinator is one state machine covering wake word, speech to text and text to speech, with queues so listening and speaking never overlap.
[click] AgentService runs each turn. It calls the LLM through LLMService with only the tools that are armed.
[click] The IntentRegistry is a deliberately simple, synchronous store: flows, global tools, armed tools, current flow, interrupts.
[click] Your app code: flows like CookingAssistant, with annotated tool methods and an orchestrator.
[click] Flows change app state through the same Signals stores and GoRouter the UI uses, so the screen shows what the agent did.
-->

---
clicks: 30
---

<div class="kicker">Supporting The Users Own Cognitive Loop</div>

# Human <span class="accent">IS</span> The Loop

<UserAgentLoops :stage="$clicks"  class="-mt-2" />

<!--
[2:30] Let's put it all together, from the user's side. Recipes4Me is B2C, and our key users are busy people running a home, very often mums. They multitask hard. Look at everything else on their mind.
[click] They're running a loop of their own. Trigger: I need to eat more vegetables. Carrots are nice.
[click] Reason: not sure I have carrots in the fridge, better check.
[click] Act, in the real world: open the fridge.
[click] Observe: no carrots. Eggplant and milk, but no carrots.
[click] Reason: better buy some. This is where the app can help.
[click] Act: "Hey Recipes, add carrots to the list." That's the trigger for the agent's loop.
[click] The agent reasons. The user wants carrots on the list, and there's a global tool that adds an item by name. That's the annotated tool we saw. Meanwhile the user has moved on.
[click] It acts: addShoppingListItem("carrots").
[click] It observes: two matching products in the user's favourites, Carrots 1kg and Baby Carrots 500g.
[click] It reasons: there's no way of knowing which one, so it doesn't guess. Better ask. Strictly, the code decided that, not the LLM. The expert knows two products means a question.
[click] It finishes with a question that names both options. It can't go on without the user.
[click] The question lands in the user's loop as an observation. Oh, two kinds of carrots.
[click] The user reasons with something only they know. I prefer the baby carrots, they're tender.
[click] Act: "The baby carrots." That starts a second, short agent loop. The agent observes the choice.
[click] It reasons: now there's a specific product, and a tool to add a product by id. That tool was hidden until the first one armed it.
[click] It acts: addProductToCartById.
[click] It observes: success.
[click] It reasons: tool call is good, looks like we are done.
[click] And it finishes: carrots have been added to the list.
[click] Back into the user's loop as an observation. Carrots are on the list.
[click] And the user is already onto the next thing: now I need some carrot recipes. The user runs the big loop. The agent runs short loops inside it, sharing the cognitive load, as an expert in the app's domain.
[click] "Hey Recipes, find me some good carrot recipes for next week." A new intent: not shopping any more, meal planning.
[click] The agent reasons: none of the tools it has right now fit, but it can start the recipe planning process.
[click] It acts: startPlanning(), a global tool, just like startCooking.
[click] It observes: planning has started, and meal planning's own tools are now armed, the way cooking arms stepComplete.
[click] It reasons: now there's a tool for recipes.
[click] It acts: searchForRecipeByIngredient("Carrots").
[click] It observes: there are two recipes.
[click] It reasons: it's not clear which one to add, so it doesn't guess. Better ask.
[click] And it finishes with a question: "I found carrot soup and carrot pie." The user changed context, and the agent followed. The human is the loop.
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
  <QrCode url="https://github.com/JavascriptMick/human-is-the-loop" :size="140" />
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
class: dense
---

<div class="kicker">Appendix</div>

# Further reading: multitasking and dialogue

<div class="grid grid-cols-2 gap-8 mt-3">
  <div>
    <div class="ref-group">
      <div class="kicker">Working spheres and task switching</div>
      <div class="ref"><strong>González & Mark (2004)</strong> <a href="https://doi.org/10.1145/985692.985707" target="_blank">"Constant, constant, multi-tasking craziness": Managing multiple working spheres</a>. CHI 2004.<br/><span class="why">People keep several persistent working spheres. Switching away doesn't end a task, so an assistant should keep its state and reduce the user's metawork.</span></div>
      <div class="ref"><strong>Iqbal & Horvitz (2007)</strong> <a href="https://www.microsoft.com/en-us/research/publication/disruption-recovery-computing-tasks-field-study-analysis-directions/" target="_blank">Disruption and recovery of computing tasks: Field study, analysis, and directions</a>. CHI 2007.<br/><span class="why">Interruptions carry a resumption cost. Preserving context matters more than allowing switching, and task boundaries are better moments to intervene.</span></div>
      <div class="ref"><strong>Altmann & Trafton (2002)</strong> <a href="https://doi.org/10.1207/s15516709cog2601_2" target="_blank">Memory for goals: An activation-based model</a>. Cognitive Science 26(1).<br/><span class="why">Interrupted goals decay. Retrieval cues bring them back, so an assistant should surface the right cue at the right time.</span></div>
      <div class="ref"><strong>Trafton, Altmann, Brock & Mintz (2003)</strong> <a href="https://doi.org/10.1016/S1071-5819(03)00023-5" target="_blank">Preparing to resume an interrupted task</a>. IJHCS 58(5).<br/><span class="why">Resumption improves when the next step is encoded first. A side interaction should end with a return cue: "Back to the curry: add the tomatoes now."</span></div>
    </div>
  </div>
  <div>
    <div class="ref-group">
      <div class="kicker">Multi-threaded dialogue</div>
      <div class="ref"><strong>Heeman, Yang, Kun & Shyrokov (2005)</strong> <a href="https://doi.org/10.1145/1040830.1040903" target="_blank">Conventions in human-human multi-threaded dialogues: A preliminary study</a>. IUI 2005.<br/><span class="why">People juggle conversational threads with conventions for interrupting and returning. Assistants should recognise thread shifts without forcing explicit mode changes.</span></div>
      <div class="ref"><strong>Yang & Heeman (2009)</strong> <a href="https://doi.org/10.1145/1502650.1502703" target="_blank">Context restoration in multi-tasking dialogue</a>. IUI 2009.<br/><span class="why">A short side task ("add apples" while cooking curry) can be temporary without becoming a durable switch. The system should know the return point.</span></div>
      <div class="ref"><strong>Grosz & Sidner (1986)</strong> <a href="https://aclanthology.org/J86-3001/" target="_blank">Attention, intentions, and the structure of discourse</a>. Computational Linguistics 12(3).<br/><span class="why">A local discourse segment ("green or red apples?") has its own focus without changing the user's broader activity focus.</span></div>
    </div>
  </div>
</div>

<!--
Appendix, for Q&A. The research behind the user's loop and the Intent Flow switching model.
Working spheres: people don't work in neat linear sequences, they keep several activities alive and switch between them. That's why flows background instead of ending.
Interruption research: resuming costs effort, and cues help. That's the switchToFlowContextSummary and "back to cooking".
Dialogue research: a clarifying question is a sub-conversation, not a new task.
-->

---
class: dense
---

<div class="kicker">Appendix</div>

# Further reading: initiative and timing

<div class="grid grid-cols-2 gap-8 mt-3">
  <div>
    <div class="ref-group">
      <div class="kicker">Mixed-initiative and human-AI interaction</div>
      <div class="ref"><strong>Horvitz (1999)</strong> <a href="https://www.microsoft.com/en-us/research/publication/principles-mixed-initiative-user-interfaces/" target="_blank">Principles of mixed-initiative user interfaces</a>. CHI 1999.<br/><span class="why">When to act, ask, defer or stay quiet. Under uncertainty about the user's goals, doing less correctly beats doing something specific and wrong.</span></div>
      <div class="ref"><strong>Amershi et al. (2019)</strong> <a href="https://www.microsoft.com/en-us/research/publication/guidelines-for-human-ai-interaction/" target="_blank">Guidelines for human-AI interaction</a>. CHI 2019.<br/><span class="why">Make capabilities clear, time services to context, support easy correction and dismissal, remember recent interactions, and scope behaviour when uncertain.</span></div>
    </div>
    <div class="ref-group">
      <div class="kicker">Activity-centred computing</div>
      <div class="ref"><strong>Bardram, Jeuris & Houben (2015)</strong> <a href="https://doi.org/10.1609/aimag.v36i2.2585" target="_blank">Activity-Based Computing: Computational management of activities reflecting human intention</a>. AI Magazine 36(2).<br/><span class="why">Organise around the user's activities, not apps, screens or a chat transcript. Activities nest: Christmas lunch contains the roast, the pudding and the potatoes.</span></div>
    </div>
  </div>
  <div>
    <div class="ref-group">
      <div class="kicker">Proactive AI and timing</div>
      <div class="ref"><strong>Pu et al. (2025)</strong> <a href="https://doi.org/10.1145/3706598.3713384" target="_blank">Assistance or disruption? Exploring and evaluating the design and trade-offs of proactive AI programming support</a>. CHI 2025.<br/><span class="why">Proactive help isn't automatically helpful. Poor timing breaks flow, and users do better when the assistant's context and intent are legible.</span></div>
      <div class="ref"><strong>Kuo, Sergeyuk, Chen & Izadi (2026)</strong> <a href="https://arxiv.org/abs/2601.10253" target="_blank">Developer interaction patterns with proactive AI: A five-day field study</a>. IUI 2026.<br/><span class="why">Interventions at workflow boundaries land better than mid-task ones. Be aware continuously, interrupt selectively.</span></div>
      <div class="ref"><strong>Pu et al. (2025)</strong> <a href="https://arxiv.org/abs/2507.21378" target="_blank">ProMemAssist: Exploring timely proactive assistance through working memory modeling in multi-modal wearable devices</a>. UIST 2025.<br/><span class="why">Weigh the value of helping against the cognitive cost of interrupting. Knowing what to say isn't enough, it has to be a good time to say it.</span></div>
    </div>
  </div>
</div>

<!--
Appendix, for Q&A. When should the agent speak first?
Horvitz and the Microsoft guidelines: under uncertainty, ask or do less rather than guess. That's the two-carrots question.
The proactive AI studies: interrupt at task boundaries, not mid-task. The cooking timer is the one place the app speaks first, and it fires at a step boundary.
-->

---
class: dense
---

<div class="kicker">Appendix</div>

# Design implications from the research

<blockquote class="implication-quote font-serif mt-3">
  A long-running AI assistant should not treat the conversation as the primary object. It should treat the user's ongoing activity world as the primary object, with conversation as one interaction channel into that world.
</blockquote>

<ol class="implications grid grid-cols-2 gap-x-8 gap-y-2 mt-5 text-sm">
  <li>Multiple activities can remain active without all being attended.</li>
  <li>The foreground should usually be single-focus, but the assistant's memory should be multi-threaded.</li>
  <li>Completing a task should normally release the foreground, not automatically promote another task.</li>
  <li>Side commands can be handled inline without switching durable workflow focus.</li>
  <li>Clarifying a side command creates a temporary conversational segment, not necessarily a new attended workflow.</li>
  <li>The assistant should restore the previous activity context after interruptions.</li>
  <li>Proactive interventions should be rare, valuable, and preferably timed at task boundaries.</li>
  <li>Activities should be represented by goals, state, dependencies, resources and timing, not merely by verbs or app screens.</li>
</ol>

<style>
.implication-quote {
  border-left: 3px solid var(--accent);
  background: var(--bg-2);
  border-radius: 0 10px 10px 0;
  padding: 0.7rem 1rem;
  font-size: 1rem;
  line-height: 1.5;
  color: var(--ink);
}
.implications {
  list-style: none;
  padding: 0;
  counter-reset: imp;
}
.implications li {
  counter-increment: imp;
  position: relative;
  padding-left: 1.8rem;
  margin: 0;
  line-height: 1.45;
}
.implications li::before {
  content: counter(imp);
  position: absolute;
  left: 0;
  font-family: var(--mono);
  font-weight: 800;
  color: var(--accent);
}
</style>

<!--
Appendix, for Q&A. The synthesis from the reading list.
The big one: the user's activity world is the primary object, not the chat transcript. That's why each flow keeps its own context object and the registry tracks which one holds attention.
-->
