# Software Requirements Specification

## GenMorphics AI — Annotation Workforce Management System

**Phase 5: Activity Diagram**

**Method:** Pressman, _Software Engineering: A Practitioner's Approach_ —
Requirements Engineering. Continues from [Phase 1: Inception](01-inception.md),
[Phase 2: QFD](02-qfd.md), [Phase 3: Usage Scenarios](03-usage-scenarios.md), and
[Phase 4: Use Case Diagram](04-use-case-diagram.md).

---

# 5. Activity Diagram

The use cases of §4 name what the system does. This section shows the order in
which it happens: the flow of control through each subsystem, the decisions taken
along the way, and the path followed when a step does not succeed.

Every diagram is followed by an **Activity List** describing each step. The list
is not a restatement of the boxes — it says what a diagram cannot: why a decision
exists, what becomes of the data on the failing path, and which requirement the
branch enforces.

Notation is conventional: `( )` start and end, `[ ]` an action, `< >` a decision,
`═══` a branch taken when something fails.

---

## Level 1.1: Access & Authorization

**Name:** Access & Authorization
**Reference:** Use Case Diagram Level 1.1

```
   ( Start )
       │
       ▼
 [ Authenticate with external identity provider ]
       │
       ▼
 < Account exists? >
       │
       ├─── No ───▶ [ Create account, holding ]
       │            [ no authority whatever   ]
       │                     │
       └─── Yes ─────────────┤
                             ▼
                   < Account active? >
                             │
       ┌─── No ══════════════┤
       │                     │ Yes
       ▼                     ▼
 [ Deny session;    ]  [ Load granted roles ]
 [ offer the appeal ]  [ and permissions    ]
 [ route            ]        │
       │                     ▼
       │               [ Establish session ]
       │                     │
       │                     ▼
       │               [ Present only permitted ]
       │               [ capabilities — others  ]
       │               [ are absent, not refused ]
       │                     │
       └──────────┬──────────┘
                  ▼
              ( End )
```

**Level 1.1: Access & Authorization**

**Activity List:**

1. **Authenticate with external identity provider.** The system holds no
   passwords and performs no authentication of its own. Whoever the provider says
   the person is, they are.

2. **Account exists?** A first sign-in creates the account. There is no
   registration step preceding this and no administrator involved — which is why
   the branch resolves to account creation rather than to a refusal.

3. **Create account with no authority.** The new account holds nothing. Every
   capability it will ever have arrives later by grant, so a newly created
   account is safe by construction rather than by configuration.

4. **Account active?** A suspended or removed account authenticates successfully
   with the provider and still receives no session. Authentication and
   authorization are separate questions, and passing the first does not settle
   the second.

5. **Deny session, offer appeal.** ⚠ The failing path. A suspended person is not
   simply refused — they are given the route to contest it, since the system they
   would use to protest is the one now closed to them.

6. **Load granted roles and their permissions.** Roles are data. What a person may
   do is read at session start rather than compiled into the software, which is
   what allows an owner to compose a new kind of administrator without a release.

7. **Present only permitted capabilities.** What the person may not do is absent
   rather than shown-and-refused, so the interface discloses nothing to someone
   probing it.

---

## Level 1.2: Workforce Management

**Name:** Workforce Management
**Reference:** Use Case Diagram Level 1.2

The subsystem holds four independent flows. Each is drawn separately below; a
worker or admin enters whichever one their task requires.

**(a) Maintain profile**

```
   ( Start )
       │
       ▼
 [ Enter identity, qualifications, ]
 [ banking details, documents      ]
       │
       ▼
 < Identity already on file? >
       │
       ├─── Yes ══▶ [ Refuse; state the reason ] ══▶ ( End )
       │
       └─── No ───▶ [ Store record, naming ]
                    [ whoever made the change ]
                              │
                              ▼
                           ( End )
```

**(b) Declare skills**

```
   ( Start )
       │
       ▼
 [ Select from the catalogue ]
 [ (no free text permitted)  ]
       │
       ▼
 [ Store claim ]
       │
       ▼
   ( End )
```

**(c) Declare availability**

```
   ( Start )
       │
       ▼
 [ State hours for a given day ]
       │
       ▼
 < Date within today .. today+14? >
       │
       ├─── No ═══▶ [ Reject the entry ] ══▶ ( End )
       │
       └─── Yes ──▶ [ Upsert that day ]
                    [ (zero is a valid ]
                    [  statement)      ]
                              │
                              ▼
                           ( End )
```

**(d) Inspect worker** _(HR admin)_

```
   ( Start )
       │
       ▼
 [ Retrieve the worker's profile, skills, ]
 [ batches, work history, earnings        ]
       │
       ▼
 [ Omit what this admin's function ]
 [ does not require — banking      ]
 [ details are not shown           ]
       │
       ▼
   ( End )
```

**Level 1.2: Workforce Management**

**Activity List:**

1. **Maintain profile.** Identity, qualifications, documents and banking details.
   An HR admin may perform this on a worker's behalf; the record names whoever
   made the change either way.

2. **Identity duplicated?** ⚠ Checked at the moment details are entered rather
   than at registration. Registration stays open because losing a capable worker
   costs more than checking — but a second account for one person would divide
   their work history and corrupt what they are paid, so the check happens where
   the evidence is.

3. **Refuse; state reason.** The failing path returns the worker to their entry
   with the reason given, rather than accepting the record and reconciling later.

4. **Declare skills from catalogue only.** No free text. A capability nobody else
   uses cannot be matched against work, so the catalogue is what makes the
   question "who can do this?" answerable at all.

5. **Declare availability within 0–14 days.** Bounded ahead because a declaration
   further out is guesswork, and bounded behind because a past date is not a
   declaration. Declaring zero hours is a statement and is stored as one, distinct
   from having said nothing.

6. **Upsert day.** One record per person per day, replaced rather than
   accumulated, so a revised declaration supersedes cleanly.

7. **Upload supporting document.** A CV, proof of identity, or photograph. Format
   and size are enforced rather than advised, and a refusal states its reason at
   the point of upload so the worker corrects it immediately.

8. **Maintain skills catalogue.** *(HR admin.)* Skills are created, grouped and
   ordered so the catalogue reflects what the business actually needs. A skill
   still claimed by a worker, or still required by a project, cannot be removed
   beneath its dependents.

9. **Inspect worker.** The HR admin's view is bounded by function: administering
   the workforce does not entail seeing banking details.

10. **Determine form of removal.** ⚠ Where a worker leaves for good, the system
    decides which removal applies rather than offering the choice: any history of
    work, payment, or a signed agreement means access is withdrawn and the record
    preserved. Only an account with no such history is erased. Preservation never
    depends on an administrator remembering to choose it.

Suspension, appeal and restoration are drawn separately in §1.2.1 below.

---

### Level 1.2.1: Suspension and Appeal

**Name:** Suspension and Appeal
**Reference:** Use Case Diagram Level 1.2, items 8–10

```
   ( HR admin suspends )
       │
       ▼
 [ Set status to suspended — ]
 [ every record left intact  ]
       │
       ▼
 [ Create the appeal route in ]
 [ the same action            ]
       │
       ▼
 [ Worker requests a link by address ]
       │
       ▼
 < Does the address match an account? >
       │
       ├─── No ───▶ [ Return the identical ]
       │            [ neutral response     ]
       │                     │
       └─── Yes ──▶ [ Return the identical ]
                    [ neutral response     ]
                             │
       ┌─────────────────────┘
       ▼                        ↑ both branches converge deliberately:
 [ Worker states their case ]     the outcome must reveal nothing
 [ once, at bounded length  ]
       │
       ▼
 < Admin decides >
       │
       ├─── Rejects ══▶ [ Suspension stands; ] ══▶ ( End )
       │                [ record still intact ]
       │
       └─── Approves ─▶ [ Restore access, with ]
                        [ everything they had  ]
                                 │
                                 ▼
                              ( End )
```

**Level 1.2.1: Suspension and Appeal**

**Activity List:**

1. **Set status suspended, preserve all records.** Assignments, work and earnings
   survive untouched. Suspending someone must never disturb what they are owed —
   the two decisions are unrelated and the system keeps them so.

2. **Create appeal token in the same action.** Not a later administrative step. A
   worker who cannot sign in cannot ask for recourse, so the recourse must exist
   before they discover they need it.

3. **Address matches an account?** ⚠ Both branches converge on an identical
   response. This is the one decision in the system deliberately drawn so that
   its outcome is invisible — a mechanism for the wrongly suspended must not
   double as a means of discovering who works here.

4. **Worker states case once, bounded.** One statement of reasonable length, not
   a correspondence. The bound is what makes the mechanism a right rather than a
   channel to be worn down.

5. **Admin decides.** Approval restores everything the worker held, since nothing
   was destroyed. Rejection leaves the suspension standing and the record intact.

---

## Level 1.3: Project & Batch Management

**Name:** Project & Batch Management
**Reference:** Use Case Diagram Level 1.3

```
   ( Start )
       │
       ▼
 [ Create project ]
       │
       ▼
 [ Create batch ]
       │
       ▼
 [ Define the roles the work needs, ]
 [ each with its own time rule      ]
       │
       ▼
 < Staff one person or many? >
       │
       ├─── One ──▶ [ Assign worker to role ]
       │                     │
       └─── Many ─▶ [ Resolve: validate and preview ]
                    [ — nothing written yet         ]
                             │
                             ▼
                    [ Commit valid rows; report ]
                    [ each failure individually  ]
                             │
       ┌─────────────────────┘
       ▼
 [ Issue work ID unique to the batch; ]
 [ set terms on the assignment        ]
       │
       ▼
 [ Worker signs agreement (§1.6) ]
       │
       ▼
 < Agreement completed? >        ← the system decides, not the admin
       │
       ├─── No ═══▶ [ Withhold credential, whatever ]
       │            [ the admin intends             ]
       │                     │
       └─── Yes ──▶ [ Release credential; record ]
                    [ who obtained it            ]
                             │
       ┌─────────────────────┘
       ▼
 [ Work proceeds (§1.4) ]
       │
       ▼
 < Engagement ended? >
       │
       └─── Yes ──▶ [ Withdraw every credential in ]
                    [ the batch, in one action     ]
                             │
                             ▼
                          ( End )
```

**Level 1.3: Project & Batch Management**

**Activity List:**

1. **Create project, create batch.** A client engagement and its divisions.
   Status follows from dates and is maintained by the scheduler, so nobody
   updates it by hand and nobody forgets to.

2. **Define roles the work needs, each with its time rule.** The role declares
   whether payable time comes from an expectation per item or from direct
   measurement. This belongs to the role — to the nature of the work — so that no
   individual can be moved to whichever rule favours them.

3. **Staff individually or in bulk.** Both paths converge on the same result; the
   bulk path adds only the safety a hundred rows require.

4. **Resolve: validate and preview.** ⚠ Nothing is written. The admin sees what
   would happen before it happens, which is what makes a bulk change reversible
   by simply not committing it.

5. **Report per-row outcome.** ⚠ Invalid rows fail alone. An import that rejects
   nine hundred good rows over one bad one is useless to whoever prepared the
   file, so the failure is reported per row and the rest proceed.

6. **Issue unique work ID, set terms on assignment.** Terms sit on the assignment
   rather than the role, so two people in one role may be engaged differently
   without splitting the role in two.

7. **Agreement completed?** ⚠ The system's own decision, not the admin's.
   Staffing may run ahead of signature because planning must not wait; access may
   not, because the client was promised it would not.

8. **Withhold credential regardless of admin.** The failing path cannot be
   overridden. This is not a permission an admin holds and declines to use — it
   is an act the system offers no one.

9. **Withdraw all credentials in one action.** At the end of an engagement,
   access ends for everyone at once. Access that outlives involvement is the
   failure the manual process was most prone to.

10. **Express interest in a project.** *(Worker.)* Running alongside the flow
    above: workers browse projects and record interest, which reaches the
    staffing admin as a candidacy. The worker's interest and the admin's list of
    candidates are one record seen from two sides.

---

## Level 1.4: Work & Time Management

**Name:** Work & Time Management
**Reference:** Use Case Diagram Level 1.4

```
   ( Start )
       │
       ▼
 [ Worker opens their batch ]
       │
       ▼
 < Batch still open? >
       │
       ├─── No ═══▶ [ Read-only: no claim, ] ══▶ ( End )
       │            [ release, or edit     ]
       ▼
 [ Show claimable work and own terms ]
       │
       ▼
 < Holds an operational role? >
       │
       ├─── No ═══▶ [ Cannot claim; coordination ] ══▶ ( End )
       │            [ is not doing the work      ]
       ▼
 [ Claim one task, or many at once ]
       │
       ▼
 < Task already assigned? >           ← decided at the moment of writing
       │
       ├─── Yes ══▶ [ Report which failed ]
       │            [ grant the rest      ]
       │                     │
       └─── No ───▶ [ Grant assignment ]
                             │
       ┌─────────────────────┘
       ▼
 [ Annotation performed outside the system ]
       │
       ▼
 [ Record time · comment · or release ]
       │
       ▼
 [ Compute payable time by the role's rule ]
       │
       ▼
   ( End )
```

**Level 1.4: Work & Time Management**

**Activity List:**

1. **Worker opens their batch.** One place holds everything about one engagement:
   available work, work held, time recorded, and what the batch has earned them.
   A worker deciding whether to take another task weighs all of these at once.

2. **Batch still open?** ⚠ A completed batch is closed to everything — claiming,
   releasing, editing. The record of what was done stands, because payroll has
   been computed from it.

3. **Operational role?** Claiming is confined to operational roles. Coordinating
   a batch is not doing its work, and a managerial role is paid for coordination
   rather than for tasks.

4. **Show claimable work and own terms.** The worker sees the rate on their own
   assignment. They see nobody else's — the same protection applied in both
   directions.

5. **Already assigned?** ⚠ Decided at the moment of writing, not from a list read
   earlier. Two workers claiming in the same instant are resolved by the fact of
   assignment rather than by whoever read the screen first.

6. **Report which failed; grant the rest.** ⚠ A bulk claim that partly fails
   still succeeds in part. One contested item never costs a worker the others.

7. **Work done outside the system.** The annotation happens in the client's
   document. The system cannot observe it, which is why everything after this
   step is reported rather than measured.

8. **Record time, comment, or release.** Comments keep context attached to the
   work rather than dispersing into messages. Released work returns to those who
   can take it, without penalty.

9. **Compute payable time by the role's rule.** Either the role's expectation per
   item, or the worker's own report where the role measures directly. All
   durations in seconds throughout.

10. **Log coordination time.** *(Batch manager.)* A managerial role records the
    time spent coordinating the batch, separately from any task — it is not work
    on the annotation, and it is paid on its own terms (§1.5).

11. **Transfer a task.** *(Project admin.)* Work moves between people without
    erasing that it was done. An admin transfers rather than deleting and
    recreating, because credit for completed work is part of the payment record.

---

## Level 1.5: Payroll & Payment

**Name:** Payroll & Payment
**Reference:** Use Case Diagram Level 1.5

Payroll has two flows that run on different occasions. Earnings accrue
continuously as work is claimed; payment is an occasional deliberate act. They
are drawn separately because conflating them would suggest a worker's earnings
are computed only when someone pays them.

**(a) Establishing what is owed** — recomputed whenever the figure is displayed

```
   ( Start )
       │
       ▼
 [ Take payable time for the assignment (§1.4) ]
       │
       ▼
 < Is this a managerial role? >
       │
       ├─── No ───────▶ [ time ÷ 3600 × rate ]
       │                         │
       │                         │
       └─── Yes ──▶ < Hourly rate set on the assignment? >
                                 │
                    ┌──── Yes ───┴─── No ────┐
                    ▼                        ▼
           [ time ÷ 3600 × rate ]   [ Fixed salary for ]
                    │               [ the engagement   ]
                    │                        │
       ┌────────────┴────────────────────────┘
       ▼
 [ Add any bonus awarded; round once, at the total ]
       │
       ▼
 [ Subtract payments already recorded → OUTSTANDING ]
       │
       ▼
 < Banking details on file? >
       │
       ├─── No ═══▶ [ Owed, but not yet payable — listed ]
       │            [ separately so the gap is visible   ]
       │
       └─── Yes ──▶ [ Appears among those who can be paid ]
                                 │
                                 ▼
                              ( End )
```

**(b) Recording a payment** — performed after money has left the bank

```
   ( Start )
       │
       ▼
 [ Admin pays the worker through the bank, ]
 [ outside this system                     ]
       │
       ▼
 [ Record the payment here, against the ]
 [ person and the work it settles       ]
       │
       ▼
 [ Snapshot the recipient's banking details ]
 [ exactly as they stand at this moment     ]
       │
       ▼
 [ Record is now closed — no amount, recipient, ]
 [ or snapshot may ever be edited              ]
       │
       ▼
 < Recorded in error? >
       │
       ├─── No ───▶ ( End )
       │
       └─── Yes ══▶ [ Archive it — removed from what is ]
                    [ outstanding, but not destroyed    ]
                                 │
                                 ▼
                    < Archived by mistake? >
                                 │
                    ┌──── Yes ───┴─── No ────┐
                    ▼                        ▼
           [ Restore it ]           [ May now be deleted ]
                    │                        │
                    └────────────┬───────────┘
                                 ▼
                              ( End )
```

**Level 1.5: Payroll & Payment**

**Activity List:**

1. **Payable time from §1.4.** Payroll never recomputes effort; it consumes what
   work management established. One calculation, one place.

2. **Managerial role? / Hourly rate set?** Three compensation arrangements decided
   by two questions. Managerial work is paid hourly where the assignment carries
   a rate and by fixed sum otherwise — coordination being sometimes engaged by
   the hour and sometimes for the job.

3. **+ bonus, round once.** Bonuses count from the moment awarded, not at some
   later reckoning. Rounding happens once, at the final total: rounding at
   intermediate steps produces figures that do not reconcile.

4. **− payments recorded.** What remains outstanding is earnings less what has
   been paid. The subtraction is the only relationship between the two, and
   neither is derived from the other.

5. **Banking details on file?** ⚠ The branch that separates two questions the
   business must not confuse: who is *owed* money, and who can *be paid*. A
   worker accrues earnings regardless. The difference names those whose payment
   waits on information only they can supply.

6. **Admin pays via bank, records it here.** The system does not move money. It
   computes what is owed and records what was sent.

7. **Snapshot banking details as they stand now.** ⚠ The recipient's account is
   captured at that moment. A worker changing their account tomorrow cannot alter
   what was paid yesterday — and a dispute can be answered with the record that
   produced the payment.

8. **Record is now closed.** ⚠ No amount, recipient, or snapshot may be edited
   after the fact. This is not a permission finance declines to exercise; there is
   no path to it for anyone.

9. **Recorded in error?** ⚠ Archive, never edit. An archived record is removed
   from what is outstanding without being destroyed, and may be restored if
   archived by mistake. Only an archived record may be deleted. Every correction
   is a further record rather than a revision of an existing one.

10. **Export for reconciliation.** The financial record leaves in a form
    accounting's own spreadsheets consume, to be checked against what the bank
    actually sent. Exporting is itself privileged, since it places data beyond
    the system's protection.

---

## Level 1.6: Agreement Management

**Name:** Agreement Management
**Reference:** Use Case Diagram Level 1.6

```
   ( Start )
       │
       ▼
 [ Author template, marking where ]
 [ each recipient's details belong ]
       │
       ▼
 [ Publish as a version — now immutable ]
       │
       ▼
 [ Issue to recipient; their details ]
 [ resolve into the text now         ]
       │
       ▼
 [ Worker views ]  ── recorded, so a signature can later
       │              be shown to have followed a reading
       ▼
 < Worker signs? >
       │
       ├─── Rejects ══▶ [ REJECTED — final for ] ══▶ ( End )
       │                [ this agreement       ]
       ▼
 [ Bind signature to a digest of ]
 [ the exact text the signer saw ]
       │
       ▼
 [ Company countersigns ]
       │
       ▼
 [ COMPLETED ]
       │
       ▼
   ( End )


   While an agreement waits unsigned, three things may move it:

   [ Compliance extends it ] ──▶ deadline moves; still waiting
   [ Compliance voids it   ] ──▶ VOIDED — may be reinstated
   [ Scheduler expires it  ] ──▶ EXPIRED — may still be extended

   Every one of these, and every step above, appends to a
   permanent record naming its cause and its author.
```

**Level 1.6: Agreement Management**

**Activity List:**

1. **Author template, marking where details belong.** Placeholders rather than
   hand-editing per person — the manual practice that made it impossible to say
   which version anyone had signed.

2. **Publish as version, now immutable.** A published version can never change. A
   correction means a new version, and what was signed against the old one stands
   exactly as signed.

3. **Issue to recipient; details resolve now.** At most one agreement per version
   per recipient, so nobody is bound twice by the same text.

4. **Worker views.** Recorded, and ordered relative to what follows — which is
   what allows a signature to be shown to have followed a reading of the text
   rather than preceded it.

5. **Worker signs?** ⚠ Rejection is final for that agreement. The worker's
   refusal is a legitimate outcome, not an error state, and the system records it
   as one.

6. **Bind signature to digest of exact text.** ⚠ The digest covers the text as the
   signer saw it, with every detail resolved. Later alteration is not merely
   forbidden but detectable — trust in the archive becomes arithmetic.

7. **Extend / Void / Scheduler expires.** ⚠ Three ways an unsigned agreement
   leaves the waiting state: extended before it lapses, voided deliberately, or
   expired by the clock with nobody present. Voiding is reversible; expiry is
   extendable; only rejection is not.

8. **Company countersigns.** The agreement binds both parties, so it is not
   complete on the worker's signature alone. Countersignature is what moves it to
   COMPLETED, and only a completed agreement releases credentials (§1.3).

9. **Every change recorded permanently, in order.** With its cause and its author.
   The record is appended to, never rewritten — a correction adds an entry rather
   than amending one.

10. **Produce the legal document.** On request, the agreement is rendered
    identically for both parties, reproducing names correctly in the signer's own
    script, and remains reproducible unchanged for as long as the record is kept.

---

## Level 1.7: Oversight & Reporting

**Name:** Oversight & Reporting
**Reference:** Use Case Diagram Level 1.7

```
   ( Start )
       │
       ▼
 [ Identify the viewer ]
       │
       ▼
 < What is this viewer entitled to see? >
       │
       ├─── Owner ────────▶ [ The whole business, each figure ]
       │                    [ against the preceding period   ]
       │
       ├─── Admin ────────▶ [ Their own domain only ]
       │
       ├─── Batch manager ▶ [ Their batch: availability and ]
       │                    [ progress — never pay          ]
       │
       └─── Worker ───────▶ [ Their own record: earned, ]
                            [ outstanding, paid         ]
                                     │
       ┌─────────────────────────────┘
       ▼
 [ Compute from the one shared calculation, ]
 [ so no two viewers of the same fact       ]
 [ can ever be shown different figures      ]
       │
       ▼
   ( End )
```

**Level 1.7: Oversight & Reporting**

**Activity List:**

1. **Identify viewer.** Every view begins by establishing who is looking. Scope is
   not a filter applied to a general result; it determines what is fetched at all.

2. **Owner / business overview.** Workforce size and standing, work in progress,
   agreements, money paid — each against the preceding period, so that direction
   of travel is visible without anyone running a report.

3. **Batch manager: availability, progress — never pay.** ⚠ The exclusion is the
   point. Coordinating a batch grants what coordination needs; what each person
   is paid remains between them and finance.

4. **Worker: earned / outstanding / paid.** Three quantities the system never
   conflates. Only the third is money that has reached them.

5. **Same calculation for all viewers of the same fact.** ⚠ A worker's earnings
   and an admin's view of that worker's earnings are one calculation seen twice,
   not two that happen to agree. There is no arrangement of the system in which
   they can disagree.

---

## Level 1.8: Bulk Data Operations

**Name:** Bulk Data Operations
**Reference:** Use Case Diagram Level 1.8

```
                    ( Start )
                        │
                        ▼
            [ Admin supplies a file ]
                        │
                        ▼
            [ RESOLVE: validate every ]
            [ row — write nothing     ]
                        │
                        ▼
              [ Present full preview ]
                        │
                        ▼
                 < Admin commits? >
                   │           │
              No ══╝           │ Yes
                   ║           ▼
                   ║  [ COMMIT: re-validate ]
                   ║  [ every row again     ]
                   ║           │
                   ║           ▼
                   ║   < Row valid now? >
                   ║     │           │
                   ║ No ═╝           │ Yes
                   ║     ║           ▼
                   ║     ║   [ Write row ]
                   ║     ▼           │
                   ║ [ Record row ]  │
                   ║ [ as failed  ]  │
                   ║     ║           │
                   ║     ╚═════┬─────┘
                   ║           ▼
                   ║  [ Report outcome per row ]
                   ║           │
                   ╚═════┬═════┘
                         ▼
                     ( End )
```

**Level 1.8: Bulk Data Operations**

**Activity List:**

1. **Admin supplies a file.** Usually one the system exported earlier, edited
   outside it. Export and import share a shape precisely so that this is the
   ordinary way to change many records.

2. **RESOLVE: validate every row — write nothing.** ⚠ The first phase is
   read-only. Its purpose is to let an admin see the consequence of a change
   before choosing it, which makes not committing a complete remedy.

3. **Present full preview.** Everything that would happen, not a sample. A preview
   that summarises is a preview that hides the row that matters.

4. **Admin commits?** Declining costs nothing, because nothing has been written.

5. **COMMIT: re-validate every row again.** ⚠ The commit does not trust the
   preview. The file may have changed, or the underlying data may have, between
   the two phases — so every fact is established again at the moment of writing.
   The preview is advisory; the commit is authoritative.

6. **Row valid now? / Record row as failed.** ⚠ Rows succeed or fail
   individually. People are matched on the identifier the system issued them, not
   on their name — names being neither unique nor stable.

7. **Report outcome per row.** The admin learns exactly which rows failed and
   why, and can correct and resubmit only those.

8. **Apply bulk change.** The same two-phase discipline governs changes made
   without a file — rates or bonuses altered across many assignments at once.
   Operations that unconditionally clear data are single-phase, having nothing to
   preview.

9. **Paginate every list.** No list returns everything at once, whatever is
   requested of it. This is what keeps the system usable as data grows, and is
   applied to every list rather than to those that have so far grown large.

---

## Conclusion

Drawing the flows exposes what the use cases could only assert: the system's
guarantees live at specific decision points, and each of those points has a
failing branch that must be designed rather than discovered.

Three observations carry into design.

**The failing branch is usually the requirement.** The interesting content of
almost every diagram above is the path taken when something does not succeed — a
duplicated identity, a contested claim, a row that will not validate, an
agreement unsigned when a credential is wanted. A design that implements only the
successful path implements almost none of the requirements.

**Two decisions are drawn so that their outcome is invisible.** In §1.2.1 the
branch on whether an address matches an account converges on an identical
response, and in §1.1 unpermitted capabilities are absent rather than refused.
These are the only places in the specification where a decision deliberately
produces no observable difference, and both exist for the same reason: what the
system reveals about who is in it must not depend on who is asking.

**Validation appears twice on purpose.** §1.8 checks every row in the resolve
phase and checks it again on commit. This is not redundancy to be optimised away
— the two phases answer different questions at different moments, and collapsing
them would mean writing on the strength of a fact established before the admin
had decided.

**Next task:** [Swimlane diagrams](06-swimlane-diagram.md) — the same flows
partitioned by who performs each step, making the handoffs between actors
explicit.
