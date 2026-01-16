# AppSec Mental Model (Day 1)

## What scales vs what never scales
Mental model for where leverage actually comes from in AppSec programs.
### Scales
- **Tools**
  - Scale coverage and consistency.
  - Automate repeatable work: scanning, triage routing, ticket creation, reporting.
  - Standardize: templates, checklists, CI gates, definitions of done.
  - Tools scale *reach*, not correctness.

- **Judgment**
  - Scale impact by making fewer bad calls.
  - Prioritize by exploitability + business impact, not severity alone.
  - Identify false positives quickly and document defensible reasoning.
  - Think in attack paths and failure modes, not “findings.”
  - Decide when *not* to act (defer, accept, or monitor risk intentionally).


- **Relationships**
  - Scale adoption.
  - Trust reduces friction; teams bring you in earlier.
  - Influence > authority: the best AppSec programs don’t rely on enforcement.
  - Relationships turn “security says no” into “security helps us ship.”

### Never scales
- **Manual effort**
  - Reviewing every finding by hand.
  - Re-explaining the same vuln repeatedly.
  - Being the human router for tickets, owners, and deadlines.

- **Heroics**
  - Last-minute fire drills.
  - “Only I know how this works.”
  - Saving releases through adrenaline instead of systems.
  - Designing systems that require constant vigilance to succeed.


- **Raw output**
  - Number of scans, findings, or tools.
  - Activity is not outcome.

## High-leverage AppSec engineer: where they spend time
- Build repeatable systems (automation, templates, pipelines).
- Improve signal quality (tuning, dedupe, triage rules).
- Clarify risk (what matters, why, and what “good” looks like).
- Teach patterns (enable devs to fix classes of bugs, not single bugs).
- Create alignment (security + engineering + product agree on priorities).

## High-leverage AppSec engineer: what they avoid
- Being a human scanner / ticket factory.
- Arguing about severities without a threat model.
- Doing work that the system should do (routing, reminders, manual reporting).
- Taking ownership of fixes that belong to engineering teams.
- Letting tools define truth instead of using judgment.
