# Software Requirements Specification

## GenMorphics AI — Annotation Workforce Management System

A requirements specification developed following Roger S. Pressman,
_Software Engineering: A Practitioner's Approach_, Chapter 8 — Requirements
Engineering.

---

## Contents

| Phase | Document | What it establishes |
|---|---|---|
| 1 | [Inception](01-inception.md) | The problem, the stakeholders, their conflicting viewpoints and how those were negotiated, the manual system being replaced, and the system proposed |
| 2 | [Quality Function Deployment](02-qfd.md) | Requirements sorted by the kind of satisfaction each produces — normal, expected, exciting |
| 3 | [Usage Scenarios](03-usage-scenarios.md) | Each actor followed through their work, in order, including what happens when a step fails |
| 4 | [Use Case Diagram](04-use-case-diagram.md) | The work decomposed into subsystems and use cases, from Level 0 to Level 1.8 |
| 5 | [Activity Diagram](05-activity-diagram.md) | The flow of control through each subsystem, with its decisions and failing paths |
| 6 | [Swimlane Diagram](06-swimlane-diagram.md) | The same flows partitioned by who performs each step, making every handoff explicit |

---

## How the phases connect

Each phase consumes the one before it, and the trace is explicit in both
directions.

```
   §1.2  Viewpoints            what each stakeholder wants
     │
     ├──▶ §1.3  Negotiations   how their conflicts were resolved
     │       │
     │       └──▶ §2.3  Exciting requirements, most of which
     │                  fell out of these resolutions
     │
     └──▶ §2.1  Normal requirements, per stakeholder
             │
             └──▶ §3  Usage scenarios — the same requirements
                     │   as sequences of action
                     │
                     └──▶ §4  Use cases  ──[Reference: 3.x]──┐
                             │                               │
                             ├──▶ §5  Activity flows ────────┘
                             │        (what happens next)
                             │             │
                             └──▶ §6  Swimlanes ◀────────────┘
                                      (who does it)
```

A use case in §4 cites the scenario in §3 that required it. An activity flow in
§5 cites the use case in §4 it realises. A swimlane in §6 partitions the flow of
§5 by the actors §4 declared — same level numbers throughout, so §4, §5 and §6
may be read side by side. Nothing appears in the later phases that no stakeholder
asked for in the first.

---

## Reading order

Read straight through for the argument. For a specific question:

- **Why does the system exist?** §1.1 (the problem), §1.4 (the manual system it replaces)
- **Who uses it, and what may each do?** §1.1 Fig IF.1, and Fig IF.4 for the capability boundaries
- **Why was something decided the way it was?** §1.3 — sixteen conflicts, each with the reasoning
- **Why was something *not* built?** §1.3, Requirements withdrawn — eleven, three of them the owner's own requests
- **What does a person actually do?** §3, by actor
- **What must the system do?** §1.5 by capability, §4 by use case
- **In what order, and what happens when it fails?** §5
- **Whose responsibility is each step, and what can no one override?** §6

---

## Conventions

- **⚠** marks a failing path — what happens when a step does not succeed.
- `[Reference: 3.x]` links a use case to the scenario that required it.
- Figures are numbered `Fig IF.n` within Inception.
- Diagrams are drawn in text: `( )` start and end, `[ ]` an action,
  `< >` a decision, `═══` a branch taken on failure.
- The specification is written as though it preceded construction. It states
  what the system is required to do, not what any implementation currently does.
