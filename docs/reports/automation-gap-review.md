# Automation Gap Review

## Overview
Recent documentation and ADRs highlight several automation areas that remain unimplemented or partially wired. The notes below consolidate the highest-impact gaps so we can prioritize fixes and unblock CI/reporting.

## Findings

### 1) Log stash plumbing is still design-only
- **Current state:** Design exists but helper scripts (`Write-LogStashEntry.ps1`, cleanup) and workflow wiring are absent; log paths remain ad-hoc and indices/retention are undefined.
- **Risk/impact:** CI artifacts cannot be traced cleanly to commits or runs, and build workspaces will keep growing without pruning.
- **Action slice:** Deliver an MVP that ships the helper and wires it into `Test.ps1`/`Build.ps1`, adds cleanup and retention, and begins publishing bundle zips from CI jobs for traceability.

### 2) DevModeAgentCli not integrated into automation
- **Current state:** ADR-2025-009 follow-ups (unit tests, making the CLI the automation entry point, and schema/contract checks against `reports/dev-mode-bind.json`) remain open.
- **Risk/impact:** Automation still drives the PowerShell binder directly, so path/version guardrails are unenforced and intent parsing lacks regression coverage.
- **Action slice:** Add the unit test suite, reroute automation playbooks through DevModeAgentCli, and validate binder output against the schema before execution.

### 3) Ollama executor lacks end-to-end automated coverage
- **Current state:** ADR-2025-019 notes the absence of an automated executor suite, including integration tests, command vetting, mock server, timeout/turn-limit handling, error paths, and performance checks.
- **Risk/impact:** Changes can regress security vetting or conversation handling, and manual testing slows feedback.
- **Action slice:** Ship Phase 1 with a mock server and command-vetting tests, then layer conversation/timeout/error scenarios and an integration harness so CI can exercise executor flows without a real Ollama instance.

## Next actions
- Scope and size the three gaps above for the next planning cycle; treat log stash MVP and DevModeAgentCli wiring as near-term because they unblock traceability and guardrails.
- Add CI tasks to exercise any new helpers (log-stash index updates, DevModeAgentCli tests, Ollama executor scenarios) so coverage stays enforced.
- Track completion of each action slice with explicit checkboxes (design, implementation, CI coverage) so the gaps close visibly and do not regress.
