# Code Security Team Tag Test

This repository is used to test and validate the behavior of team tags in Datadog Code Security.

## Test Goal

Prove that team tags are **not backfilled retroactively** and only apply to newly generated signals.

## Vulnerable Dependency

This repo intentionally uses `lodash@4.17.20` which has known CVEs:
- CVE-2020-28500 (ReDoS)
- CVE-2021-23337 (Command Injection)

## Test Status

- [ ] Phase 1: Initial scan without team tag
- [ ] Phase 2: Add team tag (no rescan)
- [ ] Phase 3: Rescan with team tag present

## Related Ticket

SCRS-1947

