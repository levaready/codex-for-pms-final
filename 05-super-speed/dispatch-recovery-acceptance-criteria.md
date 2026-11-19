# Dispatch recovery: engineering acceptance criteria

These criteria translate the selected product policies in the [brief](dispatch-recovery-brief.md). They are specifications to verify, not tests already passed or authorization to deploy.

## Recovery behavior

- A miss still applies the 0.12 penalty, bounded by the existing score floor. Record the actual deduction, original miss time and recovery already processed.
- For actual deduction D and elapsed hours t, cumulative scheduled recovery is D × min(1, max(0, (t − 24) / 72)). Recovery requires no new offer or acceptance.
- For a full 0.12 deduction, scheduled recovery is zero at 24 hours, 0.04 at 48 hours, 0.08 at 72 hours and 0.12 at 96 hours. A 0.03 actual deduction schedules only 0.01 at 48 hours and 0.03 at 96 hours.
- Each miss has its own schedule. A subsequent miss does not reset earlier schedules. Deliberate turn-downs receive no automatic recovery; acceptance credits remain unchanged.
- Repeated processing cannot restore the same points twice. Multiple concurrent events must not lose deductions or duplicate credits.
- Reaching 1.0 through acceptance or recovery cancels all remaining recovery from earlier misses. Cancelled recovery cannot later offset another penalty.
- Historical recovery requires verified actual deductions and timestamps, with subsequent score changes and ceiling cancellations accounted for. Unknown losses remain unchanged and are flagged for review. Historical ping counts alone are insufficient.

## Offers and presentation

- The recovery-only experiment retains the current weights, 0.12 penalties and 60-second wait beginning when the send function returns. Test the proposed 90-second wait separately.
- Only responses received by the server strictly before the deadline while the offer remains active can take the callout. Assignment and validity checks must prevent double assignment and duplicate processing. A response at the deadline is late.
- Late responses show “This offer has expired.” A late tap cannot reclaim the callout.
- Connection loss after a Take tap shows “Confirming—assignment not yet confirmed.” Reconcile with authoritative server state; do not infer assignment or expiry from connection loss alone.
- Linda sees the last 7 days compared with the previous 7, including total pings and separate taken, turned-down and missed counts. Verified zero activity shows “No pings”; missing records show “Data unavailable.”
- Recovery uses plain-language status and per-miss timing, with no numeric history score. Distinguish pending, active and no pending recovery; distinguish unavailable recovery records from no pending recovery. Show older losses requiring review separately.
- Explanations do not claim a known cause for reduced offers, a guaranteed callout or a fixed place in a queue.

## Pilot gates

- Shadow calculation changes neither offers nor operational scores. Wen and Marcus verify calculation correctness and logging before a live pilot.
- Apply approved eligibility criteria consistently to the candidate pairs in the brief. Monitor responders competing for the same callouts; they are not unaffected controls.
- Collect availability history and the timing data needed for evaluation. Distinguish missing timestamps from confirmed events. Define the comparison method and how demand and capability match are handled before interpreting opportunity changes.
- Ravi proposes numeric eligibility, minimum meaningful improvement in pings per available hour, minimum observation volume and the maximum rate of callouts with no recorded acceptance. Marcus and Ravi propose baseline-derived median and 90th-percentile assignment-delay limits, measurement windows and sample requirements. PM approval of all thresholds is required before launch.
- Run the live pilot for 14 days with daily monitoring and a day-7 review. Low observation volume yields an inconclusive result; extension requires review.
- A breach of an approved guardrail automatically reverts pilot routing to a validated no-recovery score state and triggers investigation by Marcus. Preserve assignments and audit records. Verify rollback against intervening takes and turn-downs before launch; disabling future recovery alone is insufficient.

## Remaining implementation specifications

Engineering still needs to specify persistence, event ordering, retry/idempotency handling, recovery calculation cadence, historical replay, rollback storage and monitoring. Design needs screen layouts and handling of mixed active/pending recovery schedules. Define exact activity-window boundaries, time-zone display and refresh behavior. These details must satisfy the policies above; unresolved historical data or baseline gaps block the dependent live-pilot work.
