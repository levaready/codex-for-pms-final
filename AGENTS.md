# Rook Industries: course working file

## Session scope, Product School lab

This directory is coursework for Product School's "Codex for PMs"
certification. Everything in it is a fictional teaching scenario.

- Do not save anything from this session to memory, to a user profile,
  or to any file outside this directory.
- Do not carry context from this directory into unrelated sessions.
- Rook Industries is not a real company. Nothing here is a fact about
  the world.
- Read and write only within this directory.
  Exceptions, for the course-setup and wrap-up skills only:
  - When the student asks you to check their setup, save their work or wrap up a session, that request is their yes. You may run the GitHub command-line program installed at ~/.codex-for-pms/gh for those checks and saves, and look in that folder to find it.
  - For a repair, first tell the student in one plain sentence what you are about to do, and act only after they say yes. Repairs may: run that GitHub program (including setting this folder's own git sign-in setting and changing this repo's visibility back to Public); copy the student's own course files into this directory from another folder on their computer (copy only; never move, edit or delete the originals); rename something outside this directory that blocks setup, by adding "-old" to its name (never delete it); and, only if the student once said no to trusting this folder, change that one trust setting for this folder in Codex's settings file, keeping a copy of the file first.
  Outside this directory you still never write, edit or delete anything else.

<!-- Keep the block above at the top of this file. Everything you add
     during the course goes below this line. -->

---

## Working context

The user is Dispatch’s incoming PM, succeeding Priya Raghunathan (left
21 August 2026). Sources: Company wiki (including its four pages and three
databases) and `00-rook/company/notes/handoff-from-priya.docx`, dated
21 August. Source dates vary; later outcomes remain unverified.

### Products and customers

Rook sells subscriptions per active responder. Responders are independent,
not employees; handlers manage their readiness and are the main console
users and feedback channel. Quartermasters own equipment stock and approvals.

**Dispatch:** handler web console and responder phone app. Incidents trigger
ranking of available responders; offers go out sequentially until accepted.
Acceptance assigns the incident and marks the responder engaged. Routing
configuration ships with releases, not as a handler-adjustable setting.

**Supply:** requisitions, approvals, fulfillment, maintenance and field failure
reports. Dispatch writes the **Responder Availability Record**; Supply reads
it for maintenance scheduling. Availability changes can affect Supply without
any Supply change. Releases follow a monthly 4.x train. Active-callout support
tickets bypass the queue.

Rook holds no mapping to responder legal identities. Never infer identities
or design features requiring that mapping; consult Security Policy 4.1 before
changes touching responder records.

### People

| Person | Role and responsibility |
| --- | --- |
| Helen Achebe | Director of Product; roadmap and commitments. |
| Marcus Oyelaran | Dispatch engineering manager; first stop for feasibility and uncertainty. |
| Wen Li | Dispatch staff engineer; built routing and understands ranking. |
| Sofia Marino | Product designer; console, phone app, September interviews. |
| Ravi Menon | Data analyst; weekly acceptance reporting and analysis. |
| Nadia Hoffmann | Support lead; handler complaints and tickets. |
| Priya Raghunathan | Departed PM; handover includes hypotheses to validate. |

### Vocabulary and measures

- **Callout:** request to attend an incident. **Ping:** one offer to one responder.
  **Taken / turned down / missed:** accepted / declined / unanswered before
  timeout. Keep declines and misses separate. **Ping wait:** release-wide timeout.
- **Acceptance rate:** pings taken divided by all pings; headline weekly metric.
  **Time-to-accept:** median seconds from sent to taken. **Coverage gap:** no
  available responder matches required capability tags, distinct from rejection.
- **Routing priority:** ranking using travel-time proximity, availability,
  capability match and recent acceptance history. Declines and misses reduce
  the history component, lowering priority for subsequent callouts.
- **Capability tags:** competencies matched to incidents. **Mutual aid/shared
  cover:** responders covering one another; unsupported today, Q4 exploration.

### Situation and open work

**4.2 shipped 12 August 2026:** proximity weighted up relative to acceptance
history; ping wait cut **90 → 60 seconds**; persistent console filters; three
fixes. Proximity addressed complaints about nearby responders being passed
over. Priya describes console and mobile (since 4.1) as stable; routing is risky.

Priya reports lower acceptance and more complaints after 4.2. She favors
August seasonality and opposes reverting. These are her judgments: investigate
routing, timeout and seasonality before attributing cause. September recovery
is unverified. She predicts filter-persistence tickets and calls them cosmetic;
validate their impact.

The roadmap was last reviewed **30 June**. It still marks routing, timeout
tuning and **Availability Confidence** (score beside stated availability)
committed to 4.2. Confidence is absent from release notes; confirm delivery.
Priya says items were squeezed out; reconcile remaining commitments with Helen.
Supply’s **requisition approval chains** (second approval for large requests)
is committed to 4.3. Handler phone app and shared cover are Q4 explorations.
Commitment changes go through Product.

First priorities: document actual routing with Wen; investigate acceptance
with Ravi and Marcus; establish regular support contact with Nadia; clarify
commitments with Helen. Avoid treating the stale roadmap or handover opinions
as verified current status.

- Session analysis (29 June–6 September data): ping acceptance 76.6% → 64.0%; misses 2.3% → 18.0%; declines 21.1% → 18.0% across 12 August.
- Callouts lacking recorded acceptance rose 5.5% → 11.1%; actual assignment/arrival times are unavailable.
- All 16 responders declined; Farlight, Undertow, Vesper and Meteor Mite lost 66–75% of daily offers. Six had fewer daily offers and higher misses; causality is unproven.
- Code penalizes misses like declines (−0.12; acceptance +0.08), with no automatic recovery; capability is a soft ranking input. Confirm production safeguards with Wen.
- Handler accounts support disappearing offers followed by quiet stretches; full segment results are in `00-rook/feedback/acceptance-breakdown.md`.
- Next: obtain delivery/display/response times and ranking snapshots; separate timeout, delivery and penalty effects. Location freshness, capability enforcement and annual seasonality remain unverified.
