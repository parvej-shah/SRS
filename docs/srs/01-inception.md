# Software Requirements Specification

## GenMorphics AI — Annotation Workforce Management System

**Phase 1: Inception**

**Method:** Pressman, _Software Engineering: A Practitioner's Approach_ —
Requirements Engineering, Task 1 (Inception).
**Date:** 2026-07-30

---

# 0. Introduction

Data annotation — labelling, transcribing, categorizing and reviewing raw data so
that it can train machine-learning models — is delivered by companies that
assemble distributed contract workforces for the purpose. The work is granular,
high-volume, and performed by many people at once, often across time zones and
often by people who never meet.

GenMorphics AI is such a company. Its annotation work is carried out not in a
specialized annotation platform but in **ordinary shared documents — spreadsheets
and word-processor files**. Those documents hold the work product and nothing
else. They do not know who was assigned a unit of work, which role that person
held, how long they spent, what they are owed, or whether they were permitted to
open the file at all. Anyone holding the link can read and alter anyone else's
work, without trace.

Everything the business needs in order to _run_ on that work — staffing,
assignment, time, payment, confidentiality, access — is therefore managed outside
those documents, in further spreadsheets and message threads. That is the problem
this system addresses. It cannot and does not replace the documents where
annotation happens; it supplies the structure, the records, the money, and the
proof that those documents cannot hold themselves.

## Purpose

This document describes the software requirements analysis of the GenMorphics AI
Annotation Workforce Management System. It records the functional,
non-functional, and supporting requirements, and establishes the requirements
baseline against which the system is developed. Requirements are organized by
topic and stated so that each can be verified.

The SRS is the official means of communicating requirements between the
stakeholder community and the development team, and the common reference point
for both. It is expected to evolve as users and developers work together to
validate, clarify and expand it.

## Intended Audience

| Audience                                                              | Use of this document                                                                                  |
| --------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| **Owners**                                                            | Verify that the system serves the business and that delegation of authority is safe                   |
| **Admins** (project, finance, compliance, HR, and others as composed) | Confirm that their functions are supported and appropriately bounded                                  |
| **Workers**                                                           | Confirm that work, time, and payment are recorded fairly, and personal data protected, apply for work |
| **Developers**                                                        | Plan milestones; derive design and implementation; trace code to stated requirements                  |
| **Testers**                                                           | Derive test plans and test cases from each documented requirement                                     |
| **Legal and compliance counsel**                                      | Confirm that NDA handling produces enforceable, provable agreements                                   |

## Conclusion

Identifying the audience clarifies who each requirement must satisfy and who is
entitled to judge whether it has been met. The remainder of this document is
written for those readers.

---

# 1. Inception

Inception establishes a basic understanding of the problem, the people who want a
solution, the nature of the solution sought, and the effectiveness of preliminary
communication between stakeholders and the development team.

## 1.1 Identifying the Stakeholders

A stakeholder is anyone who benefits directly or indirectly from the system.

### Direct stakeholders

Those who use the system. There are **two kinds of account — User and Admin** —
and they are not two groups of people. They are states an account occupies: a
user may be granted administrative authority at any time, and it may be withdrawn
just as readily.

**1. Users (workers)** — perform annotation work and are paid for it. Every
account begins here. A user holds no platform-wide authority; what they may do
comes entirely from the work assigned to them:

- **Operational worker** — performs annotation tasks under a role the work
  defines: _Annotator_, _QA_, _QA2_, or whatever that batch requires.
- **Batch manager** — coordinates one batch: plans staffing from declared
  availability and records coordination time. This is still a user. The
  authority is granted by the batch, bounded by it, and ends with it.

**2. Admins** — hold platform-wide authority. Admins are not a fixed list of job
titles. An admin is **an account granted a set of permissions**, and any coherent
set may be composed and named. The functions GenMorphics AI operates today
illustrate the pattern rather than exhaust it:

| Admin                | Composed to handle                                      |
| -------------------- | ------------------------------------------------------- |
| **Project admin**    | Projects, batches, staffing, delivery                   |
| **Finance admin**    | Payroll computation, payment records, financial exports |
| **Compliance admin** | NDA templates, issuance, countersignature, audit trail  |
| **HR admin**         | Workforce lifecycle — records, status, skills, appeals  |

Tomorrow's organization may need a _quality admin_, a _recruitment admin_, or a
single _operations admin_ holding several of these at once. Each is created by
selecting permissions, not by modifying software.

**3. Owner** — an admin holding every permission, including the authority to
create admins and decide what each may do. Ultimately accountable for the
platform.

### Movement between the two

A person who has annotated for six months and understands the work is often the
right person to coordinate it. The system supports this directly: **granting an
account a role promotes it; revoking the role returns it.** Authority is added to
an account rather than exchanged for it, so a promoted worker keeps their work
history, their earnings record, and their batch assignments — and may continue
working while administering.

This means the two populations overlap, and an account may sit in both at once: a
finance admin who is also an Annotator on a live batch is an ordinary situation,
not an anomaly. §1.3 records what this implies for separation of duties.

### Indirect stakeholders

Those affected by the system who never sign in.

1. **Clients** — commission the annotation work and receive its output
2. **Accounting** — execute the bank transfers the system computes and records
3. **Legal counsel** — rely on the NDAs the system produces
4. **Development team** — build and maintain the system

### The organizational structure

```
                             ┌─────────┐
                             │  OWNER  │  all permissions
                             └────┬────┘
                                  │
                composes permission sets into named admins
                                  │
   ┌──────────────┬───────────────┼───────────────┬──────────────┐
   │              │               │               │              │
┌──┴───────┐ ┌────┴─────┐ ┌───────┴────┐ ┌────────┴──┐ ┌─────────┴────────┐
│ Project  │ │ Finance  │ │ Compliance │ │    HR     │ │  ( any further   │
│  admin   │ │  admin   │ │   admin    │ │   admin   │ │  admin the org   │
└──┬───────┘ └──────────┘ └────────────┘ └───────────┘ │  later needs )   │
   │                                                    └──────────────────┘
   │  creates projects, batches, and the roles each batch needs
   ▼
┌────────────────────────────────────────────────┐
│  PROJECT                                       │
│  ┌──────────────────────────────────────────┐  │
│  │  BATCH                                   │  │
│  │  ┌────────────────────────────────────┐  │  │
│  │  │  Batch roles (named by the work):  │  │  │
│  │  │    • Annotator      operational    │  │  │
│  │  │    • QA             operational    │  │  │
│  │  │    • QA2            operational    │  │  │
│  │  │    • Batch manager  managerial     │  │  │
│  │  └─────────────────┬──────────────────┘  │  │
│  └────────────────────┼─────────────────────┘  │
└───────────────────────┼────────────────────────┘
                        │  a user is assigned to a role in a batch
                        ▼
                  ┌───────────┐
                  │   USER    │  every account starts here
                  └─────┬─────┘
                        │
                        └──── granted an admin role ────┐
                              (promotion; revocable,    │
                               work history retained)   │
                                                        ▲
                                    ────────────────────┘
                                    an account may hold both at once
```

**Fig IF.1: Account types, admin composition, and work hierarchy**

Two properties of this structure generate the access-control requirement.

**Authority has two independent origins.** A user's comes from the work assigned
to them and ends with it; an admin's comes from the permissions granted to their
account and applies platform-wide. The same person may be an Annotator on one
batch and a QA on another, simultaneously, at different rates — while separately
holding, or not holding, administrative authority. A batch manager coordinating
twenty people holds nothing outside that batch.

**Admins are composed, not enumerated.** Because an admin is a named set of
permissions rather than a fixed job title, the organization defines its own
administrative functions and revises them as it changes. This is a requirement in
its own right, not a convenience: the business must be able to reorganize without
a software release.

## 1.2 Stakeholder Viewpoints

Each stakeholder group was consulted for what it needs from the system. The
requirements are recorded here as expressed, before reconciliation; §1.3 resolves
the conflicts between them.

### a. Owner Viewpoint

The owner runs GenMorphics AI and is accountable for delivery, payment, finding talented workers and the
handling of client data. The problems identified:

1. The operation cannot be run by one person, but delegating work in spreadsheets
   means handing over the whole file.
2. No reliable answer to "who currently has access to this client's data".
3. No evidence that a worker signed an NDA before being given access.
4. Payroll figures that cannot be reproduced a month later.
5. Growth is limited by how much coordination one person can hold in their head.
6. Finding workers with the appropriate skills for a project's requirements
   means asking around, since capability is held in memory rather than recorded.
7. Talented people are lost before they start: there is no single place for them
   to register, present their qualifications, and be considered.
8. The people who best understand the work are the ones who have been doing it,
   but there is no way to give a proven worker more responsibility without
   handing them a shared file and hoping.
9. Trusting someone with one area means trusting them with all of it.

Requirements expressed:

1. Delegate authority by function, without surrendering control.
2. **Compose a new kind of admin by selecting the permissions it should hold**,
   and name it — so that as the organization grows or reorganizes, the software
   need not change. Today's finance, compliance, HR and project admins are the
   first four; there will be others.
3. **Promote a proven worker to an administrative role, and demote them again** —
   without creating a second account, and without losing their work history or
   earnings record.
4. Grant each admin only their own area: managing agreements must not require
   seeing what everyone earns.
5. See the state of every project and batch without asking anyone.
6. One place where prospective workers register, present their qualifications,
   and become findable — so that talent is not lost for want of a route in.
7. Find workers by recorded capability and availability rather than by memory.
8. Guarantee that every worker has signed an NDA before receiving access.
9. Withdraw a person's access completely and immediately when they leave.
10. Retain the work and payment record even after a worker departs.
11. Author agreements freely, in an editor answering to compliance rather than to
    the constraints of any one template.
12. Know not merely how many hours a worker is free but _when_ — the hours of the
    day they intend to work — so that coverage can be planned across a day.

### b. Project Admin Viewpoint

Responsible for structuring client engagements and staffing them.

1. Create projects and divide them into batches of work.
2. Define the roles each batch needs — _Annotator_, _QA_, _QA2_ — with their own
   pay rates and time expectations, since different work demands different roles.
3. Find workers by capability, rather than by asking around.
4. See who is available before committing to a delivery date.
5. Staff a batch of many people without a conversation per person.
6. Assign each worker a work identifier that is unique within the batch.
7. Track progress of a batch without interrupting the people doing the work.
8. Issue each worker access to the documents holding their work, and withdraw it
   when their involvement ends.
9. Import and export work in bulk, because work arrives from clients as
   spreadsheets and must be returned the same way.
10. Have project and batch status reflect reality without manual upkeep.

### c. Finance Admin Viewpoint

Responsible for computing what is owed and recording what was paid.

1. Earnings computed automatically from recorded work, not calculated by hand.
2. One authoritative formula, so two people computing the same figure agree.
3. Support for differing compensation: measured time, expected handling time per
   item, hourly managerial rate, fixed salary.
4. See who is owed money and how much, without assembling it from sources.
5. Record each payment made, against the person and the work it settles.
6. A payment record that cannot be altered after the fact.
7. Payment details that remain true to what was paid, even if the worker later
   changes their bank account.
8. Export financial data for reconciliation with bank statements.
9. Award bonuses where warranted, and have them counted.

### d. Compliance Admin Viewpoint

Responsible for confidentiality agreements and the proof they were made.

1. Author NDA templates in the system rather than in a word processor.
2. Insert per-recipient details automatically, rather than editing names by hand.
3. Publish a version and know it cannot be altered afterward.
4. Issue an NDA to a worker and see whether they have viewed, signed, or ignored
   it.
5. Countersign on behalf of the company.
6. Prove which exact text a person signed, and that it has not changed since.
7. A complete history of every state change, that cannot be rewritten.
8. Produce a PDF suitable for legal proceedings.
9. Expire, extend, void, or reject an agreement as circumstances require.
10. Avoid the cost and dependency of a third-party e-signature service.

### e. HR Admin Viewpoint

Responsible for the workforce itself.

1. Collect the identity, qualification and banking data needed to engage and pay
   a worker.
2. Prevent one person holding two accounts.
3. Maintain a controlled catalogue of skills, so capability means the same thing
   across the workforce.
4. Find people by skill, availability, and history.
5. See everything about one worker in one place — profile, skills, roles, work,
   earnings.
6. Suspend a worker's access without destroying their record.
7. Give a suspended worker a fair route to contest the decision.
8. Restore access when a suspension is lifted or an appeal succeeds.
9. Export workforce data for reporting.

### f. Worker Viewpoint

Workers were consulted as the people whose livelihood and personal data the
system holds.

1. See work suited to their skills, and express interest in projects.
2. Declare availability in advance, so staffing does not arrive unannounced.
3. Claim work, with certainty that nobody else holds the same unit.
4. Release work they cannot complete, without penalty.
5. Record the time they spent.
6. Comment on work, so context is not lost in message threads.
7. Do all of this in one place per batch — claim, time, comment, release, and
   see what that batch has earned them — rather than across scattered screens.
8. See earnings rise as work is done, not only once someone declares a batch
   finished, so that a batch can be judged worth the time before committing more
   of it.
9. Know the terms they are working under — the rate, any bonus — rather than
   discovering what work paid only once it is done.
10. See their own earnings, per batch and in total, without asking.
11. See what has actually been paid to them, and how it differs from what they
    have earned.
12. Correct their own personal and banking details.
13. Reach their own access credentials for the documents they work in.
14. Know that their identity documents and bank details are seen only by those
    who must see them.
15. Contest a suspension.
16. Advance: take on coordination or administrative responsibility as they prove
    themselves, without abandoning the work and earnings history they have built.
17. Register and be considered without needing to know someone already inside.
18. Be able to update their profile picture instead of using default google account photo.

### g. Batch Manager Viewpoint

A worker holding a managerial role within one batch.

1. See the availability declared by the batch's members, to plan staffing.
2. See progress on the batch they coordinate.
3. Record their own managerial time, and be paid for it.
4. Reach the work of the batch they manage — and nothing beyond it.

### h. Indirect Stakeholder Viewpoints

**Clients** require that their data be seen only by workers under a signed NDA,
that access ends when involvement ends, and that work is delivered on schedule.
They do not use the system.

**Accounting** requires exports whose columns are stable, since their own
spreadsheets are built on them.

**Legal counsel** requires that an NDA produced by the system be admissible: the
exact text, the identity of the signatories, the time of signature, and evidence
that none of it changed afterward.

## 1.3 Working towards Collaboration

Stakeholders want incompatible things. Conflicts were identified and resolved
through negotiation; several requests were rejected outright.

### Conflicts

1. Admins want full visibility of everyone; workers surrendered identity and
   banking data only because payment required it.
2. Finance requires immutable payment history; workers must be able to correct
   their bank details.
3. Compliance requires signed agreements to be unalterable; operations wants to
   fix mistakes in them.
4. Project admins want status labels meaning whatever a client engagement
   requires; developers want states the system can reason about.
5. Operations wants bulk operations at spreadsheet scale; bulk writes are
   hazardous and hard to undo.
6. Batch managers want to see the pay of the people they coordinate; finance
   holds payroll as a platform-wide responsibility.
7. Workers want to know why a capability is unavailable; disclosing that a
   capability exists tells a prospective attacker what to attack.
8. Owners want new admin functions defined as the business changes; developers
   cannot rebuild the system for each reorganization.
9. Owners want to promote proven workers into administrative roles; finance
   observes that an admin who is also a paid worker may end up approving their
   own earnings.
10. Owners want composing an admin to be unconstrained, so any function the
    business needs can be expressed; compliance and finance observe that an
    unconstrained composition can also produce an admin who can do harm — one
    holding both payment approval and payment recording, for instance.
11. Workers promoted to administrative roles want to keep working and earning;
    HR observes that a person administering the workforce they belong to may be
    asked to act on their own record.
12. Project admins want workers staffed onto batches immediately; compliance
    requires a signed NDA before any access to client material.
13. Owners want prospective workers to register freely and become findable; HR
    must prevent one person holding several accounts, which duplicate identity
    and corrupt payment.
14. Workers want their earnings to rise as they work, so they can judge whether
    a batch is worth their time; finance wants a figure shown to a worker to
    mean money the company owes, and a claimed task is not finished work.
15. Workers want to state the time a task took them; finance observes that the
    same statement determines what they are paid.
16. Workers want to see the terms they are working under — their rate, their
    bonus, what the batch has earned them; but rates are settled per assignment,
    so showing a worker their own terms in a shared workspace risks revealing
    what their colleagues earn for the same work.

### Negotiations

1. **Oversight vs. worker privacy.** _Resolved by scoping access to function_
   (owner, HR, workers). Admins had assumed oversight required breadth; examining
   the actual work showed it required _depth in one area_ instead — an HR admin
   needs a worker's whole employment history and never their bank details, and
   finance the reverse. Each therefore reaches everything its function needs,
   which is more than either had before, while the data outside that function
   becomes unreachable. Admins gain complete sight of their own domain, workers
   gain that no single person holds their whole life; the gravest fields are
   encrypted so that even legitimate access does not expose them, and every
   disclosure of a stored secret **must be recorded against the person who
   obtained it**, which protects the admin who did nothing wrong as much as the
   worker.

2. **Immutable payments vs. correctable details.** _Resolved by snapshot_
   (finance, workers). A payment record captures the recipient's banking details
   as they stood at that moment. Workers may change them freely thereafter; what
   was paid is never rewritten.

3. **Unalterable agreements vs. correcting mistakes.** _Resolved by append-only
   record_ (compliance, legal counsel, operations). Signatures are bound to the
   exact text signed and the history is never rewritten. A mistake is corrected
   by issuing a new version.

4. **Flexible status vs. reasonable states.** _Resolved by decoupling_ (project
   admins, developers). Task status is free text carrying no system behaviour;
   claimability follows from whether work is already assigned, never from its
   label.

5. **Bulk scale vs. safety.** _Resolved by two-phase commit_ (operations,
   developers). Bulk changes validate and preview before committing, with
   identical checking in both phases. Unconditional wipes are single-phase,
   having nothing to preview.

6. **Batch authority vs. payroll responsibility.** _Resolved by separating what
   coordination needs from what payroll needs_ (owner, finance, batch managers).
   Batch managers asked for pay visibility in order to plan and to judge whether
   their team was fairly treated; on examination the planning need was met by
   availability, progress and time — none of which is pay. Finance keeps payroll
   whole and undivided, batch managers get everything coordination actually
   requires without inheriting responsibility for pay disputes they cannot
   settle, and workers' pay stays between them and finance. Each is relieved of
   something they did not want along with what they asked for.

7. **Explaining refusal vs. disclosing the system.** _Resolved by removing the
   occasion for refusal_ (owner, developers, workers). Workers wanted to
   understand refusals; security wanted refusals not to advertise what exists.
   Both were satisfied by ensuring a worker is never refused at all: what they
   may do is present, what they may not is absent, so there is no denial to
   explain and nothing for a prospective attacker to enumerate. Workers get an
   interface containing only what is theirs to use — simpler than one full of
   things they cannot touch — and security gets a surface that reveals nothing.
   The safeguard workers required in exchange is that their own capabilities are
   never hidden from them.

8. **Organizational change vs. software change.** _Resolved by making roles data_
   (owner, developers). An admin is a named set of permissions held as a record,
   composed at runtime. The four functions operating today are examples of the
   mechanism, not a fixed list.

9. **Promotion vs. separation of duties.** _Resolved by making the act
   unprofitable rather than forbidding the person_ (owner, finance). Finance's
   concern was never who held which title but whether anyone could quietly move
   money toward themselves. That concern is met without restricting promotion at
   all: earnings follow one calculation nobody can vary by hand, payment records
   cannot be altered once written, and every role grant names who made it. There
   is nothing for a self-interested admin to adjust. Owners therefore promote the
   people who understand the work best, finance gets stronger assurance than a
   rule against promotion would have given — such a rule would have stopped an
   honest person while leaving the ledger editable — and promoted workers keep
   the history they earned. Whether particular combinations warrant a further
   check is carried to elicitation.

10. **Free composition vs. dangerous combinations.** _Resolved by placing the
    limits on acts rather than on combinations_ (owner, finance, compliance).
    Finance and compliance wanted certain pairings forbidden; owners wanted
    freedom to organize. Both are served by putting the boundary somewhere else
    entirely: some acts are impossible for _anyone_, whatever they hold —
    altering a recorded payment, rewriting a signed agreement, reading an
    encrypted value outside a recorded path. Owners compose freely, because
    composition distributes authority and cannot manufacture it; finance and
    compliance get protection that no permission set can defeat, which a list of
    banned pairings never could, since the dangerous pairing nobody anticipated
    would not be on it.

11. **Administering the workforce one belongs to.** _Resolved by attribution
    rather than prohibition_ (HR, owner, workers). Barring action on one's own
    record would also bar correcting one's own details. Instead every
    consequential action names its actor, so such action is visible rather than
    hidden.

12. **Staffing speed vs. confidentiality.** _Resolved by separating placement
    from access_ (project admins, compliance, clients). The two turned out not to
    compete once distinguished: what project admins needed was to _plan_ a team,
    what compliance needed was to govern _access to client material_, and these
    are different acts. A worker may therefore be placed on a batch immediately,
    so planning is never blocked — while **the system refuses to disclose
    credentials for client material to anyone without a completed agreement**.
    Admins staff at the speed they wanted, compliance gets a guarantee stronger
    than the procedure it replaced, and workers are never left holding access
    they had no standing to receive. Because clients are promised this in §1.2h,
    it is enforced by the system rather than entrusted to admins to remember.

13. **Open registration vs. one person, one account.** _Resolved by checking the
    person, not the gate_ (owner, HR). HR's concern was duplicate identities, not
    open registration as such; once that was clear, the check could move from the
    door to the details. Anyone may register, so owners lose no talent to a
    barrier, and duplicates are caught from the identifying details a worker
    supplies — a stricter test than restricting who may sign up, which would have
    turned away newcomers while missing the determined duplicator.

14. **Earnings as they accrue vs. earnings as they are owed.** _Resolved by
    naming three quantities instead of arguing over one_ (workers, finance). The
    parties were not disagreeing about a number but using one word for three
    different things. Distinguish them — what has been earned, what is
    outstanding, what has been paid — and both positions hold at once: workers
    watch value accrue from the moment a task is claimed, which is what lets them
    judge whether a batch is worth their time, and finance never has a figure
    shown to a worker mistaken for a debt the company has accepted. Delaying the
    figure would have served no one, since nothing in the system observes
    completion and it would have been withheld indefinitely.

15. **Self-reported time vs. the pay it determines.** _Resolved by matching the
    method to the work_ (workers, finance). The dispute assumed a single rule
    must cover everything. It need not: where a unit of work takes a predictable
    time, the role's expectation governs the payable figure and the worker's
    report does not enter into it — which finance prefers, and which workers
    prefer too, since a slow day costs them nothing and no one is scrutinised
    over an estimate. Direct measurement is reserved for work genuinely too
    variable for any expectation, where a fixed figure would cheat somebody every
    time. Each kind of work gets the method that suits it, and what is reported
    remains attributed and visible to those coordinating the batch, so an
    implausible pattern is apparent to a person even though it is invisible to
    the system.

16. **Knowing one's own terms vs. knowing everyone's.** _Resolved by scoping pay
    to the individual, not the batch_ (workers, finance, batch managers). A
    worker must be able to see the terms they are working under — nobody can
    judge whether work is worth taking without knowing what it pays, and terms
    concealed from the person bound by them are not terms but impositions. So a
    worker sees their own rate, their own bonus and their own earnings in the
    workspace for each batch, in full.

    What they never see is anyone else's. Because pay is settled per assignment,
    a colleague at the next desk on the same role may be on different terms, and
    that is between that colleague and the company. Every view a worker has of a
    batch is confined to their own assignment, so a shared workspace never
    becomes a shared payslip.

    Both sides gain by this. Workers get their terms stated plainly rather than
    inferred, and their own pay kept from the people they sit beside — the same
    protection, applied in both directions. Finance keeps the freedom to engage
    people individually, which a workspace exposing everyone's rate would have
    cost them: they would have had to pay everyone in a role identically, or
    accept the friction of workers comparing. It holds for batch managers too —
    coordinating a batch grants sight of availability and work, never of pay.

### Requirements withdrawn

No request was refused on the development team's authority. Each was met with the
same question — _what does this add to the business, or to the work of the person
asking?_ — and in every case below the requester, having thought it through,
withdrew or postponed it themselves. Several were dropped by the very stakeholder
who raised them, which is why they are recorded here rather than argued over
again later.

1. **Integration with the annotation documents** — reading task state directly
   from the spreadsheets and word-processor files. _Infeasible_ (developers,
   project admins): these are general-purpose documents with no notion of tasks
   or assignment. There is no system there to integrate with; work is reported,
   not observed.

2. **Automatic bank payment** — the system executing transfers directly.
   _Rejected_ (owner, finance): payouts remain a deliberate act performed through
   the bank. The system computes and records; it does not move money.

3. **Verification of submitted identity and banking data** — automated checking
   of national identity numbers and bank accounts. _Infeasible at inception_ (HR,
   developers): no available authority to check against. Recorded as an
   assumption rather than resolved as a feature.

4. **Quality review workflow** — a review and approval state machine over
   annotation output. _Out of scope_ (owner, project admins): quality is assured
   in the client's own process; adding it here would duplicate work done
   elsewhere.

5. **Skill proficiency levels and testing** — rating workers as beginner or
   expert. _Deferred_ (HR, project admins): no agreed basis for grading, and an
   ungrounded rating is worse than none. A skill is a claim, held or not held.

6. **Client accounts** — clients logging in to watch their projects. _Rejected_
   (owner): client identity is deliberately not modelled. Client relations are
   conducted outside the system.

7. **Third-party e-signature integration** — using an external signature service
   for NDAs. _Rejected_ (compliance, owner): per-signature cost and an outside
   dependency for a document the company must be able to produce itself.

8. **Per-worker timezone handling for availability** — declaring hours in local
   time. _Deferred_ (developers, batch managers): complicates aggregation for a
   gain that day-granularity planning does not require.

9. **Availability declared as times of day** — a worker stating they will work
   from 8 to 9 in the morning and again from 4 to 7 in the afternoon, rather than
   a total for the day. _Withdrawn by the owner_ (owner, batch managers,
   workers). Asked what would be done differently with the finer detail, batch
   managers could name nothing: work is claimed by workers when they choose, not
   scheduled into slots by anyone, so _which_ hours a person is free changes no
   decision the system supports — only _how many_ does. Workers then observed
   that entering several intervals a day, fourteen days ahead, is enough trouble
   that they would simply stop declaring. The owner concluded the proposal would
   buy precision nobody acts on at the price of the signal itself, and withdrew
   it. It becomes worth having only alongside shift scheduling, which is the
   thing that would consume it.

10. **A standalone NDA document editor** — the agreement editor as its own
    application, in which a compliance admin composes and controls documents
    independently of any template. _Withdrawn by the owner_ (owner, compliance,
    legal counsel). The value proposed was freedom: authoring without the
    constraints of a template. Asked what such a document would be _for_,
    compliance traced its life and found it had none — it would have no version,
    so nothing could be proved immutable; no recipient, so nothing bound anyone;
    no history, so nothing was provable afterwards. Legal counsel noted this
    describes precisely the word-processor documents the system was built to
    replace, and that a document outside the lifecycle is not an agreement but a
    draft with delusions. The owner accepted that the constraint _is_ the value
    and withdrew the request. Authoring stays within a template version, where
    publication makes it immutable and issuance binds it to a person.

11. **Notifications for everything** — alerting workers when a batch opens, a
    task is assigned, an agreement awaits signature, a payment is recorded, an
    appeal is decided; alerting admins when a worker signs, claims, or completes.
    _Reduced by its requesters to a single case_ (project admins, HR, workers,
    compliance). Everyone wanted this, and it went unquestioned until each was
    asked what they would do differently on receiving each alert. Project admins
    conceded they consult a batch when planning staffing, not when a message
    arrives, and would act no sooner for being told. Workers said an alert for
    every assigned task would, at the volume they work, become noise they would
    mute — at which point the one message that mattered is lost among those that
    did not. Compliance found the signature alert redundant: they review
    outstanding agreements deliberately, as a task, and a passing notification
    neither prompts that review nor replaces it.

    One case survived the question, and survived it decisively: **a suspended
    worker cannot be told they are suspended by a system they can no longer sign
    into.** Here a message is not a convenience but the only channel that exists,
    and without it a suspension is indistinguishable to the worker from the
    platform being broken. That case is built. The rest were withdrawn by the
    people who had asked for them, on the reasoning that a system notifying
    constantly teaches everyone to ignore it, and that the cost of having taught
    them falls due precisely when something matters.

### Conclusion

Not one of these conflicts was settled by ruling for one party. In every case the
stated positions turned out to be _proposed solutions_ rather than actual needs,
and separating the two revealed an arrangement serving both — usually one neither
side had asked for.

Three techniques did the work. Several conflicts dissolved once a thing was
**split in two**: placement from access, a payment record from the details it
snapshots, what coordination needs from what payroll needs. Each pair had been
treated as a single indivisible thing that two parties were fighting over;
separated, each party could have all of its own half. Others dissolved by
**moving the boundary elsewhere** — limits placed on acts rather than on people,
so that owners compose admins freely while finance gains protection no permission
set can defeat. The rest dissolved by **removing the occasion for conflict
altogether**: a worker never refused has no refusal to have explained, and no
enumerable surface is exposed.

Where a genuine residue of risk remained — a promoted worker administering their
own record — it was met by **attribution** rather than prohibition, since
forbidding the conduct would have barred the honest case along with the dishonest
one while leaving the underlying records just as editable. The system does not
decide who is trustworthy; it makes what anyone did impossible to conceal, which
protects the innocent party as much as it deters the other.

The withdrawn requirements are as instructive as the negotiated ones, and they
were settled the same way: by asking what a proposal would add to the business or
to the work of the person asking, and letting the answer decide. Some sought to
automate a process the system cannot observe. Others — richer availability, a
free-standing agreement editor, notifications for everything — were perfectly
buildable and were dropped anyway, by the people who had asked for them, once
they traced what would actually be done with the result and found the answer was
nothing, or worse than nothing.

Three of those came from the owner, which is the point. A specification in which
the person paying for the work receives everything they request is not a
specification but an order sheet. What makes these decisions durable is that
nobody was overruled: each requester reasoned their own way out, and a
requirement withdrawn by the person who wanted it does not return in six months
as a grievance.

## 1.4 Manual System / Existing System

The present process is conducted in shared documents and message threads. It is
described here as the baseline the proposed system must improve upon.

### Engaging a worker

1. A prospective worker is contacted, or applies informally.
2. Their qualifications are discussed by message; nothing is recorded in a
   consistent form.
3. Identity documents and banking details are collected by email or chat and
   stored wherever the recipient happens to keep them.
4. An NDA is drafted from a previous one, the name edited by hand, and sent for
   signature.
5. The signed copy is returned by email and filed. Whether the returned text
   matches what was sent is not checked.
6. The worker's capabilities are known only to whoever spoke to them.

### Structuring and staffing work

1. A client engagement arrives, usually as a set of spreadsheets.
2. Work is divided into batches by hand, and a sheet is prepared for each.
3. The roles the batch requires, and their pay rates, are agreed in conversation
   and written into the sheet — reconstructed from scratch for every batch.
4. Suitable workers are identified from memory.
5. Each is contacted individually to ask whether they are free. Staffing twenty
   people is twenty conversations, repeated whenever the roster changes.
6. Access to the annotation documents is granted by sharing a link or credentials
   through chat.

### Performing work

1. A worker opens the shared document and takes an unclaimed row.
2. Two people who both had the sheet open take the same row. A third row is
   overwritten without trace.
3. Any worker can read and alter any other worker's input.
4. Time spent is noted by the worker in a column, or reported later by message.
5. Progress is established by opening the sheet and reading it, or by asking.

### Paying for work

1. At the end of a cycle, rows are counted and time totalled by hand.
2. The applicable rule — measured time, expected handling time per item, hourly
   managerial rate, fixed salary — is applied per person, from memory.
3. Figures are assembled into a payroll sheet.
4. Payments are made through the bank and ticked off.
5. A worker disputing an amount cannot be shown evidence: the formula has since
   been edited, the source rows have moved, and the version that produced the
   payment no longer exists.

### Ending involvement

1. The worker stops being contacted.
2. Access to the annotation documents is not systematically withdrawn.
3. Credentials shared through chat remain valid indefinitely, and nobody can say
   with confidence who currently holds access to a given client's data.

```
   ┌──────────────┐
   │ Worker found │
   │ by memory /  │
   │  referral    │
   └──────┬───────┘
          │  details collected by chat and email
          ▼
   ┌──────────────┐      ┌────────────────────┐
   │ NDA edited   │─────▶│ Signed copy filed  │
   │ by hand,     │      │ in an inbox        │
   │ emailed      │      └────────────────────┘
   └──────┬───────┘
          │  access granted by sharing a link
          ▼
   ┌──────────────────────────────────────────────┐
   │            SHARED DOCUMENT                   │
   │  everyone can read and edit everything       │
   │  ┌────────────────────────────────────────┐  │
   │  │ row taken twice · row overwritten      │  │
   │  │ time noted by hand · no assignment      │  │
   │  └────────────────────────────────────────┘  │
   └──────┬───────────────────────────────────────┘
          │  totals counted manually at cycle end
          ▼
   ┌──────────────┐      ┌────────────────────┐
   │ Payroll      │─────▶│ Bank transfer,     │
   │ sheet, rules │      │ ticked off by hand │
   │ from memory  │      └─────────┬──────────┘
   └──────────────┘                │
                                   ▼
                        ┌────────────────────────┐
                        │ Dispute: no evidence.  │
                        │ Formula since edited,  │
                        │ source rows moved.     │
                        └────────────────────────┘
```

**Fig IF.2: Manual system workflow, with its characteristic failures**

### Where the manual system fails

| Failure                             | Consequence                                                                                                         |
| ----------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| Capability held in memory           | Cannot answer "who can do this work next week" without messaging people individually                                |
| Batch structure rebuilt by hand     | Rates mistyped, roles omitted, comparable batches organized differently for no recoverable reason                   |
| No assignment mechanism             | Work taken twice, work overwritten, identifiers duplicated — discovered at payment time, when hardest to adjudicate |
| Compensation rules applied manually | A wrong figure propagates into payroll unnoticed                                                                    |
| Payroll not reproducible            | Disputes answered by assertion rather than evidence                                                                 |
| Credentials shared through chat     | Access outlives engagement; no audit of who holds client data                                                       |
| NDAs edited by hand                 | No certainty which version was signed, whether text was altered, or whether a worker signed before receiving access |
| Delegation by file sharing          | All-or-nothing: no way to let someone manage NDAs without exposing what everyone earns                              |
| Documents open to all               | Any worker can alter any other's work, without trace                                                                |

### Conclusion

The manual system's failures share a cause: the place where work happens holds no
record of who did it, under what terms, or with what permission. Every fact the
business depends on is reconstructed after the event, from memory or from
documents that anyone may have altered. The proposed system does not attempt to
change where annotation happens; it establishes the record that annotation
cannot keep about itself.

## 1.5 Proposed System

The proposed system is a web application serving as the system of record for the
workforce, the work, the money, and the agreements. It is described here at the
level of capability; detailed behaviour is developed in elicitation and modelling.

### 1.5.1 Access and identity

Workers and admins alike sign in through an external identity provider; the system
holds no passwords. An account comes into existence when a person first signs in;
admins do not create accounts on others' behalf.

Every capability in the system is governed by a permission. Permissions are
grouped into named roles, and roles are records rather than code: an owner
composes an admin by selecting the permissions it should hold, names it, and
grants it — without a software change. The administrative functions in use today
are instances of this mechanism, not a fixed set.

Permissions distinguish acting on one's own data from acting on anyone's, so that
a single capability may be granted narrowly to a user and broadly to an admin.

Roles are granted to accounts additively. A user granted an administrative role
becomes an admin while remaining the same account: work history, earnings and
batch assignments are retained, and the grant may be revoked. An account may hold
administrative authority and batch work simultaneously. Every grant records who
made it and when.

Capabilities a person does not hold are absent from their interface rather than
refused. This extends to arrival: on signing in, a person is taken to the first
place their permissions allow them to work, so an admin holding only one function
never lands on a page they cannot use.

Suspension removes access without removing the account's roles, so restoring a
person does not require reconstructing what they held.

### 1.5.2 Workforce

A worker maintains a profile: personal details, education, languages, work
history, a CV, identity documentation, and the banking details by which they are
paid. The system prevents one person holding two accounts. National identity
numbers are encrypted; supporting documents are held as files reachable only
through the same access rules that govern the records referring to them.

Capability is expressed through a controlled catalogue of skills, maintained by
admins and grouped by the kind of annotation each serves, so that a skill means
the same thing across the workforce and can be searched on. Workers select their
skills from the catalogue; free description is not permitted, since a skill
nobody else uses cannot be matched against work. Admins order the catalogue so
that what matters most appears first, and may correct a worker's skills alongside
them. A skill still claimed by someone, or still required by a project, cannot
simply disappear beneath them.

Projects declare the skills they require, and it is this shared vocabulary — the
same terms on both sides — that lets work be matched to people at all.

Workers declare, for days ahead, how many hours they expect to be available — a
count for the day, not the times within it, since staffing decisions turn on
capacity rather than on when a worker sits down. Declaring none is a statement in
itself and is preserved as such, distinct from having said nothing at all.
Declarations are a planning signal, aggregated for those staffing the work, and
discarded once past.

Supporting documents — a CV, proof of identity, a photograph — are supplied as
files, and each kind admits only the formats appropriate to it, within a size the
system enforces rather than merely advises. A file that fails these checks is
refused at the point of upload, with the reason given, so that a worker learns
immediately rather than discovering later that their application was incomplete.
Where a worker supplies their own photograph it stands in place of any the
identity provider offers.

Admins may view and correct a worker's details on their behalf — for someone
struggling with the form, or a correction agreed by conversation — under the same
protections that govern the worker's own access, and every such change names the
admin who made it.

A worker's access may be suspended without destroying their record: their
assignments, work and earnings survive intact, because suspending someone must
never disturb what they are owed. Suspension creates, at that moment, the means
for them to contest it, so a suspended worker is never left without recourse.
They may state their case once, at reasonable length, and an admin approves or
rejects it; approval restores their access.

The route by which they contest it never discloses whether a given address
belongs to an account, is suspended, or has an appeal outstanding — every enquiry
answers alike. A mechanism for the wrongly suspended must not double as a means
of discovering who works here.

Removal takes one of two forms, and which applies is determined by the system
rather than chosen. A worker who has done work, been paid, or signed an agreement
is removed by withdrawing their access while their record is preserved intact —
the business is obliged to keep it, and the people they worked alongside depend
on it. Only an account carrying no such history may be erased outright, taking
its uploaded documents with it. The system refuses erasure whenever any
consequential record exists, so preservation is never a matter of an
administrator remembering to choose it.

### 1.5.3 Projects, batches and roles

A client engagement is represented as a project, divided into batches of work.
Each batch defines the roles it requires — _Annotator_, _QA_, _QA2_, or whatever
the work demands — and each role carries its own time expectations, which govern
how effort is turned into payable time (§1.5.4). A role is either operational,
performing annotation, or managerial, coordinating the batch. What a person is
paid is settled on their assignment rather than the role, so two workers holding
the same role may be engaged on different terms.

A worker is assigned to a role within a batch, receiving an identifier unique
within that batch. The same person may hold different roles in different batches
at once, at different rates.

Teams are assembled individually or in bulk from lists of addresses. Project and
batch status follows from their dates without manual upkeep.

Where a batch requires access to external documents, the credentials are held
encrypted against the assignment and withdrawn in bulk when the engagement ends.
Disclosure is a deliberate act, never a passive read: it requires an explicit
request, is refused where the recipient has no completed confidentiality
agreement, and is recorded against the person who obtained it.

Workers browse the projects on offer and express interest in those that suit
them, withdrawing it if circumstances change. An expression of interest and an
application are the same act seen from two sides: what a worker records as
interest is what a staffing admin sees as a candidate, so nobody re-enters it and
no willing worker is overlooked.

### 1.5.4 Work and time

Work is recorded as tasks within a batch, each identified by a reference to the
item in the external document. Tasks are imported and exported in bulk, since
work arrives and returns as spreadsheets. A task carries a free-text status for
labelling, which governs no behaviour.

A worker claims a task, singly or in bulk, and the system guarantees that no unit
of work is held by two people — the guarantee holding even when two workers claim
the same task at the same instant, since it is the fact of assignment and not a
prior reading of it that decides. Claiming is confined to operational roles;
coordinating a batch is not doing its work. Workers may release work they cannot
complete, and comment on work they hold, so that context stays attached to the
task rather than dispersing into messages. Completed batches are closed: no
further claims, releases, or edits.

Where a worker claims many tasks at once and some cannot be granted, the rest
still are, and the worker is told exactly which failed and why. A single
contested item never costs them the others.

An admin may transfer a task from one worker to another without erasing that the
work was done, since credit for completed work is part of the payment record.

**How effort becomes payable time.** All durations are held in seconds —
everywhere, without exception — because a system that mixes minutes and hours
will eventually add them together, and the resulting error appears in someone's
pay. Presentation may show hours; the record never does.

Payable time is not the same as time elapsed. Each batch role declares how the
two relate, and there are two arrangements:

- **Expected handling time.** The role carries what a unit of work should take,
  and payable time follows from the work done rather than from any clock: the
  count of items handled, at the role's per-item expectation, plus a second
  measure for work whose units contain a varying number of sub-parts, at the
  role's per-part expectation. Two workers completing the same work are paid the
  same, and the slow day and the fast day are alike. This suits work whose
  effort is predictable from its quantity.
- **Direct measurement.** The role takes the time the worker reports for the
  task. This suits work that varies too much for any expectation to be fair.

The choice belongs to the role and therefore to the nature of the work, not to
the person doing it — so it cannot be adjusted for an individual, and a worker
cannot select the arrangement more favourable to them.

**How payable time becomes money.** The rate is held against the assignment
rather than the role, so two workers in the same role may be paid differently —
an experienced hand and a newcomer doing identical work under different terms —
without splitting the role in two.

Operational work is always paid for the time it accounts for, at that rate.
Managerial work is paid the same way when the assignment carries an hourly rate,
and otherwise by a fixed sum for the engagement — recognising that coordination
is sometimes engaged by the hour and sometimes for the job. A managerial worker
records their coordination time separately from any task.

A bonus may be added to an assignment and counts from the moment it is awarded,
not at some later reckoning. The total for an assignment is its earnings plus
that bonus, rounded once, at the end — never at intermediate steps, since
repeated rounding produces figures that do not reconcile.

Every one of these quantities is computed in exactly one place in the system. A
worker's view, an admin's view, the payroll listing and the export all call the
same calculation, so they cannot disagree; and changing how pay is computed is a
single change that moves every figure together, which is the intent.

**The worker's workspace.** A worker does all of this in one place, per batch:
the batch they are assigned to is where they see the work available to them,
claim it, state the time it required, comment on it, release it, and see what
that batch has earned them. Everything bearing on one engagement is together,
because a worker deciding whether to take another task is weighing exactly these
things at once — what is left, what they have done, what it has been worth.

The workspace shows the worker the terms they are working under — the rate on
their assignment, any bonus awarded, and what the batch has earned them so far —
because someone deciding whether to claim another task is entitled to know what
it pays. It shows them nothing of anyone else's terms: every view is confined to
their own assignment, so people working side by side on the same batch do not
learn what each other earn.

A worker assigned to several batches, or holding two roles in one batch, keeps
these workspaces separate, since the rate, the role, and the work all differ
between them. Their claimed work across every batch is also gathered in one
listing, for the worker who wants to know what they owe the world rather than
what one engagement owes them.

### 1.5.5 Money

Earnings follow from recorded work through one authoritative calculation, so that
any two parties computing a figure arrive at the same answer. How payable time is
established, and how it becomes money, is set out in §1.5.4.

**When earnings appear.** A worker's earnings rise as they work, not when
someone declares the work finished. The moment a task is claimed it carries an
expected value — from the rate on their assignment and either the time they
state or the time their role expects for such an item — and that value is
visible to them immediately. Nothing is withheld pending an approval step, because there is no
approval step: the system cannot observe the annotation, so there is nothing for
it to approve.

What a worker sees is therefore a **projection**, and must be presented as one.
It is the value of work in hand, which is not the same as money owed and still
further from money paid. The system distinguishes all three plainly — earned so
far, outstanding, and paid — so that a rising figure is never mistaken for a
payment due.

The system answers two distinct questions about money owed, and does not conflate
them: **who is still owed something** — earnings computed, less payments already
recorded — and **who can actually be paid**, which additionally requires the
banking details a transfer needs. A worker accrues earnings whether or not they
have supplied those details, so the two lists differ, and the difference is
itself useful: it identifies the people whose payment is blocked on information
only they can provide. The same calculation serves the workforce view and each
worker's own view, so a worker and an admin never see different figures.

A payment record captures the amount, the recipient, and the recipient's banking
details as they stood at that moment. It is never altered afterwards. It may be
archived — removing it from what is outstanding without destroying it — and
restored if archived in error; only an archived record may be deleted.
Corrections are made by further records, never by revision. Financial data is
exported for reconciliation against bank statements, in a form accounting's own
spreadsheets can consume.

Payment itself is executed through the bank, outside the system. The system
computes and records; it does not move money.

### 1.5.6 Agreements

Compliance admins author NDA templates in the system, using placeholders for
per-recipient details that resolve when the agreement is issued. A template is
published as a version, and a published version cannot be altered.

An agreement is issued to a worker, who views and signs it; the company
countersigns to complete it. At most one agreement exists for a given version and
recipient, so a person cannot be bound twice by the same text.

Its state — issued, viewed, signed, completed, rejected, expired, voided — is
visible throughout, and agreements past their date expire without intervention.
The states are not all final: a voided agreement may be reinstated and an expired
one extended, while rejection ends that agreement for good. Every change of state
is recorded permanently, together with who caused it, and cannot be rewritten.
The record preserves the order of events, so that a signature can be shown to
have followed a reading of the text rather than preceded it.

Each signature is bound by a cryptographic digest to the exact text signed, so
that any later alteration is detectable, and the digest covers the text as the
signer saw it — with every per-recipient detail already resolved. The agreement
is rendered as a PDF identical for both parties, reproducing names in the
signer's own script, and remains reproducible unchanged for as long as the record
is kept.

### 1.5.7 Seeing the whole

Both populations need a view of their situation that does not require assembling
it from parts.

**Administrators** are shown the state of the business on arrival: how large the
workforce is and how it divides by standing, how many have completed their
profiles, how many projects and batches are running, how much work the catalogue
of skills covers, how many agreements exist, and what has been paid in total.
Because a number alone does not say whether things are improving, these are
presented against recent history — the present period beside the one before it —
so that a change in direction is visible without anyone running a report.

**Workers** are shown their own equivalent: the batches they are currently on,
what they have completed and how they are performing, their standing agreements,
and their earnings. A worker's history of work is retained and can be reviewed —
which tasks, in which batch, in what role, and when — as is their participation
across the projects they have joined. These are the records a worker draws on
when questioning a payment, so they must remain available after the work ends.

The two views are computed from the same records: what a worker sees of their own
earnings and what an administrator sees of that worker's earnings are never
separately derived, and therefore never disagree.

### 1.5.8 Working at scale

Every list is paginated, and no list may be persuaded to return everything at
once. Every substantial data set can be exported, and exports are shaped so that
what is exported can be imported again — the same columns on both sides, so that
the ordinary way to change a hundred records is to export, edit, and import.

Bulk changes are validated and previewed before they are committed, with
identical checking in both phases, so operations can work at the scale of a
spreadsheet against a system that will not silently accept a mistake. What the
preview shows is advisory; the commit re-establishes every fact for itself, since
the file may have changed in between and the system trusts only what it has
checked at the moment of writing.

A bad row never costs the good ones: rows are accepted or rejected individually
and the outcome reported per row, because an import of a thousand tasks that
fails entirely on one malformed line is useless to the person who prepared it.
People are matched on the identifier the system issued them rather than on their
name, names being neither unique nor stable.

Exporting is itself a privileged capability, since it places data beyond the
system's protection, and no export may reveal more than the person could already
see.

### 1.5.9 The workflow proposed

The diagram below traces one worker from arrival to payment, and should be read
against Fig IF.2, which traces the same journey as it happens today. Every point
at which the manual process loses a fact, the proposed system records one.

```
   ┌──────────────┐
   │   REGISTER   │  self-service; no introduction needed
   └──────┬───────┘
          ▼
   ┌──────────────────────────────┐
   │  PROFILE + SKILLS + BANKING  │  duplicate identity refused here
   └──────┬───────────────────────┘
          ▼
   ┌──────────────┐        ┌──────────────────────────┐
   │  DECLARE     │───────▶│  visible to those doing   │
   │ AVAILABILITY │        │  the staffing            │
   └──────┬───────┘        └──────────────────────────┘
          ▼
   ┌──────────────────┐
   │ EXPRESS INTEREST │◀────── admin sees this as a candidate
   └──────┬───────────┘
          ▼
   ┌──────────────────────────┐
   │  ASSIGNED TO BATCH ROLE  │  unique work ID · rate set on assignment
   └──────┬───────────────────┘
          │
          ▼
   ┌──────────────┐   not signed    ┌───────────────────────┐
   │  AGREEMENT   │────────────────▶│  NO CREDENTIALS       │
   │   ISSUED     │                 │  (staffing may run    │
   └──────┬───────┘                 │   ahead; access may   │
          │ signed + countersigned  │   not)                │
          ▼                         └───────────────────────┘
   ┌──────────────────────┐
   │ CREDENTIALS RELEASED │  encrypted · disclosure recorded
   └──────┬───────────────┘
          ▼
   ╔══════════════════════════════════════════════════╗
   ║           BATCH WORKSPACE                        ║
   ║   claim ─▶ work ─▶ record time ─▶ comment        ║
   ║      ▲                    │                      ║
   ║      └──── release ───────┘                      ║
   ║   no task ever held twice · own terms visible    ║
   ╚═════════════════════┬════════════════════════════╝
                         ▼
   ┌────────────────────────────────────┐
   │  EARNINGS ACCRUE AS WORK IS CLAIMED │  one calculation, shared
   └──────┬─────────────────────────────┘
          ▼
   ┌──────────────────┐     ┌─────────────────────────────┐
   │ PAYMENT RECORDED │────▶│ banking details snapshotted; │
   │  (bank pays      │     │ never rewritten afterwards   │
   │   outside)       │     └─────────────────────────────┘
   └──────┬───────────┘
          ▼
   ┌────────────────────────────────────────────┐
   │  RECORD RETAINED — through suspension,     │
   │  departure, and any later dispute          │
   └────────────────────────────────────────────┘
```

**Fig IF.3: Proposed system workflow — a worker from arrival to payment**

### 1.5.10 What each actor meets

The capabilities above are organized by what the system does. The tree below
organizes the same capabilities by who holds them, so that each viewpoint in §1.2
can be checked against what it was granted — and so that what an actor is *not*
granted is equally visible.

```
OWNER ─────────── everything below, plus:
                  ├── compose an admin from permissions, and name it
                  ├── grant and revoke administrative roles
                  └── see the whole business against recent history

PROJECT ADMIN ─── ├── projects, batches, and the roles each batch needs
                  ├── staff a batch — individually or in bulk
                  ├── set what each person is paid on their assignment
                  ├── import and export work
                  ├── issue and withdraw credentials
                  └── track progress
                      ✗ not: payroll, agreements, workforce standing

FINANCE ADMIN ─── ├── earnings, and who is owed what
                  ├── who can actually be paid
                  ├── record, archive, restore payments
                  └── export for reconciliation
                      ✗ not: identity documents, agreements, staffing

COMPLIANCE ────── ├── author and publish template versions
   ADMIN          ├── issue, countersign, expire, extend, void
                  ├── the full unalterable history
                  └── produce the legal document
                      ✗ not: earnings, banking details, staffing

HR ADMIN ──────── ├── the workforce, and everything about one worker
                  ├── correct details on a worker's behalf
                  ├── the skills catalogue
                  ├── suspend, hear appeals, restore
                  └── export workforce data
                      ✗ not: banking details, payroll, agreements

WORKER ────────── ├── own profile, skills, documents, banking details
                  ├── declare availability
                  ├── browse projects, express interest
                  ├── BATCH WORKSPACE — claim, time, comment, release
                  ├── own terms, own earnings, own payment history
                  ├── own credentials
                  ├── sign agreements, and read what was signed
                  └── contest a suspension
                      ✗ not: any other worker, anything not assigned

BATCH MANAGER ─── everything a worker has, plus, within one batch:
  (a worker)      ├── the team's declared availability
                  ├── the batch's progress
                  └── log coordination time
                      ✗ not: what any of them are paid
```

**Fig IF.4: Capabilities by actor, and the boundaries between them**

Two things are meant to be read off this figure. The first is that no actor's
authority is a superset of another's except the owner's — a finance admin is not
a lesser project admin, but a different one, and neither can do the other's work.
The second is that the exclusions are as deliberate as the grants: every ✗ above
was settled in a §1.3 negotiation, not left to whoever implements the screen.

### 1.5.11 What the system does not do

It does not host annotation content, integrate with the documents where
annotation happens, model clients, execute payments, verify submitted identity or
banking data, assess annotation quality, grade skills, or replace the judgement
of the people using it.

### Conclusion

The proposed system establishes, for each thing the manual process could not
hold, a record that is authoritative: who may act, who was assigned what, how
long it took, what is owed, what was paid, and what was agreed. It leaves the
annotation itself where it is.

---

# 2. Constraints and Assumptions

### Constraints

1. Sign-in is through a single external identity provider; the system holds no
   passwords, and adding a second provider is a business decision.
2. Accounts originate only from a person signing in.
3. All durations are recorded in seconds, everywhere, without exception. Other
   units may be displayed but never stored.
4. Payment arithmetic has exactly one authoritative implementation, called by
   every view that shows money. Rounding happens once, at the final total.
5. Personal, identity and financial data is collected only because engaging and
   paying the workforce requires it, and is used for no other purpose.
6. National identity numbers and stored access credentials are encrypted at rest
   and disclosed only through a recorded path.
7. Export is a privileged capability, since exported data leaves the system's
   protection.
8. Scheduled operations are invoked by an external scheduler, whose interface is
   a contract.
9. Exported data formats are a contract with the spreadsheets that consume them.

### Assumptions

1. Workers hold accounts with the identity provider.
2. Workers enter their own banking details accurately; there is no verification
   step, so this data is trusted as entered and is money-critical.
3. Work completion and time spent are self-reported and cannot be verified by the
   system, since annotation happens where the system cannot observe. This is a
   property of the working arrangement, not a defect to be engineered away — and
   it is why the system emphasizes an auditable record of what was claimed, by
   whom, and when.
4. Annotation quality is assured outside this system.
5. Declared availability is a planning signal; declaring none does not prevent a
   worker from claiming work.
6. Owners are trusted; the system does not constrain them.

---

# 3. Open Questions Carried Into Elicitation

1. What precisely may each admin do? §1.1 names four as examples; the exact
   permission set behind each is elicitation work, as is the full catalogue of
   permissions from which any admin may be composed.
2. May one person hold both an administrative role and a batch role — a finance
   admin who is also a batch manager on a project they are paid for — and do the
   two authorities interact? §1.3 permits it; elicitation must decide whether any
   specific combination should nonetheless be refused.
3. Should composing an admin warn about dangerous permission combinations, and if
   so which? Composition is left unconstrained at inception.
4. Who may promote whom? Owners clearly may; whether an HR admin may grant
   administrative roles, and which ones, is undecided.
5. Should a change to banking details require administrative approval, given that
   it drives payment and is not otherwise verified?
6. Does any admin require visibility limited to a subset of projects, rather than
   the whole platform?
7. Which data-protection regime governs the workforce, and what does it oblige —
   retention limits, a right to erasure, breach notification?
8. How long must personal and financial data be retained after a worker leaves?
   §1.5.2 establishes that an account with any consequential history is preserved
   rather than erased, but not for how long, nor whether a worker may compel
   erasure of the personal data within a preserved record.

---

# 4. Inception Exit Criteria

| Criterion                                                  | Status          |
| ---------------------------------------------------------- | --------------- |
| Problem understood and stated                              | ✅              |
| Stakeholders identified — direct, indirect, external       | ✅              |
| Viewpoints gathered per stakeholder group                  | ✅              |
| Conflicts surfaced, negotiated, and outcomes recorded      | ✅ 16 conflicts |
| Requirements withdrawn, with the reasoning recorded        | ✅ 11 withdrawn |
| Account model and route to advancement established         | ✅              |
| Existing manual system described and its failures analysed | ✅              |
| Proposed system described at capability level              | ✅              |
| Constraints and assumptions captured                       | ✅              |
| Open questions logged for the next task                    | ✅              |

**Next task:** [Quality Function Deployment](02-qfd.md) — the requirements
gathered above sorted by the kind of satisfaction each produces, so that what is
merely expected is not confused with what was asked for. The open questions in §3
are carried forward and resolved as the specification develops.
