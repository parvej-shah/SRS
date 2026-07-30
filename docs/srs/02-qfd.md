# Software Requirements Specification

## GenMorphics AI — Annotation Workforce Management System

**Phase 2: Quality Function Deployment (QFD)**

**Method:** Pressman, _Software Engineering: A Practitioner's Approach_ —
Requirements Engineering. Continues from [Phase 1: Inception](01-inception.md).
See the [table of contents](README.md) for the full specification.

---

# 2. Quality Function Deployment (QFD)

Quality Function Deployment is a technique for listening closely to the voice of
the customer and translating what is heard into the technical requirements a
system must satisfy. Developed in Japan in the late 1960s as a form of
cause-and-effect analysis and brought to the United States in the early 1980s,
QFD was described by its originator, Yoji Akao, as a "method to transform
qualitative user demands into quantitative parameters, to deploy the functions
forming quality, and to deploy methods for achieving the design quality into
subsystems and component parts."

Its value at this stage of requirements engineering is that it sorts what
stakeholders want by the *kind* of satisfaction each requirement produces. Some
requirements please in proportion to how well they are met. Some are noticed only
when missing. Some are never asked for at all and delight when found. The three
behave differently enough that treating them alike is a mistake: normal
requirements can be traded against one another, expected requirements cannot be
traded at all, and an exciting requirement should never be bought at the cost of
either.

The distinction also predicts how satisfaction decays. A normal requirement met
halfway satisfies halfway. An expected requirement met halfway satisfies nobody —
a payroll calculation correct most of the time would not be partly trusted, it
would be distrusted entirely. And an exciting requirement will not stay exciting:
a worker who has watched their earnings accrue for a year will no longer find it
remarkable, but would be dismayed to lose it. Today's delighter becomes
tomorrow's expectation, which is why §2.3 should be read as a moving boundary
rather than a settled classification.

The stakeholders identified in §1.1 — the owner, admins specialized by function,
workers, and batch managers — were consulted as described in §1.2. Their stated
requirements are deployed below into the three categories. Every normal
requirement traces to a viewpoint recorded in §1.2; nothing appears there that no
stakeholder asked for.

---

## 2.1 Normal Requirements

These are the requirements stakeholders state explicitly, as their goals and
objectives for the software. They are the requirements a stakeholder would list
if asked what the system should do. Satisfaction rises in proportion to how
completely they are met, and falls in proportion to how many are missing.

### Owner Requirements

1. Sign-in for every participant through a single trusted identity provider.
2. Compose a new kind of admin by selecting the permissions it holds, and name
   it, without any change to the software.
3. Promote a proven worker to an administrative role, and demote them again,
   without a second account and without losing their history.
4. Grant each admin only their own area of responsibility.
5. See the state of every project and batch without asking anyone.
6. A single place where prospective workers register and present their
   qualifications.
7. Find workers by recorded capability and availability rather than from memory.
8. Assurance that every worker has signed an agreement before receiving access to
   client material.
9. Withdraw a person's access completely and immediately when they leave.
10. Retain the work and payment record after a worker departs.
11. See the size and standing of the workforce, the volume of work, and the money
    paid, against recent history.

### Project Admin Requirements

1. Create projects and divide them into batches of work.
2. Define the roles each batch requires, with their own time expectations.
3. Set what each person is paid on their own assignment.
4. Find workers by capability, and see declared availability before committing to
   a delivery date.
5. Staff a batch individually or in bulk from a list of addresses.
6. Issue each worker an identifier unique within the batch.
7. Import work in bulk and export it in the same shape.
8. Track progress of a batch without interrupting the people doing the work.
9. Issue and withdraw credentials for the documents holding client work.
10. Have project and batch status follow from their dates without manual upkeep.
11. See workers who have expressed interest in a project as candidates for it.
12. Transfer a task from one worker to another without erasing that it was done.

### Finance Admin Requirements

1. Earnings computed from recorded work rather than calculated by hand.
2. One authoritative calculation, so that two people computing a figure agree.
3. Support for differing compensation: expected handling time, direct
   measurement, hourly managerial rate, fixed salary.
4. Award a bonus on an assignment and have it counted.
5. See who is still owed money, and separately who can actually be paid.
6. Record each payment against the person and the work it settles.
7. A payment record that cannot be altered after the fact.
8. Archive a payment record, restore one archived in error, and delete only
   archived records.
9. Export financial data in a form accounting's own spreadsheets consume.

### Compliance Admin Requirements

1. Author agreement templates within the system.
2. Insert per-recipient details automatically rather than editing by hand.
3. Publish a version and know it cannot afterwards be altered.
4. Issue an agreement and see whether it has been viewed, signed, or ignored.
5. Countersign on behalf of the company.
6. Expire, extend, void, reinstate, or reject an agreement as circumstances
   require.
7. A complete history of every state change that cannot be rewritten.
8. Produce a document suitable for legal use.

### HR Admin Requirements

1. Collect the identity, qualification and banking data needed to engage and pay
   a worker.
2. Prevent one person holding two accounts.
3. Maintain a controlled catalogue of skills, ordered as the business requires.
4. See everything about one worker in one place.
5. Correct a worker's details on their behalf.
6. Suspend a worker's access without destroying their record.
7. Give a suspended worker a route to contest the decision, and restore access
   when it succeeds.
8. Export workforce data for reporting.

### Worker Requirements

1. See work suited to their skills, and express interest in projects.
2. Declare availability in advance.
3. Claim work, singly or in bulk, with certainty that nobody else holds it.
4. Release work they cannot complete, without penalty.
5. Record the time a task required.
6. Comment on work so that context stays with the task.
7. Do all of this in one place per batch, rather than across scattered screens.
8. Know the terms they are working under before deciding to take more work.
9. See their own earnings, per batch and in total.
10. See what has actually been paid, and how it differs from what they earned.
11. Review their own history of work and participation.
12. Correct their own personal and banking details.
13. Supply their own profile photograph.
14. Reach their own credentials for the documents they work in.
15. Sign agreements issued to them, and read what they signed afterwards.
16. Contest a suspension.
17. Register and be considered without knowing someone already inside.

### Batch Manager Requirements

1. See the availability declared by the batch's members.
2. See progress on the batch they coordinate.
3. Record their own coordination time and be paid for it.
4. Reach the work of the batch they manage, and nothing beyond it.

---

## 2.2 Expected Requirements

These requirements are so fundamental that stakeholders do not think to state
them. Nobody asks for them, and meeting them earns no praise — but their absence
produces immediate and serious dissatisfaction, and in several cases below would
make the system unusable or unsafe however well everything else worked.

They are recorded because unstated requirements are the ones most easily lost. A
requirement nobody wrote down is a requirement nobody planned for, estimated, or
tested; and unlike a missing feature, a violated expectation is rarely reported
as a defect — it is experienced as the system being untrustworthy, which is a far
harder judgement to reverse. Several below were discovered only by asking what
would have to be true for a worker to accept a payment figure without checking
it, or for an admin to act on a list without verifying it elsewhere.

**Confidentiality**

1. Personal, identity and financial data reaches only those whose work requires
   it.
2. A worker sees their own data and never another person's.
3. What one worker earns is not visible to the workers beside them.
4. Nothing in the system reveals whether a given person holds an account.
5. The system resists the ordinary categories of attack against web
   applications.

**Correctness**

6. Money owed is calculated correctly, every time, without arithmetic error.
7. A figure shown to a worker and the same figure shown to an admin agree.
8. Data entered is not silently lost, corrupted, or overwritten by someone else.
9. A partially completed operation does not leave records inconsistent.
10. Two people acting at the same moment do not produce a result neither
    intended.
11. Records the business is obliged to keep survive any ordinary administrative
    action.

**Availability and performance**

12. Signing in is reliable, and a session behaves consistently.
13. The system remains available during the hours the workforce works, across
    the time zones it spans.
14. Pages load quickly enough that working through a batch is not a chore.
15. Lists remain usable as data grows, rather than degrading with volume.

**Comprehensibility**

16. An action that fails says so, and says why, rather than failing quietly.
17. Dates, times and money display unambiguously.
18. Names in every script the workforce uses are stored, searched and rendered
    correctly — including in generated documents.
19. The interface is usable on both desktop and mobile, since much of the
    workforce has no other machine.

---

## 2.3 Exciting Requirements

These requirements go beyond what stakeholders expressed or expected. They
produce satisfaction out of proportion to the asking — because nobody asked.

Almost none of them was conceived as a feature. Each is noted below with the
negotiation in §1.3 that produced it, because the pattern is worth seeing: they
are consequences of resolving conflicts properly rather than things anyone set
out to build.

### Required of this system

1. **A promotion that costs nothing.** A worker given administrative
   responsibility keeps every task, every assignment and every taka they have
   earned, and may go on working while they administer. Advancement in most
   systems means a second account and an abandoned history.
   _(from §1.3 negotiation 9 — promotion vs. separation of duties)_
2. **Signatures that prove their own text.** Each signature carries a
   cryptographic digest of the exact document the signer saw, so a later
   alteration is not merely against the rules but detectable. What is usually a
   matter of trusting the archive becomes a matter of arithmetic.
   _(from negotiation 3 — unalterable agreements vs. correcting mistakes)_
3. **Preview before commit.** A bulk change of a thousand records is validated
   and shown in full before anything is written, and a single bad row costs only
   itself. Spreadsheet work stops being irreversible.
   _(from negotiation 5 — bulk scale vs. safety)_
4. **Exports that import.** What comes out of the system goes back into it
   unchanged in shape, so the natural way to edit a hundred records is to export,
   edit, and import — no re-keying, no bespoke template.
   _(from negotiation 5, and from the ops need in §1.4)_
5. **Earnings that rise as work is done.** A worker watches value accrue from the
   moment they claim a task, rather than waiting for a cycle to close, and can
   judge mid-batch whether the work is worth their time.
   _(from negotiation 14 — earnings as they accrue vs. as they are owed)_
6. **An interface containing only what is yours.** Rather than showing a worker
   doors they cannot open, the system shows only theirs. What most systems
   present as a permission error is here simply absent.
   _(from negotiation 7 — explaining refusal vs. disclosing the system)_
7. **Payment history that cannot be quietly revised.** Every payment record
   preserves the banking details as they stood at that moment, so a worker
   changing their account tomorrow does not alter what was paid yesterday, and no
   correction is ever made by overwriting.
   _(from negotiation 2 — immutable payments vs. correctable details)_
8. **A role catalogue the business writes itself.** An owner needing a new kind
   of admin composes one from permissions and names it — no request to a
   developer, no release, no wait.
   _(from negotiation 8 — organizational change vs. software change)_
9. **Each kind of work paid the way that suits it.** A role whose items take a
   predictable time pays by that expectation, so a slow day costs a worker
   nothing and nobody is questioned over an estimate; work too variable for any
   expectation is measured directly instead. Most systems pick one rule and make
   everybody live with it.
   _(from negotiation 15 — self-reported time vs. the pay it determines)_

### Foreseen, but outside this specification

The following are natural extensions of what is specified above, and were raised
during inception. **They are not requirements of this system and nothing here
commits to building them.** They are recorded so that an idea consciously
deferred is not later mistaken for a requirement that was overlooked — and so
that when the business does come to want one, the reasoning that set it aside is
still on record.

10. **Matching workers to work automatically.** The system holds skills, declared
   availability and complete work history; proposing a staffing list for a new
   batch is a short step from there, and would remove the last manual step in
   staffing.
11. **Forecast earnings.** A worker who knows their rate, their claimed work and
    their declared availability could be shown what a fortnight is likely to pay
    them — turning a record of the past into a basis for planning.
12. **Noticing the implausible.** Reported time that departs sharply from what a
    role expects, or from a worker's own established pattern, could be surfaced
    for a person to look at — addressing the concern behind §1.3's negotiation on
    self-reported time without imposing a control the system cannot justify.
13. **Shift scheduling.** Should the business ever plan coverage across the hours
    of a day rather than the capacity of a day, the finer availability the owner
    asked for (§1.3, withdrawn requirement 9) becomes worth collecting, because
    something would finally consume it.

---

## Conclusion

Sorting the requirements this way shows where effort earns its return, and each
category earns it differently.

The **normal** requirements are what the system is *for*. They are visible in
every screen, they are what stakeholders will check first, and they are the only
category that can be traded — a project admin who must choose between bulk import
and progress tracking can make that choice, however unwelcome.

The **expected** requirements admit no such trade. They earn nothing when present
and cost everything when absent, and their cost is not proportional: a payroll
figure wrong once does not lose a fraction of a worker's trust, it loses the
figure's authority altogether. This is why they are enumerated despite nobody
having asked. An unstated requirement is one nobody plans for or tests, and a
violated expectation is rarely reported as a defect — it is experienced as the
system not being trustworthy, and that verdict is far harder to reverse than a
bug is to fix.

The **exciting** requirements are, almost without exception, consequences of
decisions taken in §1.3 for entirely other reasons. Promotion without loss came
from resolving a separation-of-duties conflict; preview-before-commit from making
bulk work safe; the absent-rather-than-refused interface from a disagreement
about how to explain a denial; paying each kind of work by the method that suits
it from a dispute about self-reported time. Not one was conceived as a feature.
Delight here was a by-product of resolving conflicts honestly — which suggests
that the way to produce more of it is not to brainstorm features but to keep
taking conflicts seriously enough to find the arrangement that serves both sides.

A closing caution. Because today's delighter becomes tomorrow's expectation, the
boundary between §2.2 and §2.3 will move. Each item required in §2.3 should be
expected, within a year of the system being in use, to have migrated into §2.2 in
the minds of the people using it — no longer remarked upon, merely assumed, and
sorely missed if withdrawn. This classification is a snapshot of expectation at
the time of writing, and should be revisited rather than relied upon
indefinitely.

**Next task:** [Usage Scenarios](03-usage-scenarios.md) — walking each stakeholder through the system as
they will actually meet it.
