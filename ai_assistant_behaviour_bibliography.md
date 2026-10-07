# Bibliography: AI Assistant Behaviour, Working Spheres, Task Switching, and Mixed-Initiative Interaction

This bibliography collects the papers and studies considered in the discussion about long-running, memory-possessing AI assistants: assistants that understand human activities, support multiple active tasks, manage interruptions, and interact with people in a human-centred way.

The emphasis here is on **behaviour and interaction design**, not implementation architecture.

---

## Core multitasking and working-sphere research

### 1. González & Mark — Multiple working spheres

**González, V. M., & Mark, G. (2004). _“Constant, constant, multi-tasking craziness”: Managing multiple working spheres._ Proceedings of CHI 2004, 113–120.**

Used for the idea that people do not naturally work in neat, linear task sequences. Instead, they maintain multiple persistent **working spheres**: larger goal-directed activity contexts with their own resources, people, timeframes, and state.

Key relevance:

- Humans switch frequently between ongoing work spheres.
- Switching away from a task does not mean the task has ended.
- A good assistant should preserve the state of suspended activities.
- The assistant can reduce the user's “metawork”: remembering where things are, what remains, and what needs attention.

Link: <https://doi.org/10.1145/985692.985707>

---

### 2. Iqbal & Horvitz — Disruption and task recovery

**Iqbal, S. T., & Horvitz, E. (2007). _Disruption and recovery of computing tasks: Field study, analysis, and directions._ Proceedings of CHI 2007.**

Used for the interruption/resumption problem: task switching is not just a state change. Humans pay a cognitive cost when they have to reconstruct what they were doing.

Key relevance:

- Interruptions create resumption costs.
- Preserving context matters more than simply allowing switching.
- Assistants should support return-to-task cues.
- Task boundaries are better times for intervention than mid-task interruptions.

Link: <https://www.microsoft.com/en-us/research/publication/disruption-recovery-computing-tasks-field-study-analysis-directions/>

---

### 3. Altmann & Trafton — Memory for goals

**Altmann, E. M., & Trafton, J. G. (2002). _Memory for goals: An activation-based model._ Cognitive Science, 26(1), 39–83.**

Used as a cognitive foundation for suspended intentions, task resumption, retrieval cues, and why people can lose the thread after interruption.

Key relevance:

- Goals decay or become less accessible when interrupted.
- Retrieval cues help suspended goals become active again.
- Assistants should remember and surface the right cues at the right time.

Link: <https://doi.org/10.1207/s15516709cog2601_2>

---

### 4. Trafton et al. — Preparing to resume interrupted tasks

**Trafton, J. G., Altmann, E. M., Brock, D. P., & Mintz, F. E. (2003). _Preparing to resume an interrupted task: Effects of prospective goal encoding and retrospective rehearsal._ International Journal of Human-Computer Studies, 58(5), 583–603.**

Used for the idea that when a user is briefly diverted, the system should help them recover the prior activity context.

Key relevance:

- Resumption improves when people encode what they will need to do next before the interruption.
- Assistants can act as an external support for resumption.
- A side interaction should often end with a lightweight return cue: “Back to the curry: add the tomatoes now.”

Link: <https://doi.org/10.1016/S1071-5819(03)00023-5>

---

## Multi-threaded dialogue and discourse structure

### 5. Heeman et al. — Human-human multi-threaded dialogues

**Heeman, P. A., Yang, F., Kun, A. L., & Shyrokov, A. (2005). _Conventions in human-human multi-threaded dialogues: A preliminary study._ Proceedings of IUI 2005, 293–295.**

Used for the idea that humans can manage multiple conversational threads and use conventions to interrupt, suspend, and return to dialogue topics.

Key relevance:

- Humans do not always converse in a single linear thread.
- Urgency and context affect whether and how people switch threads.
- Assistants should recognise thread shifts without forcing explicit mode changes every time.

Link: <https://doi.org/10.1145/1040830.1040903>

---

### 6. Yang & Heeman — Context restoration in multi-tasking dialogue

**Yang, F., & Heeman, P. A. (2009). _Context restoration in multi-tasking dialogue._ Proceedings of IUI 2009, 373–377.**

Used directly for the “add apples while cooking curry” problem: a short real-time side task can interrupt an ongoing task, but the system should support return to the prior context.

Key relevance:

- Multi-tasking dialogue often requires restoring context after an embedded side task.
- A clarification subdialogue can be temporary without becoming a durable task switch.
- The system should know the return point after the side interaction closes.

Link: <https://doi.org/10.1145/1502650.1502703>

---

### 7. Grosz & Sidner — Attention, intentions, and discourse structure

**Grosz, B. J., & Sidner, C. L. (1986). _Attention, intentions, and the structure of discourse._ Computational Linguistics, 12(3), 175–204.**

Used for separating the structure of the conversation from the broader intentional/activity structure. This supports the distinction between a temporary conversational segment and a durable workflow focus.

Key relevance:

- A local discourse segment can have its own focus.
- That does not necessarily mean the user's broader activity focus has changed.
- “Green or red apples?” can be a subordinate conversational segment inside the broader context of cooking curry.

Link: <https://aclanthology.org/J86-3001/>

---

## Mixed-initiative and human-AI interaction behaviour

### 8. Horvitz — Principles of mixed-initiative user interfaces

**Horvitz, E. (1999). _Principles of mixed-initiative user interfaces._ Proceedings of CHI 1999.**

Used for reasoning about when the assistant should act, ask, defer, interrupt, or stay quiet.

Key relevance:

- Initiative can shift between human and system.
- The system should reason under uncertainty about the user's goals and attention.
- The cost of a wrong action or interruption matters.
- Under uncertainty, doing less correctly is often better than doing something specific and wrong.

Link: <https://www.microsoft.com/en-us/research/publication/principles-mixed-initiative-user-interfaces/>

---

### 9. Amershi et al. — Guidelines for human-AI interaction

**Amershi, S., Weld, D., Vorvoreanu, M., Fourney, A., Nushi, B., Collisson, P., Suh, J., Iqbal, S., Bennett, P. N., Inkpen, K., Teevan, J., Kikin-Gil, R., & Horvitz, E. (2019). _Guidelines for human-AI interaction._ Proceedings of CHI 2019.**

Used as a modern design guideline source for AI systems that must behave around uncertainty, user control, context, timing, memory, and adaptation.

Key relevance:

- Make clear what the system can do.
- Time services based on context.
- Support efficient invocation, dismissal, correction, and control.
- Remember recent interactions and adapt cautiously over time.
- Scope behaviour when uncertain.

Link: <https://www.microsoft.com/en-us/research/publication/guidelines-for-human-ai-interaction/>

---

## Activity-centred computing

### 10. Bardram, Jeuris & Houben — Activity-Based Computing

**Bardram, J. E., Jeuris, S., & Houben, S. (2015). _Activity-Based Computing: Computational management of activities reflecting human intention._ AI Magazine, 36(2), 63–72.**

Used for the idea that systems should organise around meaningful human activities rather than applications, screens, or a single chat history.

Key relevance:

- The primary object should be the user's ongoing activity world, not the conversation transcript.
- Activities may contain subactivities.
- “Cooking Christmas lunch” can contain roast, pudding, potatoes, and serving as coordinated subactivities.
- Multiple instances of a task type should be understood by their goals and context, not merely by their verb.

Link: <https://doi.org/10.1609/aimag.v36i2.2585>

---

## Proactive AI and timing of assistance

### 11. Pu et al. — Assistance or disruption?

**Pu, K., Lazaro, D., Arawjo, I., Xia, H., Xiao, Z., Grossman, T., & Chen, Y. (2025). _Assistance or disruption? Exploring and evaluating the design and trade-offs of proactive AI programming support._ Proceedings of CHI 2025.**

Used for the point that proactive assistance can improve efficiency but also disrupt users. The timing, visibility, and context of intervention matter.

Key relevance:

- Proactive assistance is not automatically helpful.
- Poorly timed interventions can disrupt flow.
- Users benefit when the assistant's context and intention are legible.
- Task boundaries are usually better moments for intervention than mid-task interruptions.

Link: <https://doi.org/10.1145/3706598.3713384>

---

### 12. Kuo et al. — Developer interaction patterns with proactive AI

**Kuo, N., Sergeyuk, A., Chen, V., & Izadi, M. (2026). _Developer interaction patterns with proactive AI: A five-day field study._ IUI 2026 / arXiv.**

Used for the claim that interventions at workflow boundaries are better received than mid-task interventions.

Key relevance:

- Proactive AI can help when surfaced at natural workflow boundaries.
- Mid-task interventions are more likely to be dismissed or experienced as disruptive.
- The assistant should be aware continuously but interrupt selectively.

Link: <https://arxiv.org/abs/2601.10253>

---

### 13. Pu et al. — ProMemAssist

**Pu, K., Zhang, T., Sendhilnathan, N., Freitag, S., Sodhi, R., & Jonker, T. (2025). _ProMemAssist: Exploring timely proactive assistance through working memory modeling in multi-modal wearable devices._ UIST 2025 / arXiv.**

Used for the idea that a long-running assistant should balance the value of helping against the cognitive cost of interrupting.

Key relevance:

- Timely assistance depends on understanding the user's current cognitive and activity context.
- The assistant should not merely know what to say; it should judge whether now is a good time to say it.
- Working-memory modelling is one possible way to reason about opportune moments for assistance.

Link: <https://arxiv.org/abs/2507.21378>

---

## Practical reading order

For building a behavioural model of a long-running AI assistant, read in this order:

1. González & Mark (2004) — working spheres.
2. Yang & Heeman (2009) — context restoration in multi-tasking dialogue.
3. Grosz & Sidner (1986) — discourse segments, intentions, and attention.
4. Iqbal & Horvitz (2007) — interruption and recovery.
5. Horvitz (1999) — mixed-initiative interaction.
6. Bardram, Jeuris & Houben (2015) — activity-based computing.
7. Pu et al. (2025), Kuo et al. (2026), and Pu et al. (2025 ProMemAssist) — modern proactive-AI timing.

---

## Design implications drawn from the bibliography

The main synthesis from these sources is:

> A long-running AI assistant should not treat the conversation as the primary object. It should treat the user's ongoing activity world as the primary object, with conversation as one interaction channel into that world.

Important behavioural principles:

1. **Multiple activities can remain active without all being attended.**
2. **The foreground should usually be single-focus, but the assistant's memory should be multi-threaded.**
3. **Completing a task should normally release the foreground, not automatically promote another task.**
4. **Side commands can be handled inline without switching durable workflow focus.**
5. **Clarifying a side command creates a temporary conversational segment, not necessarily a new attended workflow.**
6. **The assistant should restore the previous activity context after interruptions.**
7. **Proactive interventions should be rare, valuable, and preferably timed at task boundaries.**
8. **Activities should be represented by goals, state, dependencies, resources, and timing — not merely by verbs or app screens.**

