# Operator quickstart

For someone who has just cloned this repository and has to produce one honest
observation. It takes about two minutes. Every command and every exit code
below was walked on 2026-09-08 (nbb v1.5.212, Node v26.7.0, macOS); if a step
here does not reproduce, the step is wrong and should be fixed, not worked
around.

**This repository holds no credentials and grants no authority.** Nothing here
moves funds, contacts anyone, deploys anything, or admits a country. If a
procedure seems to require any of those, stop and read
`docs/operating-contract.md` — it is the authority, not this page.

## Prerequisites

- `nbb` on `PATH` (`nbb --version`) and Node 18+ for `fetch`/`AbortSignal.timeout`.
- Outbound HTTPS to `x402.nexus`. No API key, wallet, or account is used.
- No install step. There is no `package.json`, no `node_modules`, no `.env` to fill.

## 1. Check the contract before you touch anything

```
nbb test/profile_test.cljk
```

25 named checks. Read the **exit code**, not the last line:

| exit | meaning | what to do |
|---|---|---|
| 0 | `x402 profile contract: all green` | proceed |
| 1 | one `FAIL <check-name>` line per failure | fix the named thing; see §4 |
| 2 | `REFUSED: cannot read <path>` | an input is missing or unparseable — **this is not a pass** |

Every check prints its name on PASS too, so a check that ran and passed never
looks like a check that was skipped. Run this first: if the repository is
already red, you cannot tell your change apart from what you inherited.

## 2. Take one observation

```
nbb scripts/observe.cljk
```

One bounded GET of `https://x402.nexus/qr/capabilities` (15 s timeout,
redirects refused), then it writes `reports/latest-observation.edn` and prints
the record without the body. It sends nothing, pays nothing, and messages
no one.

A reachable run looks like this (2026-09-08, HTTP 200):

```
{:observed-at "2026-09-08T12:45:32.823Z" :url "https://x402.nexus/qr/capabilities"
 :http-status 200 :reachable? true
 :sha256 "637dea536138a7ba5f08ab11a2b35979aa2ef2fc617da05d14d92553a2f2a24c"
 :physical-issuance :unmeasured :external-demand :unmeasured}
```

`:physical-issuance` and `:external-demand` stay `:unmeasured` in every record
this script writes. A reachable endpoint is not a printed ticket and not a
customer. **Unmeasured is not zero** — do not let a downstream summary silently
turn one into the other.

## 3. Decide whether the observation is news

**The digest is the identity; the timestamp is not.** `:observed-at` changes on
every run, so the file is dirty after every run even when nothing upstream
moved. Compare the digest, not the file:

```
git diff -- reports/latest-observation.edn        # always non-empty after a run
```

- **`:sha256` unchanged** → the capability document did not move. Nothing to
  report. `git checkout -- reports/latest-observation.edn` and stop. (Measured:
  the 2026-09-08 run returned the same digest the 2026-09-06 record already
  carried.)
- **`:sha256` changed** → upstream changed. Commit the new record *and* write a
  dated report in `reports/` saying what changed and what it does not prove.
  Re-run §1 afterwards: the observation record is an input to the contract test.

## 4. When it fails

Both failure modes are real and were exercised, not inferred.

**Endpoint unreachable.** `observe.cljs` exits **2** and still writes a record,
so the failure is on disk rather than only in your terminal:

```
{:observed-at "..." :url "..." :reachable? false :error "fetch failed"}
```

Running the contract test against that failure record gives exit **1** with
exactly three failures — `observation-digest-matches-recorded-body`,
`observation-body-parses-as-nexus-qr-v1`,
`observation-keeps-unmeasured-claims-explicit`. That is the expected shape of a
failed observation, not a broken repository. Restore the last good record
(`git checkout -- reports/latest-observation.edn`), then investigate the
endpoint. Do not commit a failure record as if it were an observation.

**Do not hand-edit the report to make anything green.** Editing the body
without recomputing the digest fails exactly one check —
`observation-digest-matches-recorded-body`, exit 1 — which is precisely what
that check exists to catch.

## 5. Where the real work goes

Observation is step 1 of six in `docs/operating-contract.md`. The rest —
market research, merchant identification, pilot proposals, reconciliation — is
written to dated reports under `reports/`, with primary-source URLs and
observation dates.

`data/markets.edn` holds hypotheses only. Every market is `:status :hypothesis`
and stays there: admission runs hypothesis → researched → operator-reviewed →
pilot-authorized → live-qualified, and **the last two stages are never automatic**.
`EEA` and `LATAM` are research buckets that must resolve to named jurisdictions
before anything else is true of them.

`data/runtime.edn` records measured provisioning state (`:provisioned? false`,
`:scheduled? false`, `:external-outreach-enabled? false`). Cloning this
repository does not schedule a Bot, and discovering a profile is not
provisioning it.
