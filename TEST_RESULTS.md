# Test Results: Code Security Team Tag Backfill Behavior

**Test Date**: _______________  
**Tester**: Eoghan Mellott  
**Ticket**: SCRS-1947  
**Repository**: _______________

---

## Phase 1: Initial Scan WITHOUT Team Tag

**Commit SHA**: _______________  
**Timestamp**: _______________  

### Signals Detected

| Signal ID | Advisory | Severity | Team Tag | Status |
|-----------|----------|----------|----------|--------|
| | | | ☐ Present ☐ Missing | |
| | | | ☐ Present ☐ Missing | |

### Query Results

**Query**: `repository:<repo> status:Open`
- **Result Count**: _______________
- **Expected**: Should see vulnerabilities ✅

**Query**: `repository:<repo> team:tee-team status:Open`
- **Result Count**: _______________
- **Expected**: 0 results ✅

**Phase 1 Result**: ☐ Pass ☐ Fail

---

## Phase 2: Add Team Tag (No Rescan)

**Commit SHA**: _______________  
**Timestamp**: _______________  

### Repository Configuration Check

- **Repository page shows team tag**: ☐ Yes ☐ No
- **Team value displayed**: _______________

### Existing Signal Check

**Signal ID from Phase 1**: _______________

- **Team tag backfilled**: ☐ Yes ☐ No (Expected: No)
- **Team field value**: _______________

### Query Results

**Query**: `repository:<repo> team:tee-team status:Open`
- **Result Count**: _______________
- **Expected**: STILL 0 results ✅ (proves no backfill)

**Phase 2 Result**: ☐ Pass ☐ Fail

---

## Phase 3: Rescan WITH Team Tag

**Commit SHA**: _______________  
**Timestamp**: _______________  

### New Signals Detected

| Signal ID | Advisory | Severity | Team Tag | Status |
|-----------|----------|----------|----------|--------|
| | | | ☐ tee-team | |
| | | | ☐ tee-team | |

### Query Results

**Query**: `repository:<repo> team:tee-team status:Open`
- **Result Count**: _______________
- **Expected**: NOW returns results ✅

**Query**: `repository:<repo> status:ALL`
- **Total signals**: _______________
- **Signals with team tag**: _______________
- **Signals without team tag**: _______________

### Old Signal Status

**Signal ID from Phase 1**: _______________

- **Current status**: ☐ Open ☐ Closed ☐ Resolved ☐ Auto-closed
- **Relationship to new signal**: ☐ Superseded ☐ Duplicate ☐ Independent

**Phase 3 Result**: ☐ Pass ☐ Fail

---

## Final Verdict

### Hypothesis Validation

**Hypothesis**: Team tags are NOT backfilled retroactively on existing signals. Team tags only apply to newly generated signals from new scans.

**Result**: ☐ Confirmed ☐ Rejected

### Evidence

- ☐ Phase 1 signal had no team tag
- ☐ Phase 2 signal did NOT get team tag after config added (no backfill)
- ☐ Phase 3 new scan produced signals WITH team tag

### Conclusion

```
[Write your conclusion here based on test results]




```

---

## Screenshots

- [ ] Phase 1: Signal without team tag
- [ ] Phase 2: Repository with team tag, signal unchanged
- [ ] Phase 3: New signal with team tag
- [ ] Query results showing 0 → results progression

---

## Notes

```
[Any additional observations or anomalies]




```

---

## Next Steps

- [ ] Document findings in SCRS-1947
- [ ] Update customer response with concrete proof
- [ ] Archive test repository (or keep for future reference)

---

**Test Completed**: _______________  
**Duration**: _______________  
**Overall Result**: ☐ Success ☐ Failure ☐ Inconclusive

