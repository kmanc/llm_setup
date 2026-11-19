---
name: fsm-audit
description: Produce a plan to make state machines explicit
argument-hint: "[output_filename]"
---

Audit this project for implicit state machines: places where behavior depends on combinations of booleans, status strings, or scattered conditionals, especially where impossible states can occur.

Develop a plan to make each implicit state machine explicit using the appropriate tool based on the language this project is written in. Prefer changes that improve UX (no inconsistent screens), DX (readable, testable), and AX (states and allowed actions are enumerable and inspectable by agents). If those conflict, document the conflict and prioritize UX. If the state machine is so simple that making it explicit is overkill, document that as well.

Produce an inventory of the state machines and the plan to make them explicit in $0.md. For each, provide the file/location(s), the actual states and transitions, any impossible or buggy states, the plan to make it explicit, the risks and benefits of making it explicit, and any decisions I need to make regarding the plan. Order the inventory based on what makes the most sense to address; if multiple states interact, favor putting them sequential in the list. The final inventory should serve as an issues list that another AI agent can read through and use to make the changes outlined.

Constraints: Preserve existing behavior, public APIs and persisted data. Add characterization tests first, and keep tests and typechecks passing after each change.
