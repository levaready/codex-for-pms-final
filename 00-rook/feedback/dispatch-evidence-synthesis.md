# Dispatch 4.2: evidence synthesis

Fictional coursework. Synthesizes the database, tickets and supplied routing code reviewed in this chat. Data: 1,696 pings, 29 June–6 September 2026; 147 tickets, 29 June–7 September. Source detail and queries: weekly-pings-since-june.md, tickets-versus-pings.md and callout-reconstruction.md in this directory.

## Established patterns

- Acceptance before 12 August: 831/1,085 (76.6%). Release day: 11/25 (44.0%). After release day, 13 August–6 September: 380/586 (64.8%). Do not mix this with the 64.0% figure that includes release day.
- Misses jump from 25/1,085 (2.3%) before release to 7/25 (28%) on 12 August and 12/25 (48%) on 13 August. Daily samples are small and deployment hour is unknown.
- Weekly acceptance: 77.9% in week starting 3 August, 54.2% in mixed release week, then 65.8%, 66.7%, 72.7%. Aggregate recovery does not establish recovery for everyone.
- Farlight, Undertow, Meteor Mite and Vesper collectively received 49 pings/week across six full pre-release weeks versus 8.33 across three full post-release weeks: 83% fewer. Mixed release week excluded; cohort selected after observing outcomes.
- For weeks starting 3, 10, 17, 24, 31 August, Farlight pings/takes are 12/8, 10/4, 3/0, 1/0, 0/0. Gale: 13/10, 15/9, 17/12, 20/14, 21/16. Both miss a larger share after release but opportunities diverge. No offers is not a 0% acceptance rate.
- Farlight usually covers Uptown; Gale Eastgate. They are not matched controls. Availability, demand and proximity could explain differences.

## Tickets agree on symptoms and timing

- New disappearing-offer complaints begin 12 August; first quiet-stretch complaint is Farlight on 17 August. Earlier network/notification complaints already existed.
- 30 quiet-stretch and 16 disappearing/missed-offer tickets, including follow-ups. They are not 46 unique incidents. Relevant theme share: 1/40 before 12 August, 1/2 on release day, 44/105 afterward.
- All 15 explicit seven-day counts filed through 6 September match rolling database counts. Two on 7 September match available data but cannot be fully verified beyond the ping-data cutoff.
- All 16 disappearing/missed-offer tickets have a missed ping on the described responder/date; some dates contain multiple candidates. Temporal compatibility is not confirmed unique linkage or proof of on-screen duration.
- Some harmed responders have no tickets in this extract: Vesper and Meteor Mite. Absence of complaints is not absence of harm.

## New counterevidence: before-release outcomes

Compared all 16 responders across six full weeks, 29 June–9 August. Five adjacent-week transitions each: 80 comparisons. For each week, count misses and declines and compare next week's total pings. Exclude mixed release week.

| Prior-week condition | Comparisons | Fewer next week | Same | More |
|---|---:|---:|---:|---:|
| At least one miss | 22 | 8 (36%) | 2 | 12 |
| No misses | 58 | 23 (40%) | 18 | 17 |
| Three or more declines | 31 | 14 (45%) | 5 | 12 |
| Zero to two declines | 49 | 17 (35%) | 15 | 17 |

No clear pre-release association between a miss and fewer next-week offers in this check. Higher declines show only a weak descriptive association. The decline threshold is exploratory; repeated observations are not independent, counts are unadjusted for exposure, and demand, availability, location and prior volume are uncontrolled. Weekly comparisons can miss within-week effects. No causal or significance claim.

Farlight missed one and declined two in each of the weeks starting 13 and 20 July; offers then rose 11→12 and 12→13. This contradicts an automatic miss-to-starvation story.

Reproducible pre-release query:

```sql
WITH weekly AS (
  SELECT responder, date_trunc('week',sent_at)::date AS week,
    COUNT(*) AS pings,
    COUNT(*) FILTER (WHERE outcome='missed') AS missed,
    COUNT(*) FILTER (WHERE outcome='turned down') AS declined
  FROM rook.pings
  WHERE sent_at >= TIMESTAMP '2026-06-29' AND sent_at < TIMESTAMP '2026-08-10'
  GROUP BY 1,2
), paired AS (
  SELECT *, LEAD(pings) OVER(PARTITION BY responder ORDER BY week) AS next_pings
  FROM weekly
)
SELECT * FROM paired WHERE next_pings IS NOT NULL ORDER BY responder,week;
```

## Mechanism is plausible, causality unresolved

Supplied code penalizes misses/declines −0.12 and credits takes +0.08, bounded to 0–1 without automatic time recovery. 4.2 cuts timeout 90→60 seconds, raises proximity weight 0.45→0.60 and lowers history weight 0.40→0.25. Thus the maximum immediate total-score penalty per miss is smaller, 0.048→0.030; penalties did not become stronger per event. Production parity is unverified.

A conditional feedback loop remains plausible: more misses, lower history, lower rank on some future callouts, fewer opportunities to recover. The Gale may have more chances to earn credit, but net score and rank are unknown. Neither loop is established. The pre-release check weakens the simplest explanation but does not rule out a loop under more frequent misses after release.

Available records lack delivery/display/response timestamps, historical eligibility/availability and candidate ranks/scores. Manual overrides are another possible factor: the changelog lists an override audit log, but it is not exposed here. Assignment/arrival timing is also missing.

Conclusion: a real deterioration and redistribution of opportunities coincides with release. Tickets corroborate the symptoms. Misses alone do not explain who lost opportunities. Obtain historical ranking, availability, notification and override records to distinguish timeout, delivery, proximity and history effects.
