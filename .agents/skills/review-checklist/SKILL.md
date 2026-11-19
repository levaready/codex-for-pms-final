---
name: review-checklist
description: Review a product or project brief for clear ownership, measurable success, consistent scope, problem-first reasoning, and early detection of potential issues. Use when the user points to a brief and asks for a checklist review.
---

# Review checklist

Read the entire brief the user identifies, plus directly referenced supporting material needed to interpret it and allowed by project instructions. If no brief is identifiable, ask for its path or text. Review without editing unless the user requests changes.

Check substance, not just whether headings exist:

| Check | What to look for |
| --- | --- |
| Ownership | An explicitly accountable person or clearly assigned role owns the brief and its outcome. Distinguish the owner from contributors, reviewers, and approvers; a list of stakeholders alone is insufficient. |
| Success | A measurable outcome tied to the problem, with a defined metric or observable acceptance condition, target, evaluation window, and measurement source. Where change is claimed, look for a baseline or comparison. Shipping a feature alone does not establish success. |
| Consistent scope | The affected users, problem, proposed work, exclusions, rollout, and success measures agree from opening to conclusion. Identify both sides of contradictions, unexplained additions, and commitments that disappear. Explicitly separated future work is not scope drift. |
| Problem before fix | The brief explains who is affected, what goes wrong, and why it matters before detailing the solution. Separate evidence from hypotheses; naming a desired feature is not a problem statement. A short summary mentioning the solution is acceptable if the substantive argument establishes the problem first. |
| Early detection | The brief identifies likely failure modes or risky assumptions and a practical way to notice trouble before the final evaluation. Look for an observable warning signal, data or feedback source, monitoring owner, review cadence, trigger, and response such as investigation, escalation, pause, or rollback. A risk list or final success metric alone is insufficient. |

For early detection, assess whether the signal can arrive soon enough to act. Match monitoring to the brief's risks and scale; use affected segments when aggregate results could hide harm. If collection or instrumentation is missing, flag that dependency. Qualitative triggers are acceptable when concrete and actionable.

Report each check as **Pass**, **Needs clarification**, or **Missing**. Use Needs clarification for partial, ambiguous, or contradictory content. Cite a section, line, or short excerpt supporting each assessment; for omissions, state what was not found. If referenced evidence is unavailable, disclose that limit instead of treating its contents as known.

Return a concise five-row table with columns **Check**, **Status**, **Evidence or gap**, and **Smallest useful fix**. Follow with the highest-priority gaps to resolve, if any. Prioritize contradictions and missing accountability, success criteria, or actionable warning mechanisms over wording polish.

Keep proposed fixes within the brief's intended scope. Never invent owners, evidence, baselines, targets, or agreed thresholds. Label suggested wording and monitoring mechanisms as proposals; use explicit placeholders for decisions still needed. Distinguish a documented plan to establish a threshold from a threshold already ready for use. Do not imply that passing this review proves feasibility or authorizes launch.
