# Software Requirements Specification

## GenMorphics AI — Annotation Workforce Management System

**Phase 6: Swimlane Diagram**

**Method:** Pressman, _Software Engineering: A Practitioner's Approach_ —
Requirements Engineering. Continues from [Phase 1: Inception](01-inception.md),
[Phase 2: QFD](02-qfd.md), [Phase 3: Usage Scenarios](03-usage-scenarios.md),
[Phase 4: Use Case Diagram](04-use-case-diagram.md), and
[Phase 5: Activity Diagram](05-activity-diagram.md).

---

# 6. Swimlane Diagram

The activity diagrams of §5 show the order in which things happen. They do not
show *who does them*. A swimlane diagram takes those same flows and partitions
them by responsibility: each actor receives a lane, every action is placed in the
lane of whoever performs it, and the arrows that cross a lane boundary are the
handoffs.

This matters for reasons the activity diagrams cannot express. An action drawn in
the System lane is one no person performs — and therefore one no person can be
persuaded to skip. A flow whose steps all fall in a single lane needs no
coordination to complete. A flow that crosses four lanes has three places where it
can stall waiting on someone. **The lane boundary is where a requirement about
authority lives.**

Each diagram below carries the same level number as the use case in §4 and the
activity diagram in §5, so the three may be read side by side. Every diagram is
followed by a **Handoff List** naming each crossing, what passes at it, and why
the responsibility changes hands there.

**Lanes** are drawn from the actors declared in §4 at the corresponding level. No
lane appears here that is not an actor there.

Notation follows §5: `( )` start and end, `[ ]` an action, `< >` a decision,
`═══` a branch taken when something fails. Lanes are separated by `│` and a
crossing is drawn as an arrow passing through that boundary.

---

## Level 1.1: Access & Authorization

**Name:** Access & Authorization
**Reference:** Use Case Diagram Level 1.1, Activity Diagram Level 1.1
**Lanes:** Person (Owner, Worker, any Admin) · Identity Provider · System

```
      PERSON            │   IDENTITY PROVIDER   │           SYSTEM
                        │                       │
      ( Start )         │                       │
          │             │                       │
          ▼             │                       │
 [ Request a session ]──┼──▶[ Authenticate ]    │
                        │         │             │
                        │         └─────────────┼──▶< Account exists? >
                        │                       │        │
                        │                       │        ├── No ──┐
                        │                       │        │        ▼
                        │                       │        │  [ Create account ]
                        │                       │        │  [ holding no     ]
                        │                       │        │  [ authority      ]
                        │                       │        │        │
                        │                       │        └─ Yes ──┤
                        │                       │                 ▼
                        │                       │        < Account active? >
                        │                       │           │           │
                        │                       │      No ══╝           │ Yes
                        │                       │           ║           ▼
                        │                       │           ║  [ Load roles and ]
                        │                       │           ║  [ permissions    ]
                        │                       │           ║           │
                        │                       │           ║           ▼
                        │                       │           ║  [ Establish       ]
                        │                       │           ║  [ session         ]
                        │                       │           ║           │
                        │                       │           ▼           ▼
  [ Sees the appeal ]◀──┼───────────────────────┼──[ Deny;      ]  [ Present only  ]
  [ route, and      ]   │                       │  [ offer the  ]  [ permitted     ]
  [ nothing else    ]   │                       │  [ appeal     ]  [ capabilities  ]
          ║             │                       │  [ route      ]         │
          ║             │                       │                         │
  [ Sees only what ]◀───┼───────────────────────┼─────────────────────────┘
  [ they may do    ]    │                       │
          │             │                       │
          ▼             │                       │
      ( End )           │                       │
```

**Level 1.1: Access & Authorization**

**Handoff List:**

1. **Person → Identity Provider.** The person's credentials never enter this
   system. The request leaves for the provider and what returns is an assertion of
   identity. Drawing authentication in a lane the system does not own is the whole
   of the requirement: there is no lane here in which a password could be stored,
   because there is no step in which one is received.

2. **Identity Provider → System.** What crosses back is an identity, not an
   authorization. Every remaining decision happens in the System lane, which is why
   a valid provider account still yields no session when the account is suspended —
   the two questions are answered by two different parties.

3. **System lane, entirely.** Account creation, the active check, role loading and
   capability filtering all fall in one lane with no crossing. No administrator
   participates in a first sign-in and none can intervene in it. An account is
   created holding no authority because nobody is present to grant any.

4. **System → Person, on the failing path.** ⚠ What crosses to a suspended person
   is the appeal route and nothing else. The lane boundary is the guarantee: the
   suspended person's lane receives no list of what they might otherwise have
   reached.

5. **System → Person, on the succeeding path.** Only permitted capabilities cross.
   Unpermitted ones are not sent and refused in the person's lane — they never
   cross at all. Silent-hide is a statement about which lane the filtering happens
   in.

---

## Level 1.2: Workforce Management

**Name:** Workforce Management
**Reference:** Use Case Diagram Level 1.2, Activity Diagram Level 1.2
**Lanes:** Worker · HR Admin · System · Scheduler

```
        WORKER           │        HR ADMIN         │        SYSTEM
                         │                         │
      ( Start )          │                         │
          │              │                         │
          ▼              │                         │
 [ Enter identity,   ]   │  [ Enter the same on ]  │
 [ qualifications,   ]   │  [ a worker's behalf ]  │
 [ banking details   ]   │           │             │
          │              │           │             │
          └──────────────┼───────────┴─────────────┼──▶< Identity already ]
                         │                         │   < on file?         >
                         │                         │        │        │
                         │                         │  Yes ══╝        │ No
                         │                         │        ║        ▼
                         │                         │        ║ [ Store record, ]
                         │                         │        ║ [ naming who    ]
                         │                         │        ║ [ made the      ]
                         │                         │        ║ [ change        ]
                         │                         │        ▼        │
 [ Sees the reason ]◀────┼─────────────────────────┼─[ Refuse;  ]    │
 [ and corrects it ]     │                         │ [ state the]    │
          │              │                         │ [ reason   ]    │
          ▼              │                         │                 │
 [ Select skills from ]  │                         │                 │
 [ the catalogue only ]──┼─────────────────────────┼──▶[ Store claim ]│
          │              │                         │        │        │
          ▼              │                         │        │        │
 [ State hours for  ]────┼─────────────────────────┼──▶< Within      >│
 [ a given day      ]    │                         │   < 0..14 days? >│
          │              │                         │     │       │   │
          │              │                         │ No ═╝       │Yes│
          │              │                         │     ║       ▼   │
 [ Entry rejected ]◀─────┼─────────────────────────┼─[ Reject ]  [ Upsert ]
          ║              │                         │             [ that   ]
          ║              │                         │             [ day    ]
          ║              │                         │                 │   │
          ║              │                         │  [ Discard past ]   │
          ║              │                         │  [ declarations ]   │
          ║              │                         │         ▲           │
          ║              │                         │         │           │
          ║              │            SCHEDULER ───┼──[ On a timer, ]    │
          ║              │            (no person)  │  [ nobody      ]    │
          ║              │                         │  [ present     ]    │
          ║              │  [ Inspect a worker ]───┼──▶[ Return profile, ]│
          ║              │           ▲             │   [ skills, batches, ]
          ║              │           │             │   [ history, earnings]
          ║              │           │             │        │            │
          ║              │  [ Sees everything ]◀───┼─[ Omit banking ]     │
          ║              │  [ except banking  ]    │ [ details       ]    │
          ║              │  [ details         ]    │                      │
          ║              │           │             │                      │
          ╚══════════════┼───────────┴─────────────┼──────────────────────┘
                         │           ▼             │
                         │       ( End )           │
```

**Level 1.2: Workforce Management**

**Handoff List:**

1. **Worker → System, and HR Admin → System.** Two lanes reach the same action.
   Profile details may be entered by the worker or by an HR admin on their behalf,
   and both crossings arrive at one store. What differs is only whose name the
   record carries — which is why the record names the author rather than assuming
   it.

2. **System decides the duplicate.** ⚠ The duplicate-identity check sits in the
   System lane, not the HR Admin lane. An admin cannot wave a second account
   through for a worker they know to be genuine, because the decision was never
   theirs to make. The refusal crosses back with its reason attached so the worker
   corrects it in their own lane.

3. **Worker → System, skills.** What crosses is a selection from the catalogue,
   never free text. The catalogue lives in the System lane and the worker chooses
   from it; nothing a worker types can enter the vocabulary.

4. **Worker → System, availability.** The 0–14 day bound is enforced in the System
   lane. A rejected entry crosses back; there is no lane in which an out-of-range
   declaration is accepted and reconciled afterwards.

5. **HR Admin → System → HR Admin, inspection.** ⚠ The return crossing is narrower
   than what the System lane holds. Banking details exist in the system and do not
   cross into the HR Admin lane. The omission is performed by the sender, not
   requested by the receiver — an admin cannot ask for what is never sent.

6. **Scheduler → System, discarding past declarations.** ⚠ A lane with no person in
   it. Declarations that have fallen into the past are discarded on a timer, so the
   0–14 day window stays a true statement of what is known rather than an
   accumulating history nobody pruned. No worker or admin lane has an arrow into
   this step.

7. **Removal is decided in the System lane.** Where a worker leaves for good, which
   form of removal applies is determined by the record, not chosen by the admin.
   The HR Admin lane contains the request; it does not contain the decision.

---

### Level 1.2.1: Suspension and Appeal

**Name:** Suspension and Appeal
**Reference:** Use Case Diagram Level 1.2 items 8–10, Activity Diagram Level 1.2.1
**Lanes:** Suspended Worker · HR Admin · System

```
   SUSPENDED WORKER      │        HR ADMIN         │         SYSTEM
                         │                         │
                         │   ( Start )             │
                         │       │                 │
                         │       ▼                 │
                         │ [ Suspend the ]─────────┼──▶[ Set status suspended;]
                         │ [ worker      ]         │   [ leave every record   ]
                         │                         │   [ intact               ]
                         │                         │            │
                         │                         │            ▼
                         │                         │   [ Create the appeal ]
                         │                         │   [ route in the SAME ]
                         │                         │   [ action            ]
                         │                         │            │
 [ Requests a link ]─────┼─────────────────────────┼──▶< Address matches   >
 [ by address      ]     │                         │   < an account?       >
          ▲              │                         │      │            │
          │              │                         │   No │            │ Yes
          │              │                         │      ▼            ▼
 [ Receives the   ]◀─────┼─────────────────────────┼──[ Identical ] [ Identical ]
 [ same neutral   ]      │                         │  [ neutral   ] [ neutral   ]
 [ response either]      │                         │  [ response  ] [ response  ]
 [ way            ]      │                         │
          │              │                         │
          ▼              │                         │
 [ States their case ]───┼──▶[ Reads the case ]    │
 [ once, at bounded  ]   │           │             │
 [ length            ]   │           ▼             │
                         │    < Admin decides >    │
                         │      │           │      │
                         │ Reject          Approve │
                         │      ║           │      │
                         │      ╚═══════════┼──────┼──▶[ Suspension stands; ]
                         │                  │      │   [ record still intact]
                         │                  └──────┼──▶[ Restore access with]
                         │                         │   [ everything held    ]
                         │                         │            │
 [ Signs in again ]◀─────┼─────────────────────────┼────────────┘
          │              │                         │
          ▼              │                         │
      ( End )            │                         │
```

**Level 1.2.1: Suspension and Appeal**

**Handoff List:**

1. **HR Admin → System, suspension.** What crosses is the decision to suspend. What
   the System lane does with it includes something the admin did not ask for:
   creating the appeal route. ⚠ Placing that step in the System lane, in the same
   action, is what guarantees recourse exists — an admin cannot suspend someone
   without it, because it was never a separate thing they had to remember.

2. **Suspended Worker → System, without an admin.** The request for a link crosses
   directly to the System lane. The HR Admin lane is absent from this crossing
   entirely: a person locked out cannot be made to ask the person who locked them
   out.

3. **System → Suspended Worker, identically on both branches.** ⚠ The two arrows
   returning to the worker's lane carry the same content. This is the only place in
   the specification where two branches are drawn deliberately to be
   indistinguishable at the lane boundary. What the worker's lane receives must not
   reveal whether the address matched, or the appeal route becomes a way to
   discover who works here.

4. **Suspended Worker → HR Admin.** One statement, bounded in length, crosses once.
   The bound is what keeps this a right rather than a channel: the worker's lane
   cannot flood the admin's, and the admin's cannot demand elaboration.

5. **HR Admin → System, the decision.** Restoration is performed by the System lane
   because nothing was destroyed — the earlier crossing preserved every record, so
   restoring is a status change rather than a reconstruction. The admin's lane
   contains a judgement; the System lane contains its execution.

---

## Level 1.3: Project & Batch Management

**Name:** Project & Batch Management
**Reference:** Use Case Diagram Level 1.3, Activity Diagram Level 1.3
**Lanes:** Worker · Project Admin · System · Scheduler

```
      WORKER        │    PROJECT ADMIN     │       SYSTEM        │  SCHEDULER
                    │                      │                     │
                    │    ( Start )         │                     │
                    │        │             │                     │
                    │        ▼             │                     │
                    │ [ Create project ]───┼──▶[ Store ]         │
                    │ [ Create batch   ]   │       │             │
                    │        │             │       ▼             │
                    │        │             │ [ Maintain status ]◀┼─[ On a  ]
                    │        │             │ [ from dates      ]  │[ timer ]
                    │        ▼             │                     │
                    │ [ Define roles,  ]───┼──▶[ Store, each ]   │
                    │ [ each with its  ]   │   [ with its own]   │
                    │ [ own time rule  ]   │   [ time rule   ]   │
                    │        │             │                     │
 [ Express ]────────┼──▶[ Sees the      ]  │                     │
 [ interest]        │   [ candidacy     ]  │                     │
                    │        │             │                     │
                    │        ▼             │                     │
                    │ < One or many? >     │                     │
                    │   │          │       │                     │
                    │ One        Many      │                     │
                    │   │          ▼       │                     │
                    │   │  [ Submit file ]─┼──▶[ RESOLVE: validate ]
                    │   │          ▲       │   [ — write nothing   ]
                    │   │          │       │            │          │
                    │   │  [ Reviews the ]◀┼────────────┘          │
                    │   │  [ preview     ] │                       │
                    │   │          │       │                       │
                    │   │          ▼       │                       │
                    │   │  [ Commits ]─────┼──▶[ Write valid rows; ]
                    │   │                  │   [ report each failure]
                    │   │                  │   [ individually       ]
                    │   ▼                  │            │           │
                    │ [ Assign worker ]────┼──▶[ Store assignment ] │
                    │ [ to a role     ]    │            │           │
                    │        │             │            ▼           │
                    │ [ Set terms on  ]────┼──▶[ Issue work ID    ] │
                    │ [ the assignment]    │   [ unique to batch  ] │
                    │                      │            │           │
 [ Signs the ]──────┼──────────────────────┼──▶< Agreement        > │
 [ agreement ]      │                      │   < completed? (§1.6) >│
 [ (§1.6)    ]      │                      │      │          │      │
                    │                      │  No ═╝          │ Yes  │
                    │                      │      ║          ▼      │
                    │ [ Cannot override ]◀══┼══[ Withhold ] [ Release  ]
                    │ [ this outcome    ]  │  [ credential] [ credential;]
                    │                      │               [ record who ]
                    │                      │               [ obtained it]
                    │                      │                     │     │
 [ Reaches the ]◀───┼──────────────────────┼─────────────────────┘     │
 [ client's    ]    │                      │                           │
 [ documents   ]    │                      │                           │
          │         │                      │                           │
          ▼         │                      │                           │
 [ Work proceeds ]  │                      │                           │
 [ (§1.4)        ]  │                      │                           │
                    │ [ End the      ]─────┼──▶[ Withdraw every    ]   │
                    │ [ engagement   ]     │   [ credential in the ]   │
                    │                      │   [ batch, in ONE act ]   │
                    │                      │            │              │
                    │                      │            ▼              │
                    │                      │        ( End )            │
```

**Level 1.3: Project & Batch Management**

**Handoff List:**

1. **Scheduler → System, with nobody present.** Batch status is maintained from
   dates by a lane containing no person at all. Nobody updates status by hand
   because no human lane touches it — which is also why nobody can forget to.

2. **Worker → Project Admin, expressed interest.** One record seen from two lanes.
   The worker's lane calls it interest; the admin's lane calls it a candidacy.
   Drawing it as a single crossing rather than two records is what keeps them from
   diverging.

3. **Project Admin → System, resolve, and System → Project Admin, preview.** ⚠ Two
   crossings before anything is written. The admin's lane receives the full
   consequence of the change while the System lane still holds no change at all.
   Declining to commit requires no undo because nothing crossed back the other way.

4. **Project Admin → System, commit.** The second crossing is the one that writes.
   Failures are reported per row so that the admin's lane learns exactly which
   rows to correct, rather than being handed a single verdict on the whole file.

5. **Terms cross to the assignment, not the role.** What the admin sets belongs to
   one person's engagement. Two workers in one role may be engaged differently
   without the role itself being split — the lane crossing carries an individual
   arrangement, not a class of them.

6. **System decides the credential — no admin lane touches it.** ⚠ The most
   important boundary in this diagram. The agreement check and the withholding both
   sit wholly inside the System lane. The Project Admin lane has no arrow into that
   decision. This is the difference between a permission an admin declines to
   exercise and an act the system offers no one: there is no crossing to draw.

7. **System → Worker, the credential.** Access reaches the worker's lane only after
   the check succeeds. Staffing may run ahead of signature — those crossings
   already happened — but this one cannot.

8. **Project Admin → System, ending the engagement.** One crossing withdraws every
   credential in the batch. Access ending person by person would leave the last one
   to be forgotten, which is precisely what the manual process did.

---

## Level 1.4: Work & Time Management

**Name:** Work & Time Management
**Reference:** Use Case Diagram Level 1.4, Activity Diagram Level 1.4
**Lanes:** Worker · Batch Manager · Project Admin · System

```
       WORKER          │ BATCH MANAGER │ PROJECT ADMIN │       SYSTEM
                       │               │               │
      ( Start )        │               │               │
          │            │               │               │
          ▼            │               │               │
 [ Open the batch ]────┼───────────────┼───────────────┼──▶< Batch still >
                       │               │               │   < open?       >
                       │               │               │      │       │
                       │               │               │  No ═╝       │ Yes
                       │               │               │      ║       ▼
 [ Read-only: no   ]◀══┼═══════════════┼═══════════════┼═[ Close to ] │
 [ claim, release, ]   │               │               │ [ every    ] │
 [ or edit         ]   │               │               │ [ change   ] │
          ║            │               │               │              ▼
 [ Sees claimable ]◀───┼───────────────┼───────────────┼──[ Send claimable ]
 [ work and OWN   ]    │               │               │  [ work and only  ]
 [ terms only     ]    │               │               │  [ their own terms]
          │            │               │               │
          ▼            │               │               │
 [ Claim one task ]────┼───────────────┼───────────────┼──▶< Operational >
 [ or many at once]    │               │               │   < role?       >
                       │               │               │      │       │
                       │               │               │  No ═╝       │ Yes
                       │               │               │      ║       ▼
 [ Cannot claim ]◀═════┼═══════════════┼═══════════════┼═[ Refuse ] < Already   >
                       │               │               │            < assigned? >
                       │               │               │              │      │
                       │               │               │        Yes ══╝      │ No
                       │               │               │              ║      ▼
 [ Learns which     ]◀─┼───────────────┼───────────────┼─[ Report which ] [ Grant ]
 [ failed; holds    ]  │               │               │ [ failed; grant ] [ the   ]
 [ the rest         ]  │               │               │ [ the rest      ] [ task  ]
          │            │               │               │                     │
          ▼            │               │               │                     │
 [ ANNOTATION HAPPENS OUTSIDE THIS SYSTEM — in the client's document ]       │
          │            │               │               │                     │
          ▼            │               │               │                     │
 [ Record time,   ]────┼───────────────┼───────────────┼──▶[ Store ]         │
 [ comment, or    ]    │               │               │       │             │
 [ release        ]    │               │               │       │             │
                       │               │               │       │             │
                       │ [ Log the  ]──┼───────────────┼──▶[ Store, paid ]   │
                       │ [ coordina-]  │               │   [ on its own  ]   │
                       │ [ tion time]  │               │   [ terms       ]   │
                       │               │               │       │             │
                       │               │ [ Transfer ]──┼──▶[ Move the task ] │
                       │               │ [ a task   ]  │   [ WITHOUT erasing]│
                       │               │               │   [ it was done    ]│
                       │               │               │       │             │
                       │               │               │       ▼             │
                       │               │               │ [ Compute payable ] │
                       │               │               │ [ time by the     ]◀┘
                       │               │               │ [ ROLE's rule     ]
                       │               │               │       │
                       │               │               │       ▼
                       │               │               │   ( End )
```

**Level 1.4: Work & Time Management**

**Handoff List:**

1. **Worker → System, opening the batch.** One crossing returns everything about
   one engagement — work available, work held, time recorded, earnings so far. A
   worker deciding whether to take another task weighs all of it at once, so it
   arrives at once.

2. **System → Worker, own terms only.** ⚠ The rate that crosses is the rate on the
   worker's own assignment. Other workers' terms exist in the System lane and never
   cross into any worker's lane. The protection runs in both directions and is
   enforced by what is sent, not by what is displayed.

3. **The operational-role check is in the System lane.** A batch manager holds a
   managerial role and therefore cannot claim tasks. Note that the Batch Manager
   lane in this diagram has exactly one action — logging coordination time — and no
   arrow into the claiming flow at all. Coordination is not doing the work, and the
   lane structure says so before any rule does.

4. **System decides contested claims at the moment of writing.** ⚠ Two workers
   claiming in the same instant both have crossings into the System lane. The
   conflict is resolved there, by the fact of assignment, not by whichever worker's
   lane rendered the list first.

5. **System → Worker, partial success.** ⚠ A bulk claim that partly fails returns
   both outcomes across one boundary. One contested item never costs a worker the
   others.

6. **The gap where no lane exists.** Annotation itself has no lane in this diagram
   — it happens in the client's document, outside the system entirely. Everything
   after it is *reported* across a boundary rather than observed within one. This
   is why time is recorded rather than measured, and why the role's rule for
   converting effort into payable time has to exist at all.

7. **Project Admin → System, transfer.** Work moves between workers without erasing
   that it was done. The admin's lane cannot delete-and-recreate, because credit
   for completed work is part of what the payment record is built from.

8. **The computation is in the System lane, from the role's rule.** No worker's
   lane and no admin's lane contains the choice of rule. It was fixed when the role
   was defined in §1.3, which is what prevents any individual from being moved to
   whichever rule favours them.

---

## Level 1.5: Payroll & Payment

**Name:** Payroll & Payment
**Reference:** Use Case Diagram Level 1.5, Activity Diagram Level 1.5
**Lanes:** Worker · Finance Admin · System · Accounting

Payroll's two flows are partitioned separately, as in §5. The lane structure makes
their difference plain: accrual crosses no human lane at all, while payment begins
outside the system entirely.

**(a) Establishing what is owed** — recomputed whenever the figure is displayed

```
       WORKER        │    FINANCE ADMIN    │           SYSTEM
                     │                     │
                     │                     │      ( Start )
                     │                     │          │
                     │                     │          ▼
                     │                     │ [ Take payable time ]
                     │                     │ [ from §1.4         ]
                     │                     │          │
                     │                     │          ▼
                     │                     │ < Managerial role? >
                     │                     │     │           │
                     │                     │  No │           │ Yes
                     │                     │     ▼           ▼
                     │                     │ [ time ÷    ] < Hourly rate on >
                     │                     │ [ 3600 ×    ] < the assignment?>
                     │                     │ [ rate      ]   │          │
                     │                     │     │       Yes │          │ No
                     │                     │     │           ▼          ▼
                     │                     │     │   [ time ÷    ] [ Fixed  ]
                     │                     │     │   [ 3600 ×    ] [ salary ]
                     │                     │     │   [ rate      ] │        │
                     │                     │     └───────┬────────┴────────┘
                     │                     │             ▼
                     │                     │ [ + bonus; round ONCE, ]
                     │                     │ [ at the total         ]
                     │                     │             │
                     │                     │             ▼
                     │                     │ [ − payments already   ]
                     │                     │ [ recorded = OUTSTANDING]
                     │                     │             │
                     │                     │             ▼
                     │                     │ < Banking details on file? >
                     │                     │      │              │
                     │                     │  No ═╝              │ Yes
                     │                     │      ║              ▼
 [ Sees what they ]◀─┼─────────────────────┼─[ Owed, but ]  [ Appears among ]
 [ have earned    ]  │                     │ [ NOT yet   ]  [ those who can ]
 [ either way     ]  │                     │ [ payable   ]  [ be paid       ]
                     │                     │      ║              │
                     │ [ Sees the gap  ]◀══┼══════╝              │
                     │ [ listed        ]   │                     │
                     │ [ separately    ]   │                     │
                     │        │            │                     │
                     │ [ Sees who is   ]◀──┼─────────────────────┘
                     │ [ payable now   ]   │
                     │        │            │
                     │        ▼            │
                     │    ( End )          │
```

**(b) Recording a payment** — performed after money has left the bank

```
       WORKER        │    FINANCE ADMIN    │      SYSTEM      │  ACCOUNTING
                     │                     │                  │
                     │     ( Start )       │                  │
                     │         │           │                  │
                     │         ▼           │                  │
 [ Receives    ]◀────┼─[ PAY THROUGH THE ] │                  │
 [ the money   ]     │ [ BANK — outside  ] │                  │
                     │ [ this system     ] │                  │
                     │         │           │                  │
                     │         ▼           │                  │
                     │ [ Record it here, ]─┼─▶[ Store against ]│
                     │ [ against person  ] │  [ the work it   ]│
                     │ [ and work        ] │  [ settles       ]│
                     │                     │         │        │
                     │                     │         ▼        │
                     │                     │ [ Snapshot the   ]│
                     │                     │ [ banking details]│
                     │                     │ [ AS THEY STAND  ]│
                     │                     │         │        │
                     │                     │         ▼        │
                     │                     │ [ CLOSED — no    ]│
                     │                     │ [ amount,        ]│
                     │                     │ [ recipient, or  ]│
                     │                     │ [ snapshot may   ]│
                     │                     │ [ EVER be edited ]│
                     │                     │         │        │
                     │ < Recorded in   >   │         │        │
                     │ < error?        >◀──┼─────────┘        │
                     │   │          │      │                  │
                     │  No         Yes     │                  │
                     │   │          ▼      │                  │
                     │   │  [ Archive ]────┼─▶[ Remove from  ]│
                     │   │                 │  [ outstanding, ]│
                     │   │                 │  [ NOT destroy  ]│
                     │   │                 │         │        │
                     │   │ < Archived by > │         │        │
                     │   │ < mistake?    >◀┼─────────┘        │
                     │   │   │        │    │                  │
                     │   │ Yes       No    │                  │
                     │   │   ▼        ▼    │                  │
                     │   │ [ Restore ][ May now ]             │
                     │   │ [ it      ][ delete  ]             │
                     │   │   │        │    │                  │
                     │   ▼   ▼        ▼    │                  │
                     │ [ Export for    ]───┼──────────────────┼─▶[ Reconcile ]
                     │ [ reconciliation]   │                  │  [ against   ]
                     │        │            │                  │  [ the bank  ]
                     │        ▼            │                  │
                     │    ( End )          │                  │
```

**Level 1.5: Payroll & Payment**

**Handoff List:**

1. **Accrual crosses no human lane.** ⚠ Diagram (a) begins in the System lane and
   stays there until a figure is displayed. Nobody approves earnings, because there
   is no lane containing an approval step. Earnings are projected from the moment a
   task is claimed — the calculation runs when the figure is asked for, not when
   someone authorises it.

2. **System → Worker, and System → Finance Admin, the same calculation.** ⚠ Two
   crossings out of one computation. The worker's view of their earnings and
   finance's view of that worker's earnings leave the same lane by the same
   arithmetic. There is no arrangement of these lanes in which the two figures
   disagree, because there is only one place they are produced.

3. **The banking-details branch separates two questions.** ⚠ Both branches cross to
   the worker — a worker accrues earnings regardless. What differs is what reaches
   the Finance Admin lane: those not yet payable are listed separately, so the gap
   is visible to whoever can act on it rather than silently reducing a total.

4. **Finance Admin → Worker, outside the system.** ⚠ The first crossing in diagram
   (b) does not touch the System lane at all. Money moves through the bank. The
   system computes what is owed and records what was sent; it never moves money,
   and no lane here shows it doing so.

5. **Finance Admin → System, recording.** The snapshot is taken in the System lane
   at the moment of the crossing. A worker changing their account tomorrow cannot
   alter what was paid yesterday, because the details were copied rather than
   referenced.

6. **No arrow returns to an existing record.** ⚠ Once closed, no crossing from any
   lane can edit amount, recipient, or snapshot. Archiving is a new crossing that
   changes a record's standing; restoring is another; deletion is available only to
   what was archived first. Every correction adds an arrow rather than redrawing
   one.

7. **Finance Admin → Accounting.** The financial record leaves the system in a form
   accounting's own spreadsheets consume. Accounting has a lane because it acts on
   what it receives without ever signing in. Exporting is itself privileged: the
   crossing places data beyond the system's protection, and only some lanes may
   draw it.

---

## Level 1.6: Agreement Management

**Name:** Agreement Management
**Reference:** Use Case Diagram Level 1.6, Activity Diagram Level 1.6
**Lanes:** Worker · Compliance Admin · System · Scheduler · Legal Counsel

```
     WORKER      │ COMPLIANCE ADMIN │      SYSTEM      │ SCHEDULER │ LEGAL
                 │                  │                  │           │ COUNSEL
                 │   ( Start )      │                  │           │
                 │       │          │                  │           │
                 │       ▼          │                  │           │
                 │ [ Author the ]◀──┼──────────────────┼───────────┼─[ Advises ]
                 │ [ template,  ]   │                  │           │ [ on the  ]
                 │ [ marking    ]   │                  │           │ [ wording ]
                 │ [ where each ]   │                  │           │
                 │ [ recipient's]   │                  │           │
                 │ [ details go ]   │                  │           │
                 │       │          │                  │           │
                 │       ▼          │                  │           │
                 │ [ Publish as ]───┼─▶[ Version now  ]│           │
                 │ [ a version  ]   │  [ IMMUTABLE    ]│           │
                 │       │          │         │       │           │
                 │       ▼          │         │       │           │
                 │ [ Issue to   ]───┼─▶[ Resolve the  ]│           │
                 │ [ recipient  ]   │  [ details into ]│           │
                 │                  │  [ the text NOW ]│           │
                 │                  │         │       │           │
 [ Views the ]◀──┼──────────────────┼─────────┘       │           │
 [ agreement ]───┼──────────────────┼─▶[ Record the  ]│           │
                 │                  │  [ viewing, in ]│           │
                 │                  │  [ order       ]│           │
      │          │                  │                 │           │
      ▼          │                  │                 │           │
 < Signs? >      │                  │                 │           │
   │      │      │                  │                 │           │
 No│      │Yes   │                  │                 │           │
   ║      ▼      │                  │                 │           │
   ║ [ Sign ]────┼──────────────────┼─▶[ Bind signature to a  ]   │
   ║             │                  │  [ DIGEST of the exact  ]   │
   ║             │                  │  [ text the signer saw  ]   │
   ║             │                  │            │              │
   ║             │ [ Countersign ]──┼─▶[ COMPLETED — and ]      │
   ║             │ [ on behalf of]  │  [ ONLY now may a  ]      │
   ║             │ [ the company ]  │  [ credential be   ]      │
   ║             │                  │  [ released (§1.3) ]      │
   ║             │                  │            │              │
   ╚═════════════┼══▶[ REJECTED — ]─┼────────────┤              │
                 │   [ final for  ] │            │              │
                 │   [ THIS       ] │            │              │
                 │   [ agreement  ] │            │              │
                 │                  │            │              │
   While it waits unsigned, three lanes may move it:            │
                 │                  │            │              │
                 │ [ Extend ]───────┼─▶[ Deadline moves;  ]     │
                 │                  │  [ still waiting    ]     │
                 │ [ Void   ]───────┼─▶[ VOIDED — may be  ]     │
                 │                  │  [ reinstated       ]     │
                 │                  │  [ EXPIRED — may  ]◀──────┼─[ On a  ]
                 │                  │  [ still be       ]       │ [ timer ]
                 │                  │  [ extended       ]       │
                 │                  │            │              │
                 │                  │            ▼              │
                 │                  │ [ APPEND to a permanent ] │
                 │                  │ [ record: cause, author,] │
                 │                  │ [ order — never rewrite ] │
                 │                  │            │              │
 [ Receives  ]◀──┼──[ Receives  ]◀──┼─[ Render identically ]────┼─▶[ Receives ]
 [ the same  ]   │  [ the same  ]   │ [ for every party    ]    │  [ the same ]
 [ document  ]   │  [ document  ]   │ [                    ]    │  [ document ]
                 │                  │            │              │
                 │                  │        ( End )            │
```

**Level 1.6: Agreement Management**

**Handoff List:**

1. **Legal Counsel → Compliance Admin.** Counsel has a lane because the wording
   originates outside the system and never passes through it. Counsel advises on
   the template; they do not sign in, author, or issue. The crossing is one-way
   into the admin's lane.

2. **Compliance Admin → System, publication.** ⚠ Publication is the crossing after
   which nothing returns. A published version is immutable in the System lane, and
   there is no arrow from any lane back into it. A correction means a new version;
   what was signed against the old one stands exactly as signed.

3. **System → Worker, with details already resolved.** The recipient's details are
   substituted in the System lane before the text crosses. What the worker's lane
   receives is the finished document, not a template plus a promise.

4. **Worker → System, the viewing.** Recorded and ordered relative to what follows.
   This crossing is what later allows a signature to be shown to have followed a
   reading rather than preceded it — the order of arrows is itself the evidence.

5. **Worker → System, the signature.** ⚠ The digest is computed in the System lane
   over the exact text that crossed. Later alteration is not merely forbidden but
   detectable, because the binding was made at the boundary rather than asserted
   after it.

6. **Rejection crosses once and stops.** ⚠ The worker's refusal is a legitimate
   outcome, not an error, and no lane holds a step for overriding it. It appends to
   the permanent record like every other event.

7. **Three lanes may move a waiting agreement.** The Compliance Admin lane may
   extend or void it; ⚠ the Scheduler lane may expire it with nobody present. That
   expiry lives in a lane containing no person is the requirement: an agreement
   does not stay open because everyone forgot about it. Voiding is reversible,
   expiry is extendable, only rejection is final.

8. **Countersignature is a separate lane's act.** ⚠ The agreement binds both
   parties, so a worker's signature alone does not complete it. The crossing from
   the Compliance Admin lane is what moves it to COMPLETED — and only a completed
   agreement releases a credential in §1.3.

9. **System → every lane, one document.** The rendered agreement crosses to worker,
   compliance, and counsel identically, reproducing names correctly in the signer's
   own script. One document reaching three lanes is what makes it evidence; three
   renderings would make it three claims.

---

## Level 1.7: Oversight & Reporting

**Name:** Oversight & Reporting
**Reference:** Use Case Diagram Level 1.7, Activity Diagram Level 1.7
**Lanes:** Owner · Admins · Batch Manager · Worker · System

```
    OWNER      │    ADMINS    │ BATCH MANAGER │   WORKER   │      SYSTEM
               │              │               │            │
 [ Opens a ]───┼──────────────┼───────────────┼────────────┼─▶( Start )
 [ view    ]   │ [ Opens a ]──┼───────────────┼────────────┼─────▶│
               │ [ view    ]  │ [ Opens a ]───┼────────────┼─────▶│
               │              │ [ view    ]   │ [ Opens ]──┼─────▶│
               │              │               │ [ a view]  │      ▼
               │              │               │            │ [ Identify the ]
               │              │               │            │ [ viewer       ]
               │              │               │            │      │
               │              │               │            │      ▼
               │              │               │            │ < What is this  >
               │              │               │            │ < viewer        >
               │              │               │            │ < entitled to?  >
               │              │               │            │   │
 [ The whole ]◀┼──────────────┼───────────────┼────────────┼───┤ Owner
 [ business, ]  │              │               │            │   │
 [ each figure] │              │               │            │   │
 [ against the] │              │               │            │   │
 [ preceding  ] │              │               │            │   │
 [ period     ] │              │               │            │   │
               │ [ Their own ]◀┼───────────────┼────────────┼───┤ Admin
               │ [ domain    ] │               │            │   │
               │ [ only      ] │               │            │   │
               │              │ [ Availability]◀────────────┼───┤ Batch
               │              │ [ and progress]│            │   │ manager
               │              │ [ — NEVER pay ]│            │   │
               │              │               │ [ Earned, ]◀┼───┤ Worker
               │              │               │ [ outstan-]│    │
               │              │               │ [ ding,   ]│    │
               │              │               │ [ paid    ]│    │
               │              │               │            │    ▼
               │              │               │            │ [ Compute from the ]
               │              │               │            │ [ ONE shared       ]
               │              │               │            │ [ calculation      ]
               │              │               │            │      │
               │              │               │            │      ▼
               │              │               │            │  ( End )
```

**Level 1.7: Oversight & Reporting**

**Handoff List:**

1. **Every lane crosses into the System lane first.** No view is assembled in the
   viewer's own lane. Scope is not a filter the receiving lane applies to a general
   result — it determines what is fetched at all, in the System lane, before
   anything crosses back.

2. **Four different crossings return from one decision.** The System lane answers
   one question — what is this viewer entitled to — and the answer determines which
   arrow is drawn. A viewer cannot receive another viewer's arrow, because only one
   is ever produced.

3. **System → Owner.** The whole business, each figure against the preceding
   period. Direction of travel crosses along with the figure, so nobody has to run
   a report to learn whether things are improving.

4. **System → Batch Manager, with pay excluded.** ⚠ The exclusion is the point of
   this lane. Coordinating a batch requires availability and progress; it does not
   require knowing what each person is paid. That crossing is never drawn — what a
   worker earns stays between their lane and finance's.

5. **System → Worker, three distinct quantities.** Earned, outstanding, and paid
   cross as three figures the system never conflates. Only the third is money that
   has reached them.

6. **One calculation, many crossings.** ⚠ A worker's earnings and an admin's view
   of that worker's earnings are one computation observed from two lanes, not two
   computations that happen to agree. The single source is in the System lane; the
   lanes differ only in what they are entitled to receive from it.

---

## Level 1.8: Bulk Data Operations

**Name:** Bulk Data Operations
**Reference:** Use Case Diagram Level 1.8, Activity Diagram Level 1.8
**Lanes:** Admin (Project, Finance, or HR) · System · Accounting

```
            ADMIN              │          SYSTEM          │  ACCOUNTING
                               │                          │
          ( Start )            │                          │
              │                │                          │
              ▼                │                          │
 [ Supplies a file — often ]───┼─▶[ RESOLVE: validate    ]│
 [ one this system exported]   │  [ EVERY row — write    ]│
 [ earlier, edited outside ]   │  [ NOTHING              ]│
                               │            │             │
 [ Reviews the FULL     ]◀─────┼────────────┘             │
 [ preview — everything ]      │                          │
 [ that would happen,   ]      │                          │
 [ not a sample         ]      │                          │
              │                │                          │
              ▼                │                          │
      < Admin commits? >       │                          │
        │           │          │                          │
    No ═╝           │ Yes      │                          │
        ║           ▼          │                          │
        ║ [ Commit ]───────────┼─▶[ COMMIT: re-validate  ]│
        ║                      │  [ every row AGAIN      ]│
        ║                      │            │             │
        ║                      │            ▼             │
        ║                      │    < Row valid NOW? >    │
        ║                      │      │            │      │
        ║                      │  No ═╝            │ Yes  │
        ║                      │      ║            ▼      │
        ║                      │      ║     [ Write row ] │
        ║                      │      ▼            │      │
        ║                      │ [ Record as ]     │      │
        ║                      │ [ failed —  ]     │      │
        ║                      │ [ alone     ]     │      │
        ║                      │      ║            │      │
        ║                      │      ╚═════┬══════╝      │
        ║                      │            ▼             │
 [ Learns exactly which  ]◀────┼─[ Report outcome ]       │
 [ rows failed and why;  ]     │ [ PER ROW        ]       │
 [ corrects only those   ]     │                          │
        ║      │               │                          │
        ║      ▼               │                          │
 [ Export ]────┼───────────────┼──────────────────────────┼─▶[ Consumes ]
        ║      │               │                          │  [ it       ]
        ║      ▼               │                          │
    ( End )                    │                          │
```

**Level 1.8: Bulk Data Operations**

**Handoff List:**

1. **Admin → System, resolve.** ⚠ The first crossing writes nothing. Its entire
   purpose is to let the admin's lane see the consequence of a change before
   choosing it, which is what makes *not committing* a complete remedy rather than
   a partial one.

2. **System → Admin, the full preview.** Everything that would happen, not a
   sample. A preview that summarises is a preview that hides the row that matters —
   and the admin's lane is where the decision is made, so it must receive the whole
   picture.

3. **Admin → System, commit.** ⚠ The second crossing does not trust the first. The
   file may have changed, or the underlying data may have, between the two. Every
   fact is established again at the moment of writing: the preview is advisory, the
   commit is authoritative. Two crossings exist precisely because a single one
   would have to write on the strength of a fact established earlier.

4. **Rows succeed or fail individually inside the System lane.** ⚠ People are
   matched on the identifier the system issued them, never on their name — names
   being neither unique nor stable. An invalid row fails alone; nine hundred good
   rows do not wait on one bad one.

5. **System → Admin, per-row outcome.** What crosses back is specific enough to act
   on. The admin corrects and resubmits only the failed rows, which is what makes
   this the ordinary way to change many records rather than a last resort.

6. **Admin → Accounting, export.** ⚠ The crossing that leaves the system's
   protection entirely. Once data is in accounting's lane, none of the guarantees
   drawn in any diagram above apply to it — which is why the ability to draw this
   arrow is itself a privilege rather than an ordinary capability.

7. **Pagination bounds every crossing.** No list crosses a boundary in full,
   whatever is requested of it. This applies to every list rather than to those
   that have so far grown large, because a boundary that works only for small
   volumes is one that fails exactly when the system succeeds.

---

## Conclusion

Partitioning the flows by responsibility exposes something the activity diagrams
stated but could not show: **where a step is drawn determines who can affect it.**

Three patterns recur across the eight diagrams, and each is a requirement rather
than a drawing convention.

**Some steps have no human lane at all.** Batch status maintained from dates
(§1.3), past availability declarations discarded on a timer (§1.2), agreements
expiring with nobody present (§1.6), earnings recomputed whenever they are
displayed (§1.5a) — these sit in the System or Scheduler lane with no arrow from
any person. They cannot be forgotten, delayed, or done differently for one
worker than another, because there is no lane in which such a variation could
occur.

**Some decisions are drawn with no incoming arrow from an administrator.** The
credential withheld from an unsigned worker (§1.3), the duplicate identity refused
(§1.2), the payment record closed against editing (§1.5b). Each is the difference
between a permission an administrator declines to exercise and an act the system
offers no one. The activity diagrams could assert this; the swimlanes make it
structural, because there is simply no crossing to draw.

**Some boundaries are defined by what does not cross them.** Banking details that
never reach the HR Admin lane (§1.2). Pay that never reaches the Batch Manager
lane (§1.7). Other workers' terms that never reach a worker's lane (§1.4). These
are enforced by the sender rather than filtered by the receiver, which is the same
principle as §1.1's silent-hide seen from a different angle: a capability that is
absent cannot be probed, and a figure that never crosses cannot be leaked.

Read together with §5, the two views answer different questions about the same
flows. §5 answers *what happens next, and what happens when it fails.* §6 answers
*whose responsibility is it, and what passes when it changes hands.* A design
satisfying one and not the other satisfies neither.

**Next task:** Class-based modelling — the nouns these flows operate on, drawn as
classes with their responsibilities and collaborators.
