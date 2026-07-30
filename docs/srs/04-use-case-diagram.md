# Software Requirements Specification

## GenMorphics AI — Annotation Workforce Management System

**Phase 4: Use Case Diagram**

**Method:** Pressman, _Software Engineering: A Practitioner's Approach_ —
Requirements Engineering. Continues from [Phase 1: Inception](01-inception.md),
[Phase 2: QFD](02-qfd.md), and [Phase 3: Usage Scenarios](03-usage-scenarios.md).

---

# 4. Use Case Diagram

The scenarios of §3 follow people through their work. This section decomposes
that work into use cases: what the system does, who initiates each, and how the
whole divides into parts small enough to design and test.

The decomposition proceeds by levels. Level 0 shows the system as a single unit
against everyone who touches it. Level 1 divides it into subsystems. Levels 1.1
onward decompose each subsystem into its own use cases. Every description refers
back to the scenario it serves, as `[Reference: 3.x]`, so that no use case exists
without a person who needed it.

**A note on actors.** A *primary* actor initiates a use case for their own
purpose. A *secondary* actor participates because the use case requires it, but
did not begin it. Two of ours deserve explanation. **Scheduler** is an external
timekeeper that invokes the system on a schedule with nobody present — it is an
actor because the system cannot summon itself. **System** appears as an actor
where the system acts on its own authority rather than at anyone's request:
refusing a duplicate identity, deciding which form of removal applies, withholding
credentials from an unsigned worker. These are the acts §3 describes as offered
to no one, and modelling them as system-initiated keeps that visible.

---

## Level 0

**Name:** GenMorphics AI — Annotation Workforce Management System
**Primary Actor:** Owner, Project Admin, Finance Admin, Compliance Admin,
HR Admin, Worker, Batch Manager
**Secondary Actor:** Identity Provider, Scheduler, System, Accounting,
Legal Counsel

```
   Owner ────────────┐                          ┌──────── Identity Provider
                     │                          │
   Project Admin ────┤    ┌────────────────┐    ├──────── Scheduler
                     │    │                │    │
   Finance Admin ────┤    │  GenMorphics   │    ├──────── System
                     ├────┤       AI       ├────┤
   Compliance ───────┤    │   Workforce    │    ├──────── Accounting
      Admin          │    │   Management   │    │
                     │    │                │    └──────── Legal Counsel
   HR Admin ─────────┤    └────────────────┘
                     │
   Worker ───────────┤
                     │
   Batch Manager ────┘
```

**Level 0: The system and everyone who touches it**

The seven primary actors are the two populations of §1.1 — one owner, four
composed administrators, and workers, of whom some hold a managerial role within
a batch. The secondary actors never pursue a purpose of their own inside the
system: the identity provider authenticates, the scheduler invokes on time,
accounting and legal counsel consume what the system produces without signing in,
and the system itself enforces what no one may override.

---

## Level 1

**Name:** GenMorphics AI (Detailed)
**Primary Actor:** Owner, Project Admin, Finance Admin, Compliance Admin,
HR Admin, Worker, Batch Manager
**Secondary Actor:** Identity Provider, Scheduler, System, Accounting,
Legal Counsel

```
                    ┌──────────────────────────────────────┐
   Owner ───────────┤  1.1  Access & Authorization         ├──── Identity
                    │                                      │      Provider
   HR Admin ────────┤  1.2  Workforce Management           │
                    │                                      │
   Project Admin ───┤  1.3  Project & Batch Management     ├──── System
                    │                                      │
   Worker ──────────┤  1.4  Work & Time Management         │
                    │                                      │
   Finance Admin ───┤  1.5  Payroll & Payment              ├──── Accounting
                    │                                      │
   Compliance ──────┤  1.6  Agreement Management           ├──── Legal
      Admin         │                                      │      Counsel
   Batch Manager ───┤  1.7  Oversight & Reporting          │
                    │                                      │
                    │  1.8  Bulk Data Operations           ├──── Scheduler
                    └──────────────────────────────────────┘
```

**Level 1: The eight subsystems**

**Description:**

1. **Access & Authorization.** Establishes who a person is and what they may do.
   Authentication is delegated entirely to an external identity provider; the
   system holds no passwords. Authority is composed from permissions into named
   roles, granted to accounts and revoked from them. What a person may not do is
   absent from their view rather than refused. [Reference: 3.1, 3.2, 3.3]

2. **Workforce Management.** Everything about the people: profiles,
   qualifications, supporting documents, banking details, the catalogue of
   skills, declared availability, and standing — including suspension, appeal,
   restoration, and the two forms of removal. [Reference: 3.2, 3.7]

3. **Project & Batch Management.** The structure of work: projects divided into
   batches, the roles each batch requires, the people assigned to those roles on
   their own terms, and the credentials by which they reach the client's
   documents. [Reference: 3.4]

4. **Work & Time Management.** The units of work themselves — claimed, released,
   commented on, and timed. Includes the guarantee that no task is ever held by
   two people, and the conversion of effort into payable time. [Reference: 3.2,
   3.4]

5. **Payroll & Payment.** Turns recorded work into money owed through one
   authoritative calculation, distinguishes what is owed from what can be paid,
   and records payments made outside the system in a form that cannot afterwards
   be revised. [Reference: 3.2, 3.5]

6. **Agreement Management.** The full life of a confidentiality agreement:
   authoring, versioning, issuance, signature, countersignature, and the
   unalterable history that makes it provable. [Reference: 3.2, 3.6]

7. **Oversight & Reporting.** What each party sees of the whole — the business
   against recent history for administrators, their own work and earnings for
   workers, and a batch's progress and availability for the person coordinating
   it. [Reference: 3.2, 3.3, 3.8]

8. **Bulk Data Operations.** Cross-cutting. Import, export, pagination, and the
   two-phase discipline by which a change to a thousand records is previewed
   before it is written. Serves every subsystem above rather than belonging to
   any. [Reference: 3.4, 3.5]

---

## Level 1.1: Access & Authorization

**Name:** Access & Authorization
**Primary Actor:** Owner, Worker, all Admins
**Secondary Actor:** Identity Provider, System

```
                     ┌──────────────────────────────────┐
   Worker ───────────┤  Sign In                         ├──── Identity
                     │                                  │      Provider
   Owner ────────────┤  Compose Administrative Role     │
                     │                                  │
   Owner ────────────┤  Grant / Revoke Role             │
                     │                                  │
   All Admins ───────┤  Exercise Permission             ├──── System
                     │                                  │
                     │  Withhold Unpermitted Capability ├──── System
                     └──────────────────────────────────┘
```

**Description:**

1. **Sign In.** A person authenticates through the external identity provider. An
   account comes into being on first sign-in; no administrator creates one.
   [Reference: 3.1, 3.2]

2. **Compose Administrative Role.** The owner selects the permissions a role
   should carry and names it. No change to software is required, and the set of
   administrative functions is not fixed. [Reference: 3.3]

3. **Grant / Revoke Role.** The owner grants a role to an account or withdraws
   it. Grants are additive: an account keeps everything it already held, so
   promotion costs a worker nothing. Every grant records who made it and when.
   [Reference: 3.2, 3.3]

4. **Exercise Permission.** Every protected act is gated on a permission rather
   than a role name, and permissions distinguish acting on one's own data from
   acting on anyone's. [Reference: 3.2, 3.5, 3.7]

5. **Withhold Unpermitted Capability.** *System-initiated.* What a person may not
   do is absent from their view rather than refused, so the interface discloses
   nothing to someone probing it. [Reference: 3.2]

---

## Level 1.2: Workforce Management

**Name:** Workforce Management
**Primary Actor:** Worker, HR Admin
**Secondary Actor:** System, Scheduler

```
                     ┌──────────────────────────────────┐
   Worker ───────────┤  Maintain Profile                │
                     │                                  │
   Worker ───────────┤  Upload Supporting Document      │
                     │                                  │
   System ───────────┤  Refuse Duplicate Identity       │
                     │                                  │
   Worker ───────────┤  Declare Skills                  │
                     │                                  │
   HR Admin ─────────┤  Maintain Skills Catalogue       │
                     │                                  │
   Worker ───────────┤  Declare Availability            ├──── Scheduler
                     │                                  │
   HR Admin ─────────┤  Inspect Worker                  │
                     │                                  │
   HR Admin ─────────┤  Suspend / Restore Worker        │
                     │                                  │
   Worker ───────────┤  Contest Suspension              │
                     │                                  │
   System ───────────┤  Determine Form of Removal       │
                     └──────────────────────────────────┘
```

**Description:**

1. **Maintain Profile.** The worker supplies and corrects their identity,
   qualifications, and banking details. An HR admin may do so on their behalf,
   and every such change names the admin who made it. [Reference: 3.2, 3.7]

2. **Upload Supporting Document.** A CV, proof of identity, or photograph.
   Format and size are enforced, and a refusal states its reason at the point of
   upload. [Reference: 3.2]

3. **Refuse Duplicate Identity.** *System-initiated.* Identifying details
   matching an existing account are refused, since a second account would divide
   a person's work history and corrupt what they are paid. [Reference: 3.2]

4. **Declare Skills.** The worker selects from the catalogue. Free description is
   not permitted — a capability no one else uses cannot be matched to work.
   [Reference: 3.2]

5. **Maintain Skills Catalogue.** The HR admin creates, orders, and groups
   skills, and may correct a worker's claims. A skill still claimed or still
   required cannot be removed beneath its dependents. [Reference: 3.7]

6. **Declare Availability.** The worker states hours expected free per day ahead.
   Declaring none is itself recorded. The scheduler discards past declarations.
   [Reference: 3.2]

7. **Inspect Worker.** The HR admin reaches everything about one person in one
   place — bounded by function: administering the workforce does not entail
   seeing banking details. [Reference: 3.7]

8. **Suspend / Restore Worker.** Suspension ends access while preserving
   assignments, work and earnings, and creates the means of appeal in the same
   act. [Reference: 3.2, 3.7]

9. **Contest Suspension.** The worker states their case once; an admin decides.
   The route never discloses whether an address belongs to an account.
   [Reference: 3.2]

10. **Determine Form of Removal.** *System-initiated.* A worker with any history
    has access withdrawn and their record preserved; only an account with none is
    erased. The choice is not the administrator's to make. [Reference: 3.7]

---

## Level 1.3: Project & Batch Management

**Name:** Project & Batch Management
**Primary Actor:** Project Admin, Worker
**Secondary Actor:** System, Scheduler

```
                     ┌──────────────────────────────────┐
   Project Admin ────┤  Manage Project                  ├──── Scheduler
                     │                                  │
   Project Admin ────┤  Manage Batch                    ├──── Scheduler
                     │                                  │
   Project Admin ────┤  Define Batch Role               │
                     │                                  │
   Project Admin ────┤  Assign Worker to Role           │
                     │                                  │
   Worker ───────────┤  Express Interest in Project     │
                     │                                  │
   Project Admin ────┤  Issue Credential                │
                     │                                  │
   System ───────────┤  Withhold Credential Pending     │
                     │  Agreement                       │
                     │                                  │
   Project Admin ────┤  Withdraw Credentials in Bulk    │
                     └──────────────────────────────────┘
```

**Description:**

1. **Manage Project.** A client engagement, with its dates and required skills.
   Status follows from dates; the scheduler maintains it without anyone editing
   it by hand. [Reference: 3.4]

2. **Manage Batch.** A division of a project's work, with its own dates and
   status. [Reference: 3.4]

3. **Define Batch Role.** The roles the work requires — _Annotator_, _QA_, _QA2_
   — each declaring how effort converts to payable time. The choice belongs to
   the work, not the person. [Reference: 3.4]

4. **Assign Worker to Role.** A person placed in a role, receiving an identifier
   unique within the batch and the terms of their engagement. Terms sit on the
   assignment, so two people in one role may be engaged differently.
   [Reference: 3.2, 3.4]

5. **Express Interest in Project.** The worker's interest and the admin's view of
   a candidate are the same record seen from two sides. [Reference: 3.2, 3.4]

6. **Issue Credential.** Access to the client's documents, held encrypted, its
   disclosure recorded against whoever obtained it. [Reference: 3.2, 3.4]

7. **Withhold Credential Pending Agreement.** *System-initiated.* No credential
   reaches a worker without a completed agreement, whatever the admin intends.
   Staffing may run ahead of signature; access may not. [Reference: 3.2, 3.4]

8. **Withdraw Credentials in Bulk.** Every credential in a batch revoked in one
   action when the engagement ends. [Reference: 3.4]

---

## Level 1.4: Work & Time Management

**Name:** Work & Time Management
**Primary Actor:** Worker, Project Admin, Batch Manager
**Secondary Actor:** System

```
                     ┌──────────────────────────────────┐
   Project Admin ────┤  Manage Task                     │
                     │                                  │
   Worker ───────────┤  Claim Task                      │
                     │                                  │
   System ───────────┤  Resolve Simultaneous Claim      │
                     │                                  │
   Worker ───────────┤  Release Task                    │
                     │                                  │
   Worker ───────────┤  Record Time Required            │
                     │                                  │
   Worker ───────────┤  Comment on Work                 │
                     │                                  │
   Batch Manager ────┤  Log Coordination Time           │
                     │                                  │
   Project Admin ────┤  Transfer Task                   │
                     │                                  │
   System ───────────┤  Compute Payable Time            │
                     │                                  │
   System ───────────┤  Close Completed Batch           │
                     └──────────────────────────────────┘
```

**Description:**

1. **Manage Task.** Units of work within a batch, each referring to an item in
   the external document. Status is free text and governs nothing.
   [Reference: 3.4]

2. **Claim Task.** A worker takes work, singly or in bulk. Claiming is confined
   to operational roles — coordinating a batch is not doing its work. Where a
   bulk claim partly fails, the rest still succeed and the worker is told which
   did not. [Reference: 3.2]

3. **Resolve Simultaneous Claim.** *System-initiated.* Where two workers claim
   the same task at once, one receives it and the other is told plainly. No task
   is ever held twice, and the loser of the race loses nothing else.
   [Reference: 3.2]

4. **Release Task.** Work a worker cannot complete returns to those who can take
   it, without penalty. [Reference: 3.2]

5. **Record Time Required.** The worker reports the time a task took, where their
   role is one that measures directly. [Reference: 3.2]

6. **Comment on Work.** Context stays attached to the task rather than dispersing
   into message threads. [Reference: 3.2]

7. **Log Coordination Time.** A batch manager records time spent coordinating,
   separately from any task. [Reference: 3.8]

8. **Transfer Task.** An admin moves work between people without erasing that it
   was done — credit for completed work is part of the payment record.
   [Reference: 3.4]

9. **Compute Payable Time.** *System-initiated.* Effort becomes payable time
   either by the role's expectation per item or by direct measurement, according
   to the role. All durations are held in seconds. [Reference: 3.2, 3.4]

10. **Close Completed Batch.** *System-initiated.* A completed batch accepts no
    further claiming, releasing, or editing. [Reference: 3.2]

---

## Level 1.5: Payroll & Payment

**Name:** Payroll & Payment
**Primary Actor:** Finance Admin, Worker
**Secondary Actor:** System, Accounting

```
                     ┌──────────────────────────────────┐
   System ───────────┤  Calculate Earnings              │
                     │                                  │
   Finance Admin ────┤  Award Bonus                     │
                     │                                  │
   Finance Admin ────┤  View Amount Outstanding         │
                     │                                  │
   Finance Admin ────┤  View Who Can Be Paid            │
                     │                                  │
   Finance Admin ────┤  Record Payment                  ├──── Accounting
                     │                                  │
   System ───────────┤  Snapshot Banking Details        │
                     │                                  │
   Finance Admin ────┤  Archive / Restore / Delete      │
                     │  Payment Record                  │
                     │                                  │
   Worker ───────────┤  View Own Earnings and Payments  │
                     │                                  │
   Finance Admin ────┤  Export Financial Record         ├──── Accounting
                     └──────────────────────────────────┘
```

**Description:**

1. **Calculate Earnings.** *System-initiated.* One authoritative calculation,
   used wherever a figure appears, so the worker's view and the admin's are one
   calculation seen twice rather than two that happen to agree. Rounding occurs
   once, at the final total. [Reference: 3.2, 3.5]

2. **Award Bonus.** An addition to an assignment, counted from the moment it is
   awarded. [Reference: 3.5]

3. **View Amount Outstanding.** Earnings computed, less payments recorded.
   [Reference: 3.5]

4. **View Who Can Be Paid.** Those outstanding *and* holding the banking details
   a transfer requires. The difference between this and the previous use case
   names the people whose payment is blocked on information only they can
   supply. [Reference: 3.5]

5. **Record Payment.** Payment made through the bank is recorded here against the
   person and the work it settles. The system does not move money.
   [Reference: 3.5]

6. **Snapshot Banking Details.** *System-initiated.* The record captures the
   recipient's details as they stood at that moment, so a later change to their
   account cannot alter what was already paid. No amount, recipient, or snapshot
   is editable afterwards. [Reference: 3.2, 3.5]

7. **Archive / Restore / Delete Payment Record.** A record made in error is
   archived rather than destroyed, and may be restored. Only an archived record
   may be deleted. Corrections are made by further records. [Reference: 3.5]

8. **View Own Earnings and Payments.** The worker sees three distinct quantities
   — earned, outstanding, paid — which the system never conflates.
   [Reference: 3.2]

9. **Export Financial Record.** In a form accounting's spreadsheets consume.
   Exporting is itself a privileged capability, since it places data beyond the
   system's protection. [Reference: 3.5]

---

## Level 1.6: Agreement Management

**Name:** Agreement Management
**Primary Actor:** Compliance Admin, Worker
**Secondary Actor:** System, Scheduler, Legal Counsel

```
                     ┌──────────────────────────────────┐
   Compliance ───────┤  Author Template                 │
                     │                                  │
   Compliance ───────┤  Publish Template Version        │
                     │                                  │
   Compliance ───────┤  Issue Agreement                 │
                     │                                  │
   Worker ───────────┤  View Agreement                  │
                     │                                  │
   Worker ───────────┤  Sign / Reject Agreement         │
                     │                                  │
   Compliance ───────┤  Countersign Agreement           │
                     │                                  │
   Compliance ───────┤  Extend / Void / Reinstate       │
                     │                                  │
   Scheduler ────────┤  Expire Overdue Agreement        │
                     │                                  │
   System ───────────┤  Bind Signature to Text          │
                     │                                  │
   System ───────────┤  Record State Change             │
                     │                                  │
   Compliance ───────┤  Produce Legal Document          ├──── Legal Counsel
                     └──────────────────────────────────┘
```

**Description:**

1. **Author Template.** The agreement is written within the system, marking where
   each recipient's own details belong rather than editing them by hand per
   person. [Reference: 3.6]

2. **Publish Template Version.** A published version can never be altered; a
   change means a new version, and what was signed against the old one stands.
   [Reference: 3.6]

3. **Issue Agreement.** Sent to a recipient, their details resolving into it at
   that moment. At most one agreement exists per version and recipient.
   [Reference: 3.6]

4. **View Agreement.** The worker reads it. Viewing is recorded, so a signature
   can later be shown to have followed a reading. [Reference: 3.2, 3.6]

5. **Sign / Reject Agreement.** The worker signs, or declines — rejection being
   final for that agreement. [Reference: 3.2, 3.6]

6. **Countersign Agreement.** The company signs in turn, completing it.
   [Reference: 3.6]

7. **Extend / Void / Reinstate.** An agreement may be extended before it lapses,
   voided, and reinstated if voided in error. [Reference: 3.6]

8. **Expire Overdue Agreement.** *Scheduler-initiated.* An agreement whose date
   passes unsigned lapses without anyone acting. [Reference: 3.6]

9. **Bind Signature to Text.** *System-initiated.* A digest over the exact text
   the signer saw makes any later alteration detectable rather than merely
   forbidden. [Reference: 3.6]

10. **Record State Change.** *System-initiated.* Every change is recorded
    permanently with its cause and its author, in an order that cannot be
    rewritten. [Reference: 3.6]

11. **Produce Legal Document.** Identical for both parties, correct in the
    signer's own script, reproducible unchanged for as long as the record is
    kept. [Reference: 3.6]

---

## Level 1.7: Oversight & Reporting

**Name:** Oversight & Reporting
**Primary Actor:** Owner, All Admins, Worker, Batch Manager
**Secondary Actor:** System

```
                     ┌──────────────────────────────────┐
   Owner ────────────┤  View Business Overview          │
                     │                                  │
   Project Admin ────┤  View Batch Progress             │
                     │                                  │
   Batch Manager ────┤  View Team Availability          │
                     │                                  │
   Worker ───────────┤  View Own Work History           │
                     │                                  │
   Worker ───────────┤  View Own Participation          │
                     │                                  │
   System ───────────┤  Scope View to Viewer            │
                     └──────────────────────────────────┘
```

**Description:**

1. **View Business Overview.** Workforce size and standing, profile completion,
   projects and batches running, agreements, and money paid — each against the
   period before it, so a change of direction is visible without a report being
   run. [Reference: 3.3]

2. **View Batch Progress.** What has been claimed, completed, and timed, without
   interrupting anyone to ask. [Reference: 3.4, 3.8]

3. **View Team Availability.** The declared hours of a batch's members,
   aggregated for whoever plans the work. [Reference: 3.8]

4. **View Own Work History.** A worker's record of what they did, in which batch
   and role, and when — retained after the work ends, because it is what they
   draw on to question a payment. [Reference: 3.2]

5. **View Own Participation.** The projects a worker has taken part in and what
   each yielded. [Reference: 3.2]

6. **Scope View to Viewer.** *System-initiated.* Every view is confined to what
   its viewer may see: a worker's own record and no other, a batch manager's
   batch but never its pay. [Reference: 3.2, 3.8]

---

## Level 1.8: Bulk Data Operations

**Name:** Bulk Data Operations
**Primary Actor:** Project Admin, Finance Admin, HR Admin
**Secondary Actor:** System, Accounting

```
                     ┌──────────────────────────────────┐
   Project Admin ────┤  Import Records                  │
                     │                                  │
   System ───────────┤  Validate and Preview            │
                     │                                  │
   System ───────────┤  Commit with Per-Row Outcome     │
                     │                                  │
   All Admins ───────┤  Export Records                  ├──── Accounting
                     │                                  │
   System ───────────┤  Paginate List                   │
                     │                                  │
   Project Admin ────┤  Apply Bulk Change               │
                     └──────────────────────────────────┘
```

**Description:**

1. **Import Records.** Work, teams, and payroll fields arrive as spreadsheets and
   are loaded in bulk. People are matched on the identifier the system issued
   them, never on name. [Reference: 3.4]

2. **Validate and Preview.** *System-initiated.* Nothing is written until the
   whole change has been checked and shown. What the preview displays is
   advisory. [Reference: 3.4]

3. **Commit with Per-Row Outcome.** *System-initiated.* The commit re-establishes
   every fact for itself, since the file may have changed since the preview. Rows
   succeed or fail individually and the outcome is reported per row — an import
   failing entirely on one bad line is useless to whoever prepared it.
   [Reference: 3.4]

4. **Export Records.** Shaped so that what is exported can be imported again, so
   the ordinary way to change a hundred records is to export, edit, and import.
   Exporting is privileged, and no export reveals more than its requester could
   already see. [Reference: 3.4, 3.5]

5. **Paginate List.** *System-initiated.* Every list is paginated and none can be
   persuaded to return everything at once. [Reference: 3.4]

6. **Apply Bulk Change.** Rates, bonuses, and other fields changed across many
   assignments at once, under the same two-phase discipline. Unconditional
   clearances are single-phase, having nothing to preview. [Reference: 3.4]

---

## Conclusion

The decomposition yields eight subsystems and sixty-five use cases, each
traceable to a scenario in §3 and through it to a viewpoint in §1.2. Nothing
appears here that no stakeholder needed.

Two observations are worth carrying into design.

**Sixteen use cases are initiated by the system or the scheduler rather than by a
person** — fifteen by the system, one by the clock. They
are not conveniences an administrator invokes; they are acts the system performs
on its own authority and offers to nobody — refusing a duplicate identity,
resolving a simultaneous claim, withholding a credential from an unsigned worker,
snapshotting banking details, deciding which form of removal applies. Modelling
them as use cases with the **System** as primary actor keeps them from being
mistaken for implementation detail. Each traces to a §1.3 negotiation, and each
must be tested as deliberately as any capability a person invokes.

**Subsystem 1.8 belongs to no actor.** Bulk operations, pagination and export
serve every other subsystem without being anyone's feature. It is separated here
precisely because a capability that belongs to everyone tends to be owned by no
one, and its rules — preview before commit, per-row outcomes, matching on issued
identifier — are the rules most easily lost when a new list endpoint is added in
a hurry.

**Next task:** [Activity Diagrams](05-activity-diagram.md) — the flow of control within each use case,
including the paths taken when a step fails.
