# Tokyo-Region Failover Runbook (Draft)

> Status: **Draft** — follows up on open action item #2 from the Q4 Platform Readiness Review
> ("Draft the Tokyo-region failover runbook and store it under docs/.")
> Owner: Platform Engineering Group / Tokyo office
> Source: `Q4_Platform_Review.pdf`

This runbook covers the Tokyo-region failover procedure for the Q4 rollout. All source
timeline events in the readiness review are recorded in **UTC**; the Tokyo office runs on
**local time (Asia/Tokyo, UTC+09:00)**, so every event has been converted below.

## Rollout Timeline — Tokyo Local Time

Tokyo is **9 hours ahead of UTC**. Because of this offset, one event falls past midnight
into the following day.

| # | Event | UTC time | Tokyo local time | Notes |
|---|-------|----------|------------------|-------|
| 1 | Engineering sync kickoff | 09:00 UTC | **18:00 (JST)** | Same day |
| 2 | Database migration window | 14:30 UTC | **23:30 (JST)** | Same day — late evening |
| 3 | Release freeze begins | 17:45 UTC | **02:45 (JST), next day** | ⚠️ **Crosses midnight into the following day** |
| 4 | Customer-facing launch | 08:00 UTC | **17:00 (JST)** | Same day |

## Midnight-Crossing Callout

**The release freeze begins at 17:45 UTC, which is 02:45 Tokyo local time on the
following day.** This is the only event that lands past midnight, and it falls in the very
early hours of the morning. The Tokyo on-call engineers covering the freeze window must be
scheduled for an overnight shift, and the freeze should not be treated as a normal
business-hours event for the Tokyo team.

## Failover Procedure

### 1. Pre-failover (during Engineering sync kickoff — 18:00 JST)
- Confirm the Tokyo on-call rotation covers the full window through the release freeze and
  launch, remembering the freeze begins at **02:45 JST the next day**.
- Verify Tokyo-region capacity headroom and confirm regional read replicas are in sync.
- Validate monitoring and alerting dashboards are visible from the Tokyo office.

### 2. Database migration window (23:30 JST)
- Freeze writes in the primary region before the migration window opens.
- Apply the migration and confirm replication lag returns to zero.
- Snapshot the pre-migration state so the region can be restored if failover is required.

### 3. Release freeze (02:45 JST, next day)
- Halt all deployments and configuration changes.
- Take the Tokyo failover decision point: if regional health checks degrade, promote the
  Tokyo region and redirect traffic.
- Confirm the freeze remains in place until the customer-facing launch.

### 4. Customer-facing launch (17:00 JST)
- Reopen deployments and begin the staged traffic ramp.
- Watch Tokyo-region error rates and latency for the first hour after launch.
- Escalate to the global SRE rota if Tokyo-region metrics regress.

## Post-Failover Actions

- Publish the status page banner for the launch window (open action item #3).
- Rotate the service API keys if the launch proceeds cleanly (open action item #1).
- Record the incident or drill outcome and update this runbook with lessons learned.

## Reference

- Review document: `Q4_Platform_Review.pdf` (5 pages, final, signed off)
- Timeline page (one-page extract for the Tokyo kickoff invite): `timeline-tokyo.pdf`
- Tokyo time offset: **UTC+09:00** (no daylight saving time)
