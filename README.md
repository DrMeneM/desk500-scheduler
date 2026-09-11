# Desk 500 scheduler

This repository is an alarm clock. It contains no data and no logic.

Once a day it calls the GitHub API to start the `daily` run of the private
**Broker_agents** desk, and on the 1st of each month it starts the `monthly`
research cycle instead.

## Why it is separate, and why it is public

Free-plan **private** repositories run GitHub's `schedule:` cron on a
best-effort basis — it is frequently delayed and often never fires at all, so
the desk cannot reliably schedule itself. **Public** repositories schedule
normally and get unlimited Actions minutes. So the alarm clock lives out here
in public, and everything it is allowed to know is in this file.

## What it does NOT contain

No positions, no ledger, no prices, no reasoning, no API keys. The only
secret is `DESK_DISPATCH_TOKEN`, and Actions secrets stay encrypted in public
repositories exactly as they do in private ones.

**Never add a `pull_request` trigger to the workflow.** Secrets combined with
pull requests from forks is the standard way a token gets handed to a
stranger.

## Setup

1. **Fine-grained personal access token** (github.com → Settings → Developer
   settings → Fine-grained tokens), with access to **both** repositories:
   - `Broker_agents` → Repository permissions → **Actions: Read and write**
   - this repository → Repository permissions → **Contents: Read and write**
     (for the weekly keepalive commit)
2. Add it here as an Actions secret named **`DESK_DISPATCH_TOKEN`**.
3. Actions tab → *Desk 500 scheduler* → **Run workflow** → mode `daily`, to
   check it end to end. Then look at Broker_agents: a run should have started.

Note the token's expiry date somewhere you will see it. When it expires this
goes quiet, and silence is the only symptom.

## Daylight saving

Cron is always UTC, so the workflow fires at both 05:30 and 06:30 UTC and the
job keeps whichever one is 07:00 in Berlin. That tracks German daylight
saving without anyone editing anything twice a year — 07:30 Berlin stays
before the Xetra open all year round.

## The keepalive

GitHub disables scheduled workflows after 60 days of repository inactivity.
This repo would otherwise never change, switch itself off, and take the desk
down with it — silently. A commit to `heartbeat.txt` every Monday prevents
that.
