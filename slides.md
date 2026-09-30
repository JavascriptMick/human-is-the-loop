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

# Pandora's box is open

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
clicks: 2
---

# "Human in the loop" isn't the fix

<div class="grid grid-cols-2 gap-6 mt-8">
  <div class="card">
    <div class="kicker danger">Human in the loop</div>
    <div class="mt-1 dim text-sm">The agent owns the loop. The user is a checkpoint.</div>
    <ul class="mt-4 leading-relaxed">
      <li>The agent does the thinking, the user ticks a box</li>
      <li>The agent decides what to attend to, the user steps in when asked</li>
      <li>The user prompts, the agent goes off and creates</li>
    </ul>
  </div>
  <div v-click="1" class="card">
    <div class="kicker">Human <span class="accent">IS</span> the loop</div>
    <div class="mt-1 dim text-sm">The user owns the loop. The agent runs short loops inside it.</div>
    <ul class="mt-4 leading-relaxed">
      <li>The agent follows what the user is focused on</li>
      <li>It works through the app's own tools and screens</li>
      <li>It shares the cognitive load, then gets out of the way</li>
    </ul>
  </div>
</div>

<div v-click="2" class="mt-10 text-2xl text-center">
  The agent's loop runs <strong class="accent">inside</strong> the user's loop. Not the other way round.
</div>

<!--
[1:00] The usual answer is "put a human in the loop". But that keeps the agent in charge: it thinks, it decides, and every so often the user gets asked to approve something. That's a worse experience, not a better one.
[click] Flip it. The user is already running a loop, their day. The agent should run inside that, helping with one step at a time.
[click] That's what the title means. The human isn't a checkpoint on the agent's loop. The human IS the loop.
-->

---

# Where we're going

<div class="grid grid-cols-3 gap-5 mt-10">
  <div class="card">
    <div class="kicker">See it</div>
    <div class="text-xl font-700 mt-2">A real in-app agent</div>
    <div class="mt-2 dim">recipes4me: hands-free cooking, shopping and meal planning by voice</div>
  </div>
  <div class="card">
    <div class="kicker">Part 1 · The idea</div>
    <div class="text-xl font-700 mt-2">Two loops, four rules</div>
    <div class="mt-2 dim">How the agent's loop fits inside the user's, and what keeps it there</div>
  </div>
  <div class="card">
    <div class="kicker">Part 2 · The code</div>
    <div class="text-xl font-700 mt-2">Flutter · Signals · Gemini</div>
    <div class="mt-2 dim">Real Dart from a shipping app: tools, flows, attention, voice</div>
  </div>
</div>

<div class="mt-10 text-center text-lg">
  You'll leave with a pattern that runs on <strong class="accent">flash-lite</strong>, with 2-6 tools per turn.
</div>

<!--
[0:30] Here's the plan. First I'll show you the thing working. Then the idea: two loops and four rules. Then the code. And the payoff: this runs on a small, cheap model, because the model only ever sees a handful of tools.
-->

---
layout: center
---

<div class="grid grid-cols-[auto_1fr] gap-12 items-center">
  <PhoneFrame src="/img/agent_chat_slide_menu.png" :width="200" caption="demo video goes here" />
  <div>
    <div class="kicker">Demo</div>
    <h1>Cooking with recipes4me</h1>
    <div class="placeholder mt-6">
      TODO: record the cooking flow and drop it in as public/video/demo.mp4,<br />
      then swap to &lt;PhoneFrame src="/video/demo.mp4" video /&gt;
    </div>
    <ul class="mt-6 dim">
      <li>"Cook this recipe?" contextual launch</li>
      <li>voice: ingredients → steps → timer interrupt</li>
      <li>switch to shopping and back</li>
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
  kicker="Part 1"
  title="Two loops, four rules"
  subtitle="Why the agent belongs inside the user's loop"
/>

<!--
[0:10] So how does that work? Start with the loop every agent runs.
-->

---
clicks: 15
---

<div class="kicker">The agent's loop</div>

# Every agent is this loop

How we use a prompt hack to turn a token generator into an autonomous agent

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
[click] From the harness side it's tiny: call the model, run its tools, append.
[click] Call, execute, repeat until it responds.
-->

---
clicks: 12
---

<div class="kicker">Meet our hero</div>

# The user is running a loop too

<UserAgentLoops :stage="$clicks" class="-mt-2" />

<!--
[1:45] Now meet the person it's for. Recipes4Me is B2C, and our key users are busy people running a home, very often mums. They multitask hard. Look at everything else on their mind.
[click] They're running a loop of their own. Trigger: I need to cook dinner.
[click] Think: I feel like eggplant, do I have any?
[click] Act, in the real world: open the fridge.
[click] Observe: plenty of eggplant.
[click] Think: we made a great eggplant recipe a couple of weeks ago... what was it? Memory is fuzzy, and this is where the app can help.
[click] Act: "Hey Recipes, what was that eggplant recipe we made last week?" That's the trigger for the agent's loop.
[click] The agent thinks. It's a recipe expert, and it knows there's a tool to search meal plans. Meanwhile the user has moved on.
[click] It acts: searchMealPlansForRecipe with "eggplant".
[click] It observes: no meal plan for last week.
[click] It thinks: maybe the user has the dates mixed up.
[click] And it finishes with a helpful answer, which lands in the user's loop as an observation.
[click] Oh right, 3 weeks ago. The user runs the big loop. The agent runs short loops inside it, sharing the cognitive load, as an expert in the app's domain.
-->

---
clicks: 4
---

# Four rules keep the agent inside the user's loop

<div class="mt-6">
  <BoundaryCards :stage="$clicks" />
</div>

<!--
[1:30] So what keeps the recipes4me loop from going the way of those headlines, and makes it useful at the same time? Four rules. Everything in part 2 is labelled with one of these.
[click] Bounded by the app: the tools are the app's own functions, over the app's data, driving the same screens. If the user can't do it in the app, the agent can't either.
[click] Workflows in code: known processes like cooking a recipe are written in code. The LLM doesn't decide the next step.
[click] Arm and gate: the LLM only sees a couple of tools at a time, and anything else is rejected.
[click] Follow attention: the loop follows what the user is focused on and keeps the other things they had going. Let's look at that one.
-->

---
clicks: 2
---

<div class="kicker">Rule 4 · Follow attention</div>

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
clicks: 6
class: '!py-6'
---

<h2 class="!mb-2 !font-800" style="color: var(--ink)">The demo, from the agent's side</h2>

<div class="h-[440px]">
  <LoopSimulator :stage="$clicks" />
</div>

<!--
[2:30] Let's replay the demo from the agent's side.
[click] "let's cook the satay stir-fry". The LLM can only see the global tools, so it picks startCooking. The cooking flow now holds attention and has armed exactly two tools.
[click] "I'm ready". readyToCook runs, the flow reads step 1 and sets a timer. Only stepComplete is armed now.
[click] 15 minutes later, the app itself interrupts. No user input, but it's still code in the app deciding to speak.
[click] I get distracted and ask to add satay sauce to the shopping list. Shopping takes my attention, and cooking is backgrounded with its state kept.
[click] "back to cooking", switchToFlow, and we pick up at step 1.
[click] The whole thing in one line. All four rules are in there. Now let's see how it's built.
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
[0:15] Now the code. All of this is real Dart from the framework and the recipes app, cut down to fit. Each slide is tagged with the rule it implements.
-->

---
clicks: 5
---

<div class="kicker">The big picture</div>

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

<div class="kicker">Rule 1 · Bounded by the app</div>

# A tool is just an annotated app method

````md magic-move {lines: true}
```dart
// cooking_assistant.dart - you write this
@IntentTool(
  description:
      'Start cooking a new recipe. Requires a recipe_id. Do not call this '
      'if a cooking workflow is already in progress.',
  isGlobal: true,
)
FutureOr<IntentResult> startCooking(
  @Param('The Recipe Id to cook') int recipe_id,
) {
  ...
}
```

```dart
// cooking_assistant.intent.g.dart - build_runner writes this
agent.registerTool(
  IntentToolRegistration(
    toolName: 'startCooking',
    description:
        '''Start cooking a new recipe. Requires a recipe_id. Do not call this if a cooking workflow is already in progress.''',
    parametersSchema: {
      'type': 'object',
      'properties': <String, dynamic>{
        'recipe_id': <String, dynamic>{
          'type': 'integer',
          'description': '''The Recipe Id to cook''',
        },
      },
      'required': ['recipe_id'],
    },
    handler: (args) => startCooking((args['recipe_id'] as num).toInt()),
    isGlobal: true,
  ),
);
```
````

<!--
[1:30] A tool is just a method on your class. The description is the prompt. isGlobal: true means it's always offered, like a main menu item. isGlobal: false means it's only offered when a flow arms it.
[click] build_runner turns Dart types into JSON Schema and generates the registration. No hand-written schemas, and the analyzer checks everything.
-->

---

<div class="kicker">Rule 1 · Bounded by the app</div>

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

<div class="kicker">Rule 2 · Workflows in code</div>

# A flow owns one user intent

```dart {all|2|3-4|5|7-12}
abstract class IntentFlow {
  String get flowName;                    // 'cooking', 'shopping'
  String get switchToFlowContextSummary;  // one-liner when it's in the background
  String get currentFlowContextSummary;   // one-liner when it's current
  bool get flowIsActive;                  // dirty context? ("in progress: step 3")

  /// Flip back to this flow using its saved context, no mutation
  FutureOr<IntentResult> resumeThisFlow();
  /// Attention is moving away: drop transient state (pending confirmations)
  FutureOr<void> backgroundThisFlow();
  /// The user abandoned it: drop ALL context so flowIsActive goes false
  FutureOr<void> cancelThisFlow();
}
```

<div v-click="5" class="mt-4 dim">
A flow is a <strong>re-entrant orchestrator</strong> for one user intent. It's related to sagas, actors, dialogue policies and FSMs.
</div>

<!--
[1:00] Every flow implements this.
[click] Name. [click] Two summaries: these are how the LLM knows about flows it isn't currently in. [click] flowIsActive: conventionally `_context != null`.
[click] Resume, background, cancel: the lifecycle of the user's attention.
-->

---

<div class="kicker">Rule 3 · Arm and gate</div>

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
class: dense
---

<div class="kicker">Rule 2 · Workflows in code</div>

# The orchestrator drives the steps and the screen

```dart {all|2-4|6-13|14-23|24-27}
IntentResult _orchestrate(CookingAssistantContext ctx, List<String> messages) {
  // agent 'shows' the user the cooking assistant screen while orchestrating
  final path = appRouter.routerDelegate.currentConfiguration.uri.path;
  if (path != '/recipes/assistant') appRouter.go('/recipes/assistant');

  if (ctx.is_in_pre_cook) {
    orchestratedStepIndex.value = -1;          // signal → screen scrolls to ingredients
    return IntentResult.withNextTools(
      [...messages, "Let's cook ${ctx.recipe_name}.",
       'Would you like me to read out the ingredients, or are you ready to cook?'],
      ['readIngredients', 'readyToCook'],
    );
  }

  orchestratedStepIndex.value = ctx.current_step_index;   // highlight the step
  final stepAtSet = ctx.current_step;
  if (!identical(_timerStep, stepAtSet)) {
    _stepTimer?.cancel();                      // previous step's timer is obsolete
    _timerStep = null;
    if (stepAtSet.timerDuration != null) {
      _stepTimer = Timer(stepAtSet.timerDuration!, () => _onStepTimerFired(ctx, stepAtSet));
      _timerStep = stepAtSet;
    }
  }
  return IntentResult.withNextTools(
    [...messages, ctx.current_step_index == 0 ? 'First step' : 'next step', ctx.current_step.prompt],
    ['stepComplete'],
  );
}
```

<!--
[1:30] The heart of a flow. Every tool handler changes the context and then calls this. It's re-entrant: it looks at the context and works out where we are.
[click] It navigates, with the same router the UI uses.
[click] Pre-cook: update a signal so the screen scrolls, ask one question, arm two tools.
[click] Cooking: highlight the step and set a timer if the step has one.
[click] Then say the step and arm stepComplete. The LLM never decides what step comes next.
-->

---
class: dense
---

<div class="kicker">Rule 3 · Arm and gate</div>

# The LLM can only call what's armed

<div class="grid grid-cols-[0.8fr_1.2fr] gap-6 mt-2">
<div class="flex flex-col gap-2 text-sm">
  <div class="card !py-2"><span class="kicker">1 · input</span><br/>voice or typed text → <code>processUserInput</code></div>
  <div class="card !py-2"><span class="kicker">2 · fast path</span><br/>regex on armed tools, e.g. "next" → no LLM call</div>
  <div class="card !py-2"><span class="kicker">3 · LLM</span><br/>system prompt + flow summaries + history + <strong>armed tools only</strong></div>
  <div class="card !py-2"><span class="kicker">4 · gate</span><br/>reject anything not armed</div>
  <div class="card !py-2"><span class="kicker">5 · apply</span><br/>move attention, arm the next tools, speak</div>
</div>

```dart {all|7-15|17-21}
Future<IntentResult?> _executeIntentTool(
  String toolName,
  Map<String, dynamic> args,
) async {
  final registration = _registry.registrationFor(toolName);

  // Availability gate: only currently-advertised tools may execute.
  // The LLM is constrained to the advertised set already; this rejects
  // stale calls from concurrent turns, direct-execution bypasses and
  // provider glitches. Intents may therefore assume an intent tool
  // only runs while its flow is armed.
  if (!_registry.isToolArmed(toolName)) {
    _log.warning('Tool $toolName not currently available - rejecting');
    return null;
  }

  final owningFlow = _registry.flowNameForTool(toolName);
  final result = await registration!.handler(args);

  await _applyIntentResult(result, owningFlow);
  return result;
}
```
</div>

<!--
[1:30] AgentService, one turn, in five steps.
[click] The availability gate. Even if the model hallucinates a tool name, or a stale turn arrives late, it can't run anything that isn't armed. This is where "bounded" is enforced in code.
[click] Then apply the result: attention goes to whichever flow owns the tool, and that flow's requested tools get armed.
-->

---
class: dense
---

<div class="kicker">Rule 4 · Follow attention</div>

# Attention moves in exactly one place

<div class="grid grid-cols-[1.25fr_1fr] gap-6">

```dart {1-12|14-23}
// every LLM turn: tell the model what's going on
final sessionPrompts = [
  if (flows.currentFlow case final flow?)
    "Current Workflow: ${flow.currentFlowContextSummary}",
  ...flows.otherActiveFlows.map(
    (flow) =>
        "Other In Progress Workflow: ${flow.switchToFlowContextSummary}",
  ),
  ...flows.inactiveFlows.map(
    (flow) => "Inactive workflows: ${flow.flowName}",
  ),
];

// the ONLY place attention moves
Future<void> _transferAttention(String? newFlowName) async {
  if (newFlowName == null ||
      newFlowName.isEmpty ||
      newFlowName == _registry.currentFlowName) {
    return;
  }
  await _registry.currentFlow?.backgroundThisFlow();
  _registry.setCurrentFlow(newFlowName);
}
```

<div class="flex flex-col gap-4">
  <AttentionStack
    :flows="[
      { name: 'shopping', state: 'current', summary: '1 item added' },
      { name: 'cooking', state: 'background', summary: 'cooking Quick Pork Satay, step 1 of 8' },
      { name: 'whats for dinner', state: 'inactive' },
    ]"
  />
  <div class="card text-sm">
    <div class="kicker">system tools</div>
    <div class="mt-1"><code>switchToFlow</code> and <code>cancelFlow</code> are generated each turn from live flow state. Their enums only list flows that are actually in progress.</div>
  </div>
</div>

</div>

<!--
[1:30] How does the model know about the cooking session while we're shopping? Every turn it gets a summary of every flow, written by the flow itself.
[click] Attention only moves in one place. The outgoing flow is always backgrounded exactly once: tool execution, interrupts and switchToFlow all go through here.
-->

---

<div class="kicker">Rule 2 · Workflows in code</div>

# Interrupts: the app speaks first

```dart {all|3|4-17}
void _onStepTimerFired(CookingAssistantContext ctx, CookingStep firingStep) {
  // Ignore irrelevant or superseded timer events
  if (identical(ctx, _context) && identical(firingStep, ctx.current_step)) {
    _timerInterruptStream.add(
      InterruptIntent(
        flowName: flowName,
        result: IntentResult.withNextTools(
          [
            firingStep.timerPrompt ??
                "It's been ${_speakDuration(firingStep.timerDuration!)} "
                '- have you checked the ${firingStep.name}?',
          ],
          ['stepComplete'],     // re-arm the same follow-up; the reply resolves it
        ),
        marker: '[timer elapsed: ${firingStep.name}]',
      ),
    );
  }
}
```

<div v-click="3" class="mt-4 card">
Proactive, but <strong>not autonomous</strong>: the flow decides when to speak, and AgentService drains the interrupt queue when it's safe to.
</div>

<!--
[1:00] Sometimes the agent should speak first. The timer fires, the flow pushes an InterruptIntent, and AgentService speaks it when nothing else is going on. Attention moves back to cooking.
[click] Only if the timer still belongs to the current step.
[click] An interrupt is just an IntentResult without a tool call, handled the same way.
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

<div class="kicker">Rule 1 · Bounded by the app</div>

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

---

<div class="kicker">The payoff</div>

# Tokenomics that add up

<div class="grid grid-cols-3 gap-5 mt-10">
  <div v-click class="card"><div class="text-4xl font-800 accent">2-6</div><div class="mt-2">armed tools per turn, not the whole app. <code>narrowToolList</code></div></div>
  <div v-click class="card"><div class="text-4xl font-800 accent">flash-lite</div><div class="mt-2">a small, cheap model works because it only has to <strong>classify intent</strong></div></div>
  <div v-click class="card"><div class="text-4xl font-800 accent">0</div><div class="mt-2">LLM calls for regex fast paths, direct actions and interrupts</div></div>
</div>

<!--
[0:30] The payoff I promised at the start: small context, a small model, and lots of turns that never call the LLM. That's what makes this affordable for consumer apps.
-->

---

# Takeaways

<div class="grid grid-cols-[1fr_auto] gap-10 items-center mt-6">
  <div class="flex flex-col gap-4 text-xl">
    <div v-click>① <strong>Bounded by the app</strong>: put the agent loop inside the app. Its tools and data end where the app's do.</div>
    <div v-click>② <strong>Workflows in code</strong>: use the LLM for intent, write the process in code.</div>
    <div v-click>③ <strong>Arm and gate</strong>: offer a few tools at a time, and reject anything else.</div>
    <div v-click>④ <strong>Follow attention</strong>: one current flow, others backgrounded, all remembered.</div>
  </div>
  <QrCode url="https://acedant.ai" :size="170" caption="TODO: final link" />
</div>

<div v-click class="mt-10 text-2xl text-center">
  The human isn't a checkpoint on the agent's loop. The human <span class="accent">IS</span> the loop.
</div>

<!--
[1:30] The same four rules, to take home.
[click] [click] [click] [click]
[click] And back to where we started: the human isn't a checkpoint on the agent's loop. The human IS the loop.
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
