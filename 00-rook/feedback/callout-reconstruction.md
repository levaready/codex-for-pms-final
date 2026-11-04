# Callout reconstruction: testing the 4.2 feedback-loop hypothesis

## Status and scope

Desk reconstruction from the database, supplied code, support tickets and three existing interviews. No new interviews conducted and no messages sent to Wen or Ravi. Their proposed work below remains to be done.

Hypothesis: shorter offer window increases misses; history penalties then lower subsequent ranks and reduce future opportunities. This is plausible, not demonstrated. Cohorts were selected after seeing outcomes, so comparisons are exploratory, not a causal control group.

Read-only database extraction: all 68 callouts involving Farlight, The Undertow, Meteor Mite or Vesper with a ping on/after 10 August, including every recorded ping to other responders on those callouts: 141 rows, no database truncation. Comparative weekly extraction: 69 rows for those four plus Captain Vantage, Nightwell and The Gale, since 1 June. Available data ends 6 September. Tickets: all 40 for the four affected responders; returned tickets concern Farlight and The Undertow only.

Times below are database local time. Connector serializes timestamps with Z, but schema explicitly says local timestamp without time zone; no timezone conversion is applied. A ping's timestamp records sending, not acceptance. Gaps between sends are not measured response latency.

## What the records establish

| Responder | Mean weekly pings before | Mean weekly pings after | Misses / pings before | Misses / pings after |
|---|---:|---:|---:|---:|
| Captain Vantage | 12.0 | 16.7 | 2/72 | 5/50 |
| Farlight | 12.0 | 1.3 | 3/72 | 3/4 |
| Meteor Mite | 11.2 | 2.3 | 2/67 | 4/7 |
| Nightwell | 15.0 | 19.7 | 3/90 | 6/59 |
| The Gale | 13.0 | 19.3 | 3/78 | 6/58 |
| The Undertow | 12.0 | 2.0 | 1/72 | 4/6 |
| Vesper | 13.8 | 2.7 | 3/83 | 5/8 |

Before = six full weeks, 29 June–9 August; after = three full weeks, 17 August–6 September. Mixed release week excluded here. All seven show increased miss rates, but only four lose ping volume. Different locations, capabilities and availability remain uncontrolled.

## Incident walkthrough candidates

### Farlight: #41245, 28 August, Mill District

- Previous recorded ping: #41160, 23 August 13:51:03, missed; Cindermark next sent 13:52:05, taken.
- #41245: Farlight sent 28 August 19:28:20, missed; Cindermark next sent 19:29:21, taken (61 seconds between sends).
- Ticket #3110, Linda Pruitt, 29 August: first offer in five days arrived the previous night and disappeared before Farlight could answer. Strong temporal match, not an explicit callout-ID link.
- No further Farlight ping through 6 September.
- Ask Linda/Farlight to confirm the incident, phone alert/display time, when they tried to answer, network/app state and whether any explicit decline occurred. Ask Wen for candidate/rank snapshots during the five-day gap, not just on this callout.
- Important complication: #41021 was taken on 14 August after four recorded misses on 12–14 August. She could still receive and take offers after misses. A simplistic immediate lockout explanation is false.

### The Undertow: #41394, 5 September, Harborside

- Previous recorded ping: #41235, 27 August 23:18:46, missed; The Drift sent 23:19:48, taken.
- #41394: The Undertow sent 5 September 15:17:47, missed; Stormwrack sent 15:18:48, taken.
- Ticket #3140, Desmond Okafor, 5 September 18:17:47: first offer in nine days that afternoon disappeared before he could answer. Strong temporal match; confirm ID with handler.
- Ask for an exact notification-to-attempt sequence and whether availability/gear/network conditions changed during the gap.
- Earlier ticket #3014 (14 July) reports missing an offer while out of signal. Notification/connectivity failures predate 4.2; do not attribute every miss to the timeout change.

### Meteor Mite and The Gale: #41097, 19 August, Eastgate

- The Gale sent 17:29:31, missed; Meteor Mite sent 17:30:34, taken.
- This reverses the simple “Gale always wins, Mite cannot answer” story. Mite could still take a call after release, and Gale also missed.
- Kip's 4 September interview describes Mite quiet while Gale was very busy. Same handler is a useful comparison, but does not match location, capability or availability.
- Ask Kip to identify this incident and compare it with #41152 (22 August: Gale missed, then Mite missed, then Nightwell took) and #41254 (29 August: Mite missed, Nightwell missed, Gale took).
- Look for what differed on Mite's successful offer: phone proximity, network, activity, time to view, incident suitability. Kip's existing interview does not establish a disappearing-offer event for Mite.

### Vesper: #41311, 1 September, Old Town

- Previous recorded ping: #41214, 26 August 18:09:13, missed.
- #41311: Vesper sent 11:42:44, missed; Captain Vantage sent 11:43:47, turned down; Nightwell sent 11:44:10, taken.
- Aunt Dot's 3 September interview describes the phone being away from Vesper, offers disappearing before he reaches it, and quiet stretches. It supplies a plausible human mechanism but no incident dates or IDs.
- Use #41311 as a recall prompt; do not label it as the incident she described until confirmed. Ask for a successful offer too (for example #41050, 17 August) to compare circumstances.

## Evidence that challenges or narrows the explanation

1. Captain Vantage, Nightwell and The Gale all miss more often after release yet receive more pings. A miss is not sufficient to predict starvation.
2. Mr. Ambrose reports a disappearing offer for Captain Vantage in his 2 September interview, despite increasing volume. The exact incident is unidentified. Ask for the date and compare with an affected responder under similar opportunity conditions.
3. Farlight accepts after several misses; Mite accepts after Gale misses. The loop, if present, is conditional and cumulative, not a universal lockout.
4. Supplied config reduces history weight from 0.40 to 0.25 while increasing proximity from 0.45 to 0.60. A 0.12 history penalty changes total score by at most 0.030 in 4.2, versus 0.048 before, away from the floor. It did not become a larger per-miss ranking penalty. More misses and changed proximity weights could still combine to change outcomes.
5. Supplied code treats misses and declines alike and has no time-based recovery; taking an offer adds 0.08. Scores clip at 0 and 1, so do not sum penalties into a claimed actual score without the starting score and full history.
6. Later pings often still go first to affected responders: Farlight in #41245, Undertow in #41394, Vesper in #41311. These received-offer sequences cannot show how often they ranked below someone who accepted first on other callouts.
7. Ticket recollections and counts are not exact telemetry. #3060 says no phone alert for three days by 17 August, while a ping was recorded as taken late on 14 August. Do not silently reconcile recollection, sent events and device alerts as equivalent.

## Proposed work for Wen and Ravi

Wen: verify deployed version/time, notification/timeout implementation, production history storage/reset behavior and any safeguards. For all seven responders, retrieve historical candidate inclusion/exclusion, availability and engagement, capability requirements, location freshness/travel time, component scores, final rank, offer delivery/display/expiry/response times and changes after each outcome. Cover callouts they never received as well as the selected callouts.

Ravi: build one chronological event table per responder, reconcile tickets to IDs with handlers, and compute pings per eligible callout and per available hour, rank distribution, misses vs declines, and assignment outcomes. Stratify comparisons by area, time and capability, checking demand shifts. Keep pre-release, release day and post-release separate when deployment time is unavailable. Start with all four affected and the three comparators, then test findings across all 16 to reduce selection bias.

Joint diagnostic: replay ranking on frozen historical candidate snapshots with (a) actual weights/history, (b) old proximity/history weights, and (c) history held at its pre-change value. This can isolate ranking sensitivity for a single decision, not simulate the whole system: different assignments change later availability and history. Replaying a 90-second timeout needs actual late response attempts; the current data cannot tell who would have accepted between 60 and 90 seconds.

Support for feedback loop: misses precede score/rank losses on otherwise comparable eligible opportunities, followed by fewer offers; removing the relevant history change restores position in snapshot replay.
Evidence against it: eligibility/demand/location explains the gaps; ranks remain stable despite misses; or proximity-only replay explains the ordering. If eligible snapshots are missing, classify cause as unresolved.

Walkthrough protocol for each handler: confirm incident ID/date; reconstruct alert → visible offer → attempted response → disappearance; capture device/network/availability context; compare one success and one failure; ask about intentional declines and changed routines; record uncertainty. Avoid suggesting that the penalty caused the gap before collecting their account.

## Source interviews

- [Aunt Dot / Vesper, 3 September](https://app.notion.com/p/Aunt-Dot-handler-for-Vesper-3eac8516f50181a0bf04dd3b61063feb)
- [Kip / Meteor Mite and The Gale, 4 September](https://app.notion.com/p/Kip-handler-for-Meteor-Mite-and-The-Gale-3eac8516f501815f9b0bc8b80e97d20a)
- [Mr. Ambrose / Captain Vantage, 2 September](https://app.notion.com/p/Mr-Ambrose-handler-for-Captain-Vantage-3eac8516f50181499a11d78c4982adee)

Code: 00-rook/code/dispatch-routing/{config,history,routing,offer}.py. Illustrates mechanism; production parity unverified.

## All reconstructed callout rows

| Callout | Incident time | Area | Incident | Responder | Sent at | Outcome |
|---|---|---|---|---|---|---|
| 40938 | 2026-08-10 02:01:19 | Old Town | A mime is painting the town hall green. | Vesper | 2026-08-10 02:01:40 | taken |
| 40945 | 2026-08-10 13:33:08 | Old Town | A clown is redirecting traffic on the ring road. | Vesper | 2026-08-10 13:33:28 | turned down |
| 40946 | 2026-08-10 14:03:12 | Eastgate | A man who says he is from the year 2091 is redirecting traffic on the ring road. | Meteor Mite | 2026-08-10 14:03:36 | turned down |
| 40946 | 2026-08-10 14:03:12 | Eastgate | A man who says he is from the year 2091 is redirecting traffic on the ring road. | Sgt. Bulwark | 2026-08-10 14:04:13 | turned down |
| 40946 | 2026-08-10 14:03:12 | Eastgate | A man who says he is from the year 2091 is redirecting traffic on the ring road. | The Gale | 2026-08-10 14:04:33 | taken |
| 40949 | 2026-08-10 15:38:28 | Uptown | A man in a hot air balloon is painting the town hall green. | Farlight | 2026-08-10 15:38:46 | taken |
| 40950 | 2026-08-10 17:34:31 | Uptown | A man claiming to be the rightful mayor is lifting parked cars outside the stadium. | Farlight | 2026-08-10 17:34:47 | taken |
| 40953 | 2026-08-10 18:48:43 | Harborside | A woman in a brass diving suit has locked the doors of the clock tower from the inside. | Ironvale | 2026-08-10 18:48:57 | turned down |
| 40953 | 2026-08-10 18:48:43 | Harborside | A woman in a brass diving suit has locked the doors of the clock tower from the inside. | The Undertow | 2026-08-10 18:49:29 | taken |
| 40956 | 2026-08-10 19:45:02 | Harborside | An accountant with a jet pack has flooded the underpass. | The Undertow | 2026-08-10 19:45:14 | taken |
| 40959 | 2026-08-10 21:08:24 | Kingsbridge | A man in a hot air balloon has taken over the bridge. | Farlight | 2026-08-10 21:08:38 | turned down |
| 40959 | 2026-08-10 21:08:24 | Kingsbridge | A man in a hot air balloon has taken over the bridge. | Sgt. Falkirk | 2026-08-10 21:09:18 | taken |
| 40960 | 2026-08-10 21:14:51 | Eastgate | A former weather presenter has frozen the fountain in the square. | Meteor Mite | 2026-08-10 21:15:00 | taken |
| 40963 | 2026-08-11 06:41:08 | Eastgate | A woman in a beekeeping suit is painting the town hall green. | Meteor Mite | 2026-08-11 06:41:14 | taken |
| 40970 | 2026-08-11 18:57:48 | Southport | Four people in identical gray raincoats have stopped the ferry mid-crossing. | The Undertow | 2026-08-11 18:58:01 | turned down |
| 40970 | 2026-08-11 18:57:48 | Southport | Four people in identical gray raincoats have stopped the ferry mid-crossing. | Stormwrack | 2026-08-11 18:58:24 | taken |
| 40975 | 2026-08-11 21:00:14 | Old Town | A man carrying a very large magnet has stopped the ferry mid-crossing. | Vesper | 2026-08-11 21:00:33 | taken |
| 40981 | 2026-08-12 09:04:41 | Southport | A woman riding a mechanical ostrich has stopped the ferry mid-crossing. | The Undertow | 2026-08-12 09:04:56 | missed |
| 40981 | 2026-08-12 09:04:41 | Southport | A woman riding a mechanical ostrich has stopped the ferry mid-crossing. | Stormwrack | 2026-08-12 09:05:58 | taken |
| 40985 | 2026-08-12 16:35:16 | Uptown | A group calling itself the Lantern Society is holding a bus full of commuters at the depot. | Sgt. Falkirk | 2026-08-12 16:35:28 | turned down |
| 40985 | 2026-08-12 16:35:16 | Uptown | A group calling itself the Lantern Society is holding a bus full of commuters at the depot. | Cindermark | 2026-08-12 16:35:58 | turned down |
| 40985 | 2026-08-12 16:35:16 | Uptown | A group calling itself the Lantern Society is holding a bus full of commuters at the depot. | Farlight | 2026-08-12 16:36:14 | taken |
| 40987 | 2026-08-12 19:03:16 | Midtown | A man who says he is from the year 2091 is releasing the animals at the city zoo. | Captain Vantage | 2026-08-12 19:03:31 | missed |
| 40987 | 2026-08-12 19:03:16 | Midtown | A man who says he is from the year 2091 is releasing the animals at the city zoo. | Meteor Mite | 2026-08-12 19:04:32 | turned down |
| 40987 | 2026-08-12 19:03:16 | Midtown | A man who says he is from the year 2091 is releasing the animals at the city zoo. | The Gale | 2026-08-12 19:04:58 | missed |
| 40987 | 2026-08-12 19:03:16 | Midtown | A man who says he is from the year 2091 is releasing the animals at the city zoo. | Nightwell | 2026-08-12 19:05:59 | taken |
| 40989 | 2026-08-12 19:51:00 | Mill District | A woman in a brass diving suit is removing the doors from the courthouse. | Ironvale | 2026-08-12 19:51:16 | turned down |
| 40989 | 2026-08-12 19:51:00 | Mill District | A woman in a brass diving suit is removing the doors from the courthouse. | Farlight | 2026-08-12 19:51:25 | missed |
| 40989 | 2026-08-12 19:51:00 | Mill District | A woman in a brass diving suit is removing the doors from the courthouse. | Cindermark | 2026-08-12 19:52:27 | taken |
| 40990 | 2026-08-12 20:02:26 | Kingsbridge | Four people in identical gray raincoats are redirecting traffic on the ring road. | Farlight | 2026-08-12 20:02:48 | turned down |
| 40990 | 2026-08-12 20:02:26 | Kingsbridge | Four people in identical gray raincoats are redirecting traffic on the ring road. | Sgt. Falkirk | 2026-08-12 20:03:25 | taken |
| 40992 | 2026-08-13 07:34:40 | Lakeshore | A retired stage magician has taken the keys to the tram yard. | Vesper | 2026-08-13 07:34:58 | missed |
| 40992 | 2026-08-13 07:34:40 | Lakeshore | A retired stage magician has taken the keys to the tram yard. | Halfmoon | 2026-08-13 07:36:00 | missed |
| 40992 | 2026-08-13 07:34:40 | Lakeshore | A retired stage magician has taken the keys to the tram yard. | The Longcast | 2026-08-13 07:37:02 | taken |
| 40993 | 2026-08-13 09:45:09 | Uptown | A retired lion tamer has jammed every radio in the market. | Farlight | 2026-08-13 09:45:33 | missed |
| 40993 | 2026-08-13 09:45:09 | Uptown | A retired lion tamer has jammed every radio in the market. | Cindermark | 2026-08-13 09:46:34 | turned down |
| 40993 | 2026-08-13 09:45:09 | Uptown | A retired lion tamer has jammed every radio in the market. | Sgt. Falkirk | 2026-08-13 09:46:40 | taken |
| 40994 | 2026-08-13 11:24:38 | Harborside | A group calling itself the Lantern Society is stealing every streetlight bulb on the high street. | The Undertow | 2026-08-13 11:24:44 | missed |
| 40996 | 2026-08-13 13:26:58 | Harborside | A pastry chef is removing the doors from the courthouse. | Stormwrack | 2026-08-13 13:27:08 | missed |
| 40996 | 2026-08-13 13:26:58 | Harborside | A pastry chef is removing the doors from the courthouse. | The Undertow | 2026-08-13 13:28:09 | taken |
| 40997 | 2026-08-13 13:42:47 | Midtown | A retired lion tamer has frozen the fountain in the square. | Meteor Mite | 2026-08-13 13:43:10 | missed |
| 40997 | 2026-08-13 13:42:47 | Midtown | A retired lion tamer has frozen the fountain in the square. | Captain Vantage | 2026-08-13 13:44:12 | turned down |
| 40997 | 2026-08-13 13:42:47 | Midtown | A retired lion tamer has frozen the fountain in the square. | The Gale | 2026-08-13 13:44:48 | missed |
| 40997 | 2026-08-13 13:42:47 | Midtown | A retired lion tamer has frozen the fountain in the square. | Nightwell | 2026-08-13 13:45:51 | taken |
| 40998 | 2026-08-13 14:51:17 | Kingsbridge | A former weather presenter has stopped the ferry mid-crossing. | Farlight | 2026-08-13 14:51:31 | missed |
| 40998 | 2026-08-13 14:51:17 | Kingsbridge | A former weather presenter has stopped the ferry mid-crossing. | Sgt. Falkirk | 2026-08-13 14:52:32 | taken |
| 40999 | 2026-08-13 15:12:23 | Eastgate | A man dressed as a lighthouse is removing the doors from the courthouse. | Meteor Mite | 2026-08-13 15:12:38 | missed |
| 40999 | 2026-08-13 15:12:23 | Eastgate | A man dressed as a lighthouse is removing the doors from the courthouse. | The Gale | 2026-08-13 15:13:40 | taken |
| 41000 | 2026-08-13 15:54:45 | Harborside | A ventriloquist has taken the keys to the tram yard. | The Undertow | 2026-08-13 15:55:03 | taken |
| 41003 | 2026-08-13 18:44:07 | Harborside | A woman on stilts has barricaded the public library. | The Undertow | 2026-08-13 18:44:20 | missed |
| 41005 | 2026-08-14 02:52:05 | Southport | A clown is climbing the television mast. | Stormwrack | 2026-08-14 02:52:27 | turned down |
| 41005 | 2026-08-14 02:52:05 | Southport | A clown is climbing the television mast. | The Undertow | 2026-08-14 02:53:00 | missed |
| 41006 | 2026-08-14 02:52:44 | Old Town | A retired stage magician has stopped the ferry mid-crossing. | The Longcast | 2026-08-14 02:53:02 | turned down |
| 41006 | 2026-08-14 02:52:44 | Old Town | A retired stage magician has stopped the ferry mid-crossing. | Nightwell | 2026-08-14 02:53:24 | turned down |
| 41006 | 2026-08-14 02:52:44 | Old Town | A retired stage magician has stopped the ferry mid-crossing. | Vesper | 2026-08-14 02:53:46 | taken |
| 41009 | 2026-08-14 07:26:19 | Eastgate | Three men in matching capes have stopped the ferry mid-crossing. | Meteor Mite | 2026-08-14 07:26:28 | missed |
| 41009 | 2026-08-14 07:26:19 | Eastgate | Three men in matching capes have stopped the ferry mid-crossing. | The Gale | 2026-08-14 07:27:29 | taken |
| 41011 | 2026-08-14 10:22:09 | Riverside | A chess grandmaster is holding a bus full of commuters at the depot. | The Undertow | 2026-08-14 10:22:29 | missed |
| 41011 | 2026-08-14 10:22:09 | Riverside | A chess grandmaster is holding a bus full of commuters at the depot. | Ironvale | 2026-08-14 10:23:31 | taken |
| 41016 | 2026-08-14 16:32:30 | Uptown | A clown has flooded the underpass. | Farlight | 2026-08-14 16:32:44 | missed |
| 41016 | 2026-08-14 16:32:30 | Uptown | A clown has flooded the underpass. | Cindermark | 2026-08-14 16:33:45 | taken |
| 41017 | 2026-08-14 17:10:04 | Hillcrest | A woman with a trained flock of pigeons has flooded the underpass. | Vesper | 2026-08-14 17:10:16 | missed |
| 41017 | 2026-08-14 17:10:04 | Hillcrest | A woman with a trained flock of pigeons has flooded the underpass. | Captain Vantage | 2026-08-14 17:11:19 | taken |
| 41021 | 2026-08-14 21:22:16 | Uptown | A man who says he is from the year 2091 is releasing the animals at the city zoo. | Farlight | 2026-08-14 21:22:33 | taken |
| 41023 | 2026-08-15 13:01:14 | Old Town | A woman riding a mechanical ostrich is releasing the animals at the city zoo. | Nightwell | 2026-08-15 13:01:38 | turned down |
| 41023 | 2026-08-15 13:01:14 | Old Town | A woman riding a mechanical ostrich is releasing the animals at the city zoo. | The Longcast | 2026-08-15 13:01:48 | turned down |
| 41023 | 2026-08-15 13:01:14 | Old Town | A woman riding a mechanical ostrich is releasing the animals at the city zoo. | Vesper | 2026-08-15 13:02:09 | taken |
| 41025 | 2026-08-15 15:17:56 | Eastgate | A woman in a brass diving suit is releasing the animals at the city zoo. | The Gale | 2026-08-15 15:18:08 | turned down |
| 41025 | 2026-08-15 15:17:56 | Eastgate | A woman in a brass diving suit is releasing the animals at the city zoo. | Sgt. Bulwark | 2026-08-15 15:18:38 | turned down |
| 41025 | 2026-08-15 15:17:56 | Eastgate | A woman in a brass diving suit is releasing the animals at the city zoo. | Meteor Mite | 2026-08-15 15:18:48 | taken |
| 41026 | 2026-08-15 15:33:44 | Midtown | A man dressed as a lighthouse has locked the doors of the clock tower from the inside. | Vesper | 2026-08-15 15:33:56 | turned down |
| 41026 | 2026-08-15 15:33:44 | Midtown | A man dressed as a lighthouse has locked the doors of the clock tower from the inside. | Nightwell | 2026-08-15 15:34:13 | taken |
| 41032 | 2026-08-15 19:38:55 | Eastgate | A man claiming to be the rightful mayor has taken over the bridge. | Sgt. Bulwark | 2026-08-15 19:39:00 | missed |
| 41032 | 2026-08-15 19:38:55 | Eastgate | A man claiming to be the rightful mayor has taken over the bridge. | Meteor Mite | 2026-08-15 19:40:01 | taken |
| 41034 | 2026-08-15 20:55:50 | Old Town | A group calling itself the Lantern Society is removing the doors from the courthouse. | The Longcast | 2026-08-15 20:56:04 | missed |
| 41034 | 2026-08-15 20:55:50 | Old Town | A group calling itself the Lantern Society is removing the doors from the courthouse. | Nightwell | 2026-08-15 20:57:07 | missed |
| 41034 | 2026-08-15 20:55:50 | Old Town | A group calling itself the Lantern Society is removing the doors from the courthouse. | Vesper | 2026-08-15 20:58:08 | taken |
| 41039 | 2026-08-16 10:46:13 | Hillcrest | A woman in a beekeeping suit is emptying a bank. | Vesper | 2026-08-16 10:46:31 | missed |
| 41039 | 2026-08-16 10:46:13 | Hillcrest | A woman in a beekeeping suit is emptying a bank. | Captain Vantage | 2026-08-16 10:47:34 | taken |
| 41040 | 2026-08-16 11:16:00 | Eastgate | A man convinced he is a wolf is redirecting traffic on the ring road. | Meteor Mite | 2026-08-16 11:16:17 | missed |
| 41040 | 2026-08-16 11:16:00 | Eastgate | A man convinced he is a wolf is redirecting traffic on the ring road. | Nightwell | 2026-08-16 11:17:20 | turned down |
| 41041 | 2026-08-16 13:46:22 | Greenway | A man carrying a very large magnet is releasing the animals at the city zoo. | The Undertow | 2026-08-16 13:46:27 | turned down |
| 41041 | 2026-08-16 13:46:22 | Greenway | A man carrying a very large magnet is releasing the animals at the city zoo. | The Drift | 2026-08-16 13:46:42 | taken |
| 41042 | 2026-08-16 14:04:52 | Hillcrest | A man who says he is from the year 2091 is removing the doors from the courthouse. | Vesper | 2026-08-16 14:05:10 | missed |
| 41042 | 2026-08-16 14:04:52 | Hillcrest | A man who says he is from the year 2091 is removing the doors from the courthouse. | Captain Vantage | 2026-08-16 14:06:13 | taken |
| 41043 | 2026-08-16 16:03:08 | Old Town | A retired stage magician is refusing to leave the museum roof. | Captain Vantage | 2026-08-16 16:03:13 | turned down |
| 41043 | 2026-08-16 16:03:08 | Old Town | A retired stage magician is refusing to leave the museum roof. | Vesper | 2026-08-16 16:03:23 | taken |
| 41050 | 2026-08-17 08:54:11 | Old Town | A woman on stilts is painting the town hall green. | Nightwell | 2026-08-17 08:54:20 | missed |
| 41050 | 2026-08-17 08:54:11 | Old Town | A woman on stilts is painting the town hall green. | Vesper | 2026-08-17 08:55:21 | taken |
| 41054 | 2026-08-17 14:10:00 | Greenway | A man who insists he is a statue is emptying a bank. | The Undertow | 2026-08-17 14:10:18 | turned down |
| 41054 | 2026-08-17 14:10:00 | Greenway | A man who insists he is a statue is emptying a bank. | The Drift | 2026-08-17 14:10:43 | taken |
| 41059 | 2026-08-17 19:06:45 | Harborside | A man claiming to be the rightful mayor is redirecting traffic on the ring road. | The Undertow | 2026-08-17 19:07:03 | taken |
| 41070 | 2026-08-18 07:36:39 | Eastgate | A retired lion tamer is removing the doors from the courthouse. | Meteor Mite | 2026-08-18 07:37:03 | turned down |
| 41070 | 2026-08-18 07:36:39 | Eastgate | A retired lion tamer is removing the doors from the courthouse. | The Gale | 2026-08-18 07:37:13 | taken |
| 41079 | 2026-08-18 16:17:53 | Harborside | A retired lion tamer has taken over the announcements at the train station. | The Undertow | 2026-08-18 16:18:10 | missed |
| 41079 | 2026-08-18 16:17:53 | Harborside | A retired lion tamer has taken over the announcements at the train station. | Stormwrack | 2026-08-18 16:19:12 | turned down |
| 41079 | 2026-08-18 16:17:53 | Harborside | A retired lion tamer has taken over the announcements at the train station. | Ironvale | 2026-08-18 16:19:23 | taken |
| 41092 | 2026-08-19 13:27:52 | Uptown | A woman in a beekeeping suit is holding a sit-in at the power station. | Sgt. Falkirk | 2026-08-19 13:28:04 | turned down |
| 41092 | 2026-08-19 13:27:52 | Uptown | A woman in a beekeeping suit is holding a sit-in at the power station. | Farlight | 2026-08-19 13:28:40 | missed |
| 41097 | 2026-08-19 17:29:19 | Eastgate | A chess grandmaster has jammed every radio in the market. | The Gale | 2026-08-19 17:29:31 | missed |
| 41097 | 2026-08-19 17:29:19 | Eastgate | A chess grandmaster has jammed every radio in the market. | Meteor Mite | 2026-08-19 17:30:34 | taken |
| 41102 | 2026-08-19 18:42:21 | Old Town | A retired stage magician has jammed every radio in the market. | Vesper | 2026-08-19 18:42:37 | missed |
| 41102 | 2026-08-19 18:42:21 | Old Town | A retired stage magician has jammed every radio in the market. | The Longcast | 2026-08-19 18:43:40 | taken |
| 41114 | 2026-08-20 16:41:45 | Eastgate | A person in a full suit of armor has taken over the announcements at the train station. | Meteor Mite | 2026-08-20 16:41:50 | missed |
| 41130 | 2026-08-21 16:45:02 | Harborside | A clown has locked the doors of the clock tower from the inside. | The Undertow | 2026-08-21 16:45:07 | missed |
| 41130 | 2026-08-21 16:45:02 | Harborside | A clown has locked the doors of the clock tower from the inside. | Stormwrack | 2026-08-21 16:46:08 | missed |
| 41130 | 2026-08-21 16:45:02 | Harborside | A clown has locked the doors of the clock tower from the inside. | The Drift | 2026-08-21 16:47:10 | taken |
| 41142 | 2026-08-22 11:13:31 | Uptown | A man in a hot air balloon has barricaded the public library. | Farlight | 2026-08-22 11:13:54 | turned down |
| 41142 | 2026-08-22 11:13:31 | Uptown | A man in a hot air balloon has barricaded the public library. | Cindermark | 2026-08-22 11:14:14 | taken |
| 41152 | 2026-08-22 20:51:47 | Midtown | A man wearing a crown of antlers is climbing the television mast. | The Gale | 2026-08-22 20:52:11 | missed |
| 41152 | 2026-08-22 20:51:47 | Midtown | A man wearing a crown of antlers is climbing the television mast. | Meteor Mite | 2026-08-22 20:53:13 | missed |
| 41152 | 2026-08-22 20:51:47 | Midtown | A man wearing a crown of antlers is climbing the television mast. | Nightwell | 2026-08-22 20:54:15 | taken |
| 41153 | 2026-08-23 00:10:14 | Old Town | A man who insists he is a statue is lifting parked cars outside the stadium. | Vesper | 2026-08-23 00:10:19 | missed |
| 41153 | 2026-08-23 00:10:14 | Old Town | A man who insists he is a statue is lifting parked cars outside the stadium. | Nightwell | 2026-08-23 00:11:22 | missed |
| 41153 | 2026-08-23 00:10:14 | Old Town | A man who insists he is a statue is lifting parked cars outside the stadium. | Captain Vantage | 2026-08-23 00:12:25 | taken |
| 41155 | 2026-08-23 05:34:48 | Midtown | A man dressed as a lighthouse is painting the town hall green. | Vesper | 2026-08-23 05:34:55 | turned down |
| 41155 | 2026-08-23 05:34:48 | Midtown | A man dressed as a lighthouse is painting the town hall green. | Nightwell | 2026-08-23 05:35:27 | taken |
| 41160 | 2026-08-23 13:50:58 | Mill District | A retired stage magician is holding a bus full of commuters at the depot. | Farlight | 2026-08-23 13:51:03 | missed |
| 41160 | 2026-08-23 13:50:58 | Mill District | A retired stage magician is holding a bus full of commuters at the depot. | Cindermark | 2026-08-23 13:52:05 | taken |
| 41165 | 2026-08-23 20:14:27 | Lakeshore | A retired lion tamer has taken the keys to the tram yard. | Halfmoon | 2026-08-23 20:14:35 | missed |
| 41165 | 2026-08-23 20:14:27 | Lakeshore | A retired lion tamer has taken the keys to the tram yard. | Vesper | 2026-08-23 20:15:38 | missed |
| 41165 | 2026-08-23 20:14:27 | Lakeshore | A retired lion tamer has taken the keys to the tram yard. | The Longcast | 2026-08-23 20:16:41 | taken |
| 41186 | 2026-08-24 21:40:09 | Old Town | A man dressed as a lighthouse is climbing the television mast. | Vesper | 2026-08-24 21:40:13 | turned down |
| 41214 | 2026-08-26 18:08:51 | Old Town | A woman in a brass diving suit is holding a sit-in at the power station. | Vesper | 2026-08-26 18:09:13 | missed |
| 41232 | 2026-08-27 20:03:07 | Midtown | A former weather presenter has frozen the fountain in the square. | Meteor Mite | 2026-08-27 20:03:12 | turned down |
| 41232 | 2026-08-27 20:03:07 | Midtown | A former weather presenter has frozen the fountain in the square. | Captain Vantage | 2026-08-27 20:03:35 | turned down |
| 41232 | 2026-08-27 20:03:07 | Midtown | A former weather presenter has frozen the fountain in the square. | Nightwell | 2026-08-27 20:03:44 | turned down |
| 41235 | 2026-08-27 23:18:39 | Harborside | A man wearing a crown of antlers is climbing the television mast. | The Undertow | 2026-08-27 23:18:46 | missed |
| 41235 | 2026-08-27 23:18:39 | Harborside | A man wearing a crown of antlers is climbing the television mast. | The Drift | 2026-08-27 23:19:48 | taken |
| 41245 | 2026-08-28 19:28:12 | Mill District | A woman in a beekeeping suit is holding a bus full of commuters at the depot. | Farlight | 2026-08-28 19:28:20 | missed |
| 41245 | 2026-08-28 19:28:12 | Mill District | A woman in a beekeeping suit is holding a bus full of commuters at the depot. | Cindermark | 2026-08-28 19:29:21 | taken |
| 41254 | 2026-08-29 09:15:31 | Eastgate | An accountant with a jet pack is emptying a bank. | Meteor Mite | 2026-08-29 09:15:52 | missed |
| 41254 | 2026-08-29 09:15:31 | Eastgate | An accountant with a jet pack is emptying a bank. | Nightwell | 2026-08-29 09:16:54 | missed |
| 41254 | 2026-08-29 09:15:31 | Eastgate | An accountant with a jet pack is emptying a bank. | The Gale | 2026-08-29 09:17:56 | taken |
| 41311 | 2026-09-01 11:42:28 | Old Town | A retired lion tamer has barricaded the public library. | Vesper | 2026-09-01 11:42:44 | missed |
| 41311 | 2026-09-01 11:42:28 | Old Town | A retired lion tamer has barricaded the public library. | Captain Vantage | 2026-09-01 11:43:47 | turned down |
| 41311 | 2026-09-01 11:42:28 | Old Town | A retired lion tamer has barricaded the public library. | Nightwell | 2026-09-01 11:44:10 | taken |
| 41393 | 2026-09-05 15:06:03 | Midtown | A woman riding a mechanical ostrich is painting the town hall green. | Meteor Mite | 2026-09-05 15:06:20 | missed |
| 41393 | 2026-09-05 15:06:03 | Midtown | A woman riding a mechanical ostrich is painting the town hall green. | Nightwell | 2026-09-05 15:07:22 | taken |
| 41394 | 2026-09-05 15:17:41 | Harborside | Four people in identical gray raincoats are releasing the animals at the city zoo. | The Undertow | 2026-09-05 15:17:47 | missed |
| 41394 | 2026-09-05 15:17:41 | Harborside | Four people in identical gray raincoats are releasing the animals at the city zoo. | Stormwrack | 2026-09-05 15:18:48 | taken |

## Comparative weekly rows

| Week | Responder | Pings | Taken | Missed | Declined |
|---|---|---:|---:|---:|---:|
| 2026-06-29 | Captain Vantage | 12 | 10 | 0 | 2 |
| 2026-06-29 | Farlight | 12 | 9 | 0 | 3 |
| 2026-06-29 | Meteor Mite | 11 | 7 | 0 | 4 |
| 2026-06-29 | Nightwell | 15 | 11 | 1 | 3 |
| 2026-06-29 | The Gale | 13 | 10 | 1 | 2 |
| 2026-06-29 | The Undertow | 12 | 9 | 0 | 3 |
| 2026-06-29 | Vesper | 14 | 11 | 0 | 3 |
| 2026-07-06 | Captain Vantage | 13 | 10 | 1 | 2 |
| 2026-07-06 | Farlight | 12 | 9 | 0 | 3 |
| 2026-07-06 | Meteor Mite | 12 | 9 | 0 | 3 |
| 2026-07-06 | Nightwell | 14 | 10 | 0 | 4 |
| 2026-07-06 | The Gale | 12 | 9 | 0 | 3 |
| 2026-07-06 | The Undertow | 11 | 9 | 0 | 2 |
| 2026-07-06 | Vesper | 13 | 10 | 1 | 2 |
| 2026-07-13 | Captain Vantage | 12 | 8 | 0 | 4 |
| 2026-07-13 | Farlight | 11 | 8 | 1 | 2 |
| 2026-07-13 | Meteor Mite | 10 | 6 | 0 | 4 |
| 2026-07-13 | Nightwell | 15 | 12 | 1 | 2 |
| 2026-07-13 | The Gale | 13 | 10 | 0 | 3 |
| 2026-07-13 | The Undertow | 12 | 9 | 1 | 2 |
| 2026-07-13 | Vesper | 15 | 13 | 1 | 1 |
| 2026-07-20 | Captain Vantage | 11 | 7 | 1 | 3 |
| 2026-07-20 | Farlight | 12 | 9 | 1 | 2 |
| 2026-07-20 | Meteor Mite | 11 | 7 | 1 | 3 |
| 2026-07-20 | Nightwell | 16 | 12 | 1 | 3 |
| 2026-07-20 | The Gale | 14 | 11 | 1 | 2 |
| 2026-07-20 | The Undertow | 13 | 10 | 0 | 3 |
| 2026-07-20 | Vesper | 14 | 11 | 0 | 3 |
| 2026-07-27 | Captain Vantage | 12 | 9 | 0 | 3 |
| 2026-07-27 | Farlight | 13 | 10 | 0 | 3 |
| 2026-07-27 | Meteor Mite | 12 | 9 | 1 | 2 |
| 2026-07-27 | Nightwell | 15 | 11 | 0 | 4 |
| 2026-07-27 | The Gale | 13 | 11 | 1 | 1 |
| 2026-07-27 | The Undertow | 12 | 10 | 0 | 2 |
| 2026-07-27 | Vesper | 13 | 11 | 1 | 1 |
| 2026-08-03 | Captain Vantage | 12 | 9 | 0 | 3 |
| 2026-08-03 | Farlight | 12 | 8 | 1 | 3 |
| 2026-08-03 | Meteor Mite | 11 | 8 | 0 | 3 |
| 2026-08-03 | Nightwell | 15 | 12 | 0 | 3 |
| 2026-08-03 | The Gale | 13 | 10 | 0 | 3 |
| 2026-08-03 | The Undertow | 12 | 9 | 0 | 3 |
| 2026-08-03 | Vesper | 14 | 12 | 0 | 2 |
| 2026-08-10 | Captain Vantage | 13 | 8 | 1 | 4 |
| 2026-08-10 | Farlight | 10 | 4 | 4 | 2 |
| 2026-08-10 | Meteor Mite | 10 | 4 | 4 | 2 |
| 2026-08-10 | Nightwell | 16 | 9 | 2 | 5 |
| 2026-08-10 | The Gale | 15 | 9 | 3 | 3 |
| 2026-08-10 | The Undertow | 11 | 4 | 5 | 2 |
| 2026-08-10 | Vesper | 12 | 6 | 4 | 2 |
| 2026-08-17 | Captain Vantage | 15 | 11 | 2 | 2 |
| 2026-08-17 | Farlight | 3 | 0 | 2 | 1 |
| 2026-08-17 | Meteor Mite | 4 | 1 | 2 | 1 |
| 2026-08-17 | Nightwell | 18 | 14 | 2 | 2 |
| 2026-08-17 | The Gale | 17 | 12 | 2 | 3 |
| 2026-08-17 | The Undertow | 4 | 1 | 2 | 1 |
| 2026-08-17 | Vesper | 5 | 1 | 3 | 1 |
| 2026-08-24 | Captain Vantage | 17 | 13 | 1 | 3 |
| 2026-08-24 | Farlight | 1 | 0 | 1 | 0 |
| 2026-08-24 | Meteor Mite | 2 | 0 | 1 | 1 |
| 2026-08-24 | Nightwell | 20 | 13 | 2 | 5 |
| 2026-08-24 | The Gale | 20 | 14 | 2 | 4 |
| 2026-08-24 | The Undertow | 1 | 0 | 1 | 0 |
| 2026-08-24 | Vesper | 2 | 0 | 1 | 1 |
| 2026-08-31 | Captain Vantage | 18 | 13 | 2 | 3 |
| 2026-08-31 | Meteor Mite | 1 | 0 | 1 | 0 |
| 2026-08-31 | Nightwell | 21 | 15 | 2 | 4 |
| 2026-08-31 | The Gale | 21 | 16 | 2 | 3 |
| 2026-08-31 | The Undertow | 1 | 0 | 1 | 0 |
| 2026-08-31 | Vesper | 1 | 0 | 1 | 0 |

Farlight has zero pings in week starting 31 August; grouped query returns no row for that week.

## Queries

```sql
SELECT c.callout_number, c.happened_at, c.area, c.what_happened, p.responder, p.sent_at, p.outcome FROM rook.callouts c JOIN rook.pings p USING (callout_number) WHERE c.callout_number IN (SELECT callout_number FROM rook.pings WHERE sent_at >= TIMESTAMP '2026-08-10' AND responder IN ('Farlight','The Undertow','Meteor Mite','Vesper')) ORDER BY c.happened_at, c.callout_number, p.sent_at
```

```sql
SELECT date_trunc('week', sent_at)::date AS week_start, responder, COUNT(*) AS pings, COUNT(*) FILTER (WHERE outcome='taken') AS taken, COUNT(*) FILTER (WHERE outcome='missed') AS missed, COUNT(*) FILTER (WHERE outcome='turned down') AS declined FROM rook.pings WHERE sent_at >= TIMESTAMP '2026-06-01' AND responder IN ('Farlight','The Undertow','Meteor Mite','Vesper','The Gale','Nightwell','Captain Vantage') GROUP BY 1,2 ORDER BY 1,2
```

```sql
SELECT * FROM rook.support_tickets WHERE about_responder IN ('Farlight','The Undertow','Meteor Mite','Vesper') ORDER BY filed_at
```

