# Software Requirements Specification

## GenMorphics AI — Annotation Workforce Management System

**Phase 3: Usage Scenarios**

**Method:** Pressman, _Software Engineering: A Practitioner's Approach_ —
Requirements Engineering. Continues from [Phase 1: Inception](01-inception.md)
and [Phase 2: QFD](02-qfd.md).

---

# 3. Usage Scenarios

The requirements of §1.5 describe what the system does. This section describes
what people do with it: each actor followed through the work they came to
perform, in the order they perform it.

Scenarios are written in the present tense, as the system will be met. Where a
step can fail, the failure is described alongside the success — a scenario that
only ever succeeds hides exactly the requirements that are most expensive to
discover late. Failure paths are marked **⚠**.

Nothing here specifies a screen. Scenarios say what a person does and what the
system does in return; how that is arranged visually is a design decision taken
later, against these scenarios rather than in place of them.

---

## 3.1 Bootstrapping the System

Every other scenario presumes someone is already there to grant access. The first
owner is therefore established when the system is installed, not created through
it — an account that could create the first administrator would be an account
that could create administrators.

From that point the system is self-sustaining: the owner composes the
administrative roles the business needs (§3.3), and everyone else arrives by
signing in for themselves (§3.2). No administrator ever creates a person's
account.

---

## 3.2 Worker Interaction

### Arriving

A prospective worker reaches the platform and signs in with their existing
identity-provider account. There is no registration form to complete first, no
password to invent, and no introduction required from someone already inside — an
account comes into being the moment they sign in.

They are shown what an account without work can do: complete a profile, declare
skills, and browse projects. Nothing yet suggests work they cannot take.

### Completing a profile

The worker supplies what the business needs in order to engage and pay them:
their name and contact details, education, languages, work history, and the
banking details they are to be paid into. They upload a CV, proof of identity,
and a photograph.

**⚠** A file in the wrong format, or larger than the system accepts, is refused
at the point of upload with the reason given, so the worker corrects it
immediately rather than discovering later that their application was incomplete.

**⚠** If the identifying details match an existing account, the system refuses
the profile and says so. One person holds one account; a second would divide
their work history and corrupt what they are paid.

The photograph they supply stands in place of whatever image their sign-in
account carries.

### Declaring capability and availability

The worker selects their skills from the catalogue the business maintains. They
cannot type a skill of their own devising — a capability no one else uses cannot
be matched to work, and the catalogue is what makes "who can do this?" a question
with an answer.

They then declare, for each of the coming days, how many hours they expect to be
free. Declaring none is itself a statement, and is recorded as one, distinct from
having said nothing. These declarations reach whoever is staffing work, and are
discarded once the day has passed.

### Finding work

The worker browses the projects on offer and expresses interest in those that
suit them. Their interest reaches the admins staffing that project as a
candidacy — the same record seen from the other side, so nothing is re-entered
and no willing worker is overlooked. They may withdraw interest if circumstances
change.

### Being engaged

An admin assigns the worker to a role in a batch — _Annotator_, _QA_, or whatever
that work requires. They receive an identifier unique within that batch, and the
terms of their engagement are set on that assignment: the rate they are paid, and
any bonus.

An agreement is issued to them. They read it, and sign it, or decline to.

**⚠** Until the agreement is signed and countersigned, the credentials for the
client's documents are not released to them. They may already appear on the
batch — planning does not wait — but access does. A worker who never signs never
receives access, and no administrator can hurry this past them.

### Working

The worker opens the batch they are assigned to. This is where everything
concerning that engagement is together: the work available to them, the work they
hold, the time they have recorded, and what the batch has earned them so far.

They see the terms they are working under. They do not see anyone else's — the
colleague at the next desk in the same role may be engaged on different terms,
and that is between that colleague and the company.

They claim work, singly or in bulk.

**⚠** If another worker claimed the same task in the same moment, one of them
receives it and the other is told plainly that it is taken. No task is ever held
by two people, and the loser of the race loses nothing else.

**⚠** Where a worker claims many tasks at once and some cannot be granted, the
rest still are, and they are told exactly which failed. One contested item never
costs them the others.

They do the annotation itself in the client's document, outside this system. They
return to record the time the task required and to comment on anything worth
knowing later, so context stays attached to the work rather than dispersing into
messages. Work they cannot complete they release, without penalty, and it becomes
available to someone else.

**⚠** Once a batch is completed, it is closed: no further claiming, releasing, or
editing. The record of what was done stands.

### Being paid

The worker's earnings rise as they work. From the moment a task is claimed it
carries a value — from the rate on their assignment and either the time they
report or the time their role expects for such an item — and they see it
immediately. Nothing waits for a cycle to close or for anyone to declare the work
finished.

They see three quantities, and the system never conflates them: what they have
**earned**, what is **outstanding**, and what has been **paid**. The first is the
value of work in hand. Only the third is money that has reached them.

Payment itself happens through the bank, outside the system. When it is recorded,
it captures their banking details as they stood at that moment. If they change
their account tomorrow, what was paid yesterday is unaffected — and if they
dispute a figure, the record that produced it still exists exactly as it was.

### Advancing

A worker who has done the work well may be granted a managerial role in a batch,
or an administrative role across the platform. Either way they remain the same
account: every task, every assignment and every taka they have earned stays with
them, and they may go on working while they administer. Advancement costs them
nothing they have built.

### Being suspended, and contesting it

**⚠** A worker whose access is suspended can no longer sign in. Their record is
untouched — assignments, work and earnings all survive, because suspending
someone must never disturb what they are owed.

At the moment of suspension, the means to contest it is created. The worker
requests a link, states their case once and at reasonable length, and an admin
approves or rejects it. Approval restores their access, and they resume with
everything they had.

The route by which they do this never discloses whether any particular address
belongs to an account, is suspended, or has a case outstanding. Every enquiry is
answered alike — a mechanism for the wrongly suspended must not double as a means
of discovering who works here.

---

## 3.3 Owner Interaction

### Composing the administration

The owner holds every permission, including the one that matters most: deciding
what everyone else may do.

Rather than choosing from a fixed list of administrator types, they compose one —
selecting the permissions it should carry and naming it. The business today needs
someone for projects, someone for money, someone for agreements, someone for the
workforce. Tomorrow it may need a quality administrator, or a recruitment
administrator, or one person holding several of these at once. Each is composed
the same way, and none requires a change to the software.

The owner grants these roles to accounts, and revokes them. Every grant records
who made it and when.

**⚠** No composition, however careless, can produce an administrator who may
alter a recorded payment, rewrite a signed agreement, or read an encrypted value
outside the audited path. Those acts are impossible for everyone. The owner
distributes authority; they cannot manufacture it.

### Overseeing

The owner arrives at a view of the business: how large the workforce is and how
it divides by standing, how many have completed their profiles, how many projects
and batches are running, how many agreements exist, and what has been paid — each
against the period before it, so that a change of direction is visible without
anyone running a report.

From there they reach anything: any project, any worker, any payment, any
agreement.

---

## 3.4 Project Admin Interaction

### Structuring work

A client engagement arrives. The admin creates a project for it and divides it
into batches.

For each batch they define the roles the work requires and what each role expects
of the people in it — whether a unit of work has a predictable handling time, or
whether the work varies so much that time must be measured directly. This choice
belongs to the role, and therefore to the nature of the work, not to the person
who will do it.

### Staffing

The admin searches the workforce by skill and by declared availability. They add
people to the batch individually, or in bulk from a list of addresses.

**⚠** A bulk addition is validated and shown in full before anything is written.
Addresses that match nobody, or people already on the batch, are reported before
the admin commits rather than discovered afterwards.

Each person added receives an identifier unique within the batch, and the terms
they are engaged on. Two people in the same role may be engaged on different
terms; the role does not dictate pay.

### Loading and returning work

Work arrives from the client as a spreadsheet. The admin imports it, and the
system checks every row before writing anything.

**⚠** A malformed row fails alone. The remaining nine hundred and ninety-nine are
accepted, and the admin is told exactly which failed and why. An import that
fails entirely on one bad line is useless to the person who prepared the file.

When the work is done it is exported in the same shape it was imported, so the
export can be edited and imported again. This is the ordinary way to change a
hundred records: export, edit, import.

### Granting and withdrawing access

The admin issues credentials for the client's documents to the people who need
them.

**⚠** The system refuses to release credentials to anyone without a completed
agreement, whatever the admin intends. Staffing may run ahead of signature;
access may not.

When the engagement ends, the admin withdraws every credential in the batch in a
single action. Nobody is left holding access to a client's material after their
involvement has finished.

### Watching progress

The admin sees what has been claimed, what has been completed, and how much time
has been recorded — without interrupting anyone to ask. Project and batch status
follows from their dates; nobody maintains it by hand.

**⚠** Where a task must change hands, the admin transfers it rather than deleting
and recreating it, so that the record of who did the work survives the change.
Credit for completed work is part of the payment record.

---

## 3.5 Finance Admin Interaction

### Establishing what is owed

The admin does not calculate anything. Earnings follow from recorded work through
a single calculation, applied identically wherever a figure appears — so the
figure the admin sees and the figure the worker sees are not two calculations
that happen to agree, but one calculation seen twice.

They see who is owed money. Separately, they see who can actually be paid — the
two differ, because a worker accrues earnings whether or not they have supplied
banking details. That difference is itself the useful thing: it names the people
whose payment is blocked on information only they can provide.

### Recording payment

Payment is made through the bank, outside the system. The admin records it here
against the person and the work it settles.

The record captures the recipient's banking details as they stood at that moment.

**⚠** No amount, recipient, or snapshot can be edited afterwards. A payment
recorded in error is archived — removed from what is outstanding without being
destroyed — and may be restored if archived by mistake. Only an archived record
may be deleted. Every correction is made by a further record, never by revising
an existing one.

### Reconciling

The admin exports the financial record in a form accounting's own spreadsheets
consume, and reconciles it against what the bank actually sent.

**⚠** Exporting places data beyond the system's protection, so the ability to
export is itself a privileged capability rather than a convenience available to
anyone who can read a list.

---

## 3.6 Compliance Admin Interaction

### Authoring an agreement

The admin writes an agreement template within the system, marking the places
where each recipient's own details belong — their name, the date, whatever the
agreement requires — rather than editing those by hand for every person.

They publish it as a version.

**⚠** A published version can never be altered. A change means a new version;
what has already been signed against the old one stays exactly as it was signed.

### Issuing and completing

The admin issues the agreement to a worker, and the recipient's details resolve
into it at that moment. They can see whether it has been viewed, signed, or
ignored.

The worker signs. The admin countersigns on behalf of the company, and the
agreement is complete.

**⚠** A worker may decline instead, which ends that agreement. It may also lapse
if its date passes without signature — though it can be extended before then, or
reinstated if it was voided in error. Only rejection is final.

Each signature is bound to the exact text signed by a digest computed over it, so
that any later alteration is not merely against the rules but detectable. Every
change of state is recorded permanently, together with who caused it, in an order
that cannot be rewritten — so a signature can be shown to have followed a reading
of the text rather than preceded it.

### Producing the document

When the agreement is needed as evidence, the admin produces it: identical for
both parties, reproducing names correctly in the signer's own script, and
reproducible unchanged for as long as the record is kept.

---

## 3.7 HR Admin Interaction

### Maintaining the vocabulary of capability

The admin maintains the catalogue of skills, grouped by the kind of annotation
each serves, and orders it so that what matters most appears first. This
catalogue is what makes capability searchable — without it, "who can do this?"
has no answer but memory.

**⚠** A skill still claimed by a worker, or still required by a project, cannot
simply be removed beneath them.

### Attending to one worker

The admin reaches everything about a single person in one place: their profile
and qualifications, the skills they claim, the batches they are on, the work they
have done, and what they have earned.

Where a worker is struggling with a form, or a correction has been agreed by
conversation, the admin corrects their details on their behalf. Every such change
names the admin who made it.

**⚠** The admin sees what their function requires and no more. Administering the
workforce does not entail seeing anyone's banking details.

### Changing a worker's standing

The admin suspends a worker whose access must end, and the system creates their
means of appeal in the same act. They hear appeals and approve or reject them,
restoring access where an appeal succeeds.

**⚠** Where a worker leaves permanently, the system decides which of two removals
applies rather than leaving it to the admin: a worker with any history — work,
payment, or a signed agreement — has their access withdrawn and their record
preserved, because the business is obliged to keep it. Only an account carrying
no such history is erased outright. Preservation is never a matter of an
administrator remembering to choose it.

---

## 3.8 Batch Manager Interaction

A batch manager is a worker holding a managerial role within one batch.
Everything in §3.2 applies to them; this section describes only what the role
adds.

### Planning the batch

The manager sees the availability their batch's members have declared, and the
progress of the work — what has been claimed, what remains, how much time has
been recorded.

**⚠** They do not see what any of them are paid. Coordinating a batch grants
sight of availability and work; a manager plans the work, and what each person is
paid for it remains between them and finance.

### Being paid for coordination

The manager records the time they spend coordinating, separately from any task.
They are paid for it hourly where their assignment carries an hourly rate, and by
a fixed sum for the engagement otherwise — coordination being sometimes engaged
by the hour and sometimes for the job.

**⚠** Their authority ends with the batch that granted it. It confers nothing
platform-wide, and nothing at all once that batch is over.

---

## Conclusion

Following each actor through their work exposes what a capability list cannot:
the order in which things must happen, and what the system owes a person when
something goes wrong.

Two patterns recur across every scenario. The first is that **failure is
answered, not merely prevented** — a contested claim tells the loser plainly, a
malformed import row fails alone, a refused upload gives its reason. In each case
the person is left able to act rather than merely stopped. The second is that
**the system refuses on its own authority where the stakes are high enough**:
credentials are withheld from an unsigned worker however the admin intends,
payment records resist editing however senior the editor, and a departing
worker's history is preserved without anyone choosing to preserve it. These are
not permissions an administrator holds and declines to use; they are acts the
system does not offer anyone.

**Next task:** [Use Case Diagram](04-use-case-diagram.md) — use-case modelling, the actors and scenarios above decomposed
into use cases, from the system as a whole down to the individual capability,
each referring back to the scenario it serves.
