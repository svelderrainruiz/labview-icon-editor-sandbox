# Automation Gap Review

## Overview
Recent documentation and ADRs highlight several automation areas that remain unimplemented or partially wired. The notes below consolidate the highest-impact gaps so we can prioritize fixes and unblock CI/reporting.

## Findings

### 1) Log stash plumbing is still design-only
- The log stash design calls out ad-hoc log paths, missing indices, and no retention strategy, and the helper scripts (`Write-LogStashEntry.ps1`, cleanup) plus workflow wiring steps are not yet present.
- Impact: makes CI artifacts hard to trace to commits/runs and leaves workspaces growing without pruning.
- Suggested next steps: implement the MVP slice (helper + wiring in `Test.ps1`/`Build.ps1`), add cleanup, and start publishing bundle zips from CI jobs.

### 2) DevModeAgentCli not integrated into automation
- ADR-2025-009 lists follow-ups to add unit tests, wire the CLI as the only automation entry point, and add schema/contract checks against `reports/dev-mode-bind.json`; all are unchecked.
- Impact: automation still relies on the PowerShell binder directly, missing guardrails for expected-path/version enforcement and lacking regression tests for intent parsing.
- Suggested next steps: land the unit test suite, route automation playbooks through DevModeAgentCli, and add schema validation for binder output before execution.

### 3) Ollama executor lacks end-to-end automated coverage
- ADR-2025-019 documents that there is no automated test suite for the executor and specifically calls out the absence of integration tests and other coverage gaps (command vetting, mock server, timeout/turn limits, error paths, performance).
- Impact: executor changes risk regressions in security vetting and conversation handling; manual testing slows feedback.
- Suggested next steps: prioritize the Phase 1 items (mock server + command vetting tests), then add conversation/timeout/error scenarios and the integration harness so CI can exercise executor flows without a real Ollama instance.

## Next actions
- Scope and size the three gaps above for the next planning cycle; treat log stash MVP and DevModeAgentCli wiring as near-term because they unblock traceability and guardrails.
- Add CI tasks to exercise any new helpers (log-stash index updates, DevModeAgentCli tests, Ollama executor scenarios) so coverage stays enforced.
