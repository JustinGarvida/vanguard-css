# Client Segmentation Service — General Requirements

## Problem

Every Vanguard digital experience that wants to personalize has to work out "who is this client?" itself. That means calling several systems (profile, accounts, advice, engagement) and applying its own segmentation logic. The result is duplicated effort, inconsistent answers between platforms, and a dependency on several teams for every new personalized feature.

## Synthetic data

Each JSON file represents a different upstream system that one of the mock services serves:

| File | Owns | Key fields |
|---|---|---|
| `profiles.json` | Demographics | `dateOfBirth`, `state`, `maritalStatus`, `employmentStatus`, `annualIncomeBand`, `preferredChannel` |
| `accounts.json` | Accounts and holdings | `accountType`, `ownerClientIds[]`, `status`, `balance`, `holdings[]` |
| `engagement.json` | Digital behavior | `lastLoginDate`, `logins90d`, `mobileAppUser`, `paperless`, `emailOptIn` |
| `advice.json` | Advice relationship | `adviceService`, `advisorId`, `riskTolerance`, `goals[]` |
| `edge-cases.json` | Test oracle | Client IDs with deliberately tricky data (see the data README) |

All dates are relative to `asOfDate` = **2026-10-01**. Compute ages and recency from that date, not from today, so results stay reproducible.

## Starter segment catalogue

The students implement these as **rules stored as data**, not hard-coded logic.

| Segment family | Segments | Based on |
|---|---|---|
| Service tier | `STANDARD` <$50K · `VOYAGER` $50K+ · `VOYAGER_SELECT` $500K+ · `FLAGSHIP` $1M+ · `FLAGSHIP_SELECT` $5M+ | Sum of open account balances |
| Life stage | `EARLY_CAREER` 22–34 · `MID_CAREER` 35–54 · `PRE_RETIREE` 55–64 · `RETIREE` 65+ | Age from `dateOfBirth` |
| Advice | `SELF_DIRECTED` · `DIGITALLY_ADVISED` · `HUMAN_ADVISED` | `adviceService` |
| Engagement | `DIGITALLY_ACTIVE` (login ≤30 days) · `DORMANT` (no login in 180+ days) | Engagement data |
| Opportunity | `ROLLOVER_CANDIDATE` (age 55+ with a 401K) · `EDUCATION_SAVER` (has a 529) · `ADVICE_PROSPECT` (≥$500K and self-directed) | Combined sources |

Service-tier families are **mutually exclusive**: a client gets exactly one tier. Opportunity segments **can overlap**. Must design for both.

## Out of scope

- Real Vanguard systems, data, or authentication.

## Open questions for the student team

1. Should segments be computed when requested, precomputed when data changes, or both? What are the trade-offs?
2. How should a client be segmented when a source disagrees with another (see the `CONFLICTING_DATA` edge cases)?
3. Which client should a joint account's assets count toward?
4. Should segment results be versioned by the rule-set version that produced them?
5. Which technology stack? Leave this to the team, with a constraint such as "containerized, any mainstream language."
