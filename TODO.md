# Todo

(done) - the scenario presented in the users loop (slide 8) is lame, instead of finding no data, the agent should find 2 recipes and prompt the user to specify which one
(todo) - recut the demo video with no music and maybe cut the transitions
(done) - big slide reorder

1. Title
2. TLDR
3. Problem: The last 3 months
4. Problem: HITL is not enough
5. Problem: The Agentic Loop
   --> the user is only involved at the start and the end -->
6. Solution: Principles
   --> how do I make this work in practice ? -->
7. Solution: Demo
8. Solution: Overview (new slide)

```
# The Recipes4Me Solution

## Flutter Agent Framework

- Voice Capable
  -- On device STT + TTS
  -- Wake words

- Intent Tools (Principle 1)
  -- Global tools that are available at all times
  -- A bit like main menu actions
  -- Quick actions or kick off longer Intent Flows
  -- e.g. add item to cart, start meal planning

- Intent Flows (Principle 1)
  -- Multi-step, multi-conversation tasks
  -- Usually stateful
  -- Can be switched in/out of the foreground while retaining state (Principle 2)
  -- A bit like Wizard style UX
  -- e.g. cook a specific recipe, plan my weekly meals

## Agent Enhanced App

- Assistant classes for each area of expertise (Principle 3)
  -- e.g. shopping, meal planning, cooking
  -- A bit like skills
  -- Annotate public methods as IntentTools
  -- Optionally implement 'IntentFlow' interface + state and orchestration
  -- Use store methods to mutate state (Principle 4)
  -- Use routing to navigate user to relevant parts of the UI (Principle 4)
```

10. Code: Annotating an IntentTool (current page 12, lose the UserAgentLoops mini view)
11. Code: CookingAssistant - flow registrations
    -- flowIsActive
    -- flowName
    -- switchToFlowContextSummary
    -- currentFlowContextSummary
    -- backgroundThisFlow
    -- cancelThisFlow
    12: Code: CookingAssistant - tools
    -- startCooking (IntentTool)
    -- stepComplete (IntentTool)

12. Code: CookingAssistant - orchestration
    -- \_orchestrate

```dart
  IntentResult _orchestrate(
    CookingAssistantContext ctx,
    List<String> messages,
  ) {
    _log.info('Orchestrating with context: $ctx');

    // agent 'shows' the user the cooking assistant screen while orchestrating
    if (appRouter.routerDelegate.currentConfiguration.uri.path !=
        '/recipes/assistant') {
      appRouter.go('/recipes/assistant');
    }

    if (ctx.is_in_pre_cook) {
      // sentinel: no current step — the screen scrolls to the ingredients card
      orchestratedStepIndex.value = -1;
      return IntentResult.withNextTools(
        [
          ...messages,
          "Let's cook ${ctx.recipe_name}.",
          "Would you like me to read out the ingredients, or are you ready to cook?",
        ],
        ['readIngredients', 'readyToCook'],
      );
    }
    //...
  }
```

13. Solution: Architecture
14. BIG FINISH!: Supporting the Cognitive loop (no hide)...
15. Learnings
16. Thank You
