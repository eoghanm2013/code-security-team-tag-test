# Test Execution Guide: Code Security Team Tag Behavior

## Prerequisites

Before starting, you need:
- [ ] GitHub repository with Code Security enabled (can create a new one or use existing test repo)
- [ ] Repository must be connected to Datadog Code Security
- [ ] Access to Datadog UI to view signals

---

## Setup: Initialize Git Repository

```bash
cd /Users/eoghan.mellott/code-security-team-tag-test

# Initialize git
git init
git branch -M main

# Create GitHub repo (replace with your org/name)
# Option 1: Via GitHub CLI
gh repo create code-security-team-tag-test --public --source=. --remote=origin

# Option 2: Manually create repo on GitHub, then:
# git remote add origin https://github.com/YOUR_ORG/code-security-team-tag-test.git
```

---

## Phase 1: Initial Scan WITHOUT Team Tag

### Step 1.1: Commit vulnerable dependency (no team tag)

```bash
git add package.json index.js README.md
git commit -m "Phase 1: Add vulnerable lodash dependency (no team tag)"
git push -u origin main
```

### Step 1.2: Wait for Code Security scan

**Time**: ~5-15 minutes

**Monitor**: Check GitHub Actions or Datadog Code Security UI

### Step 1.3: Verify signal exists WITHOUT team tag

**Navigate to**: Datadog UI > Security > Code Security > Vulnerabilities

**Query**:
```
repository:github.com/YOUR_ORG/code-security-team-tag-test status:Open
```

**Expected Results**:
- ✅ See vulnerability signals for lodash CVEs
- ✅ Team facet shows **"No value"** or **blank**

**Record**:
- [ ] Signal ID: _________________
- [ ] Advisory: _________________
- [ ] Created timestamp: _________________
- [ ] Screenshot saved: ☐ Yes

**Query to confirm NO team tag**:
```
repository:github.com/YOUR_ORG/code-security-team-tag-test team:tee-team status:Open
```

**Expected**: **0 results** ✅

---

## Phase 2: Add Team Tag WITHOUT Rescan

### Step 2.1: Update entity.datadog.yaml template

```bash
# Edit the template with your actual repo URL
sed -i '' 's|YOUR_GITHUB_ORG|YOUR_ACTUAL_ORG|g' entity.datadog.yaml.template
sed -i '' 's|YOUR_REPO_NAME|code-security-team-tag-test|g' entity.datadog.yaml.template

# Rename to active file
mv entity.datadog.yaml.template entity.datadog.yaml

# Verify content
cat entity.datadog.yaml
```

### Step 2.2: Commit ONLY the team tag config

```bash
git add entity.datadog.yaml
git commit -m "Phase 2: Add team tag configuration (no code change)"
git push origin main
```

### Step 2.3: Wait for config to propagate

**Time**: ~5 minutes

### Step 2.4: Verify repository shows team tag

**Navigate to**: Datadog UI > Security > Code Security > Repositories > [Your Repo]

**Expected**: Repository page shows `team:tee-team` in metadata ✅

### Step 2.5: **CRITICAL CHECK**: Verify existing signal does NOT have team tag

**Query**:
```
repository:github.com/YOUR_ORG/code-security-team-tag-test team:tee-team status:Open
```

**Expected**: **STILL 0 results** ✅ (proves no backfill!)

**Check original signal**:
- Navigate to the signal from Phase 1 (use Signal ID you recorded)
- **Expected**: Team field is STILL empty/missing ✅

**Record**:
- [ ] Repository shows team tag: ☐ Yes
- [ ] Existing signal updated: ☐ No (expected)
- [ ] Query with team filter returns 0: ☐ Yes
- [ ] Screenshot of unchanged signal: ☐ Yes

---

## Phase 3: Trigger Rescan WITH Team Tag Present

### Step 3.1: Make trivial change to trigger rescan

```bash
# Add a test comment to trigger new scan
echo "" >> README.md
git add README.md
git commit -m "Phase 3: Trigger rescan with team tag configured"
git push origin main
```

### Step 3.2: Wait for scan to complete

**Time**: ~5-15 minutes

**Monitor**: Check for new scan completion in Datadog UI

### Step 3.3: Verify NEW signal has team tag

**Query**:
```
repository:github.com/YOUR_ORG/code-security-team-tag-test team:tee-team status:Open
```

**Expected**: **NOW RETURNS RESULTS!** ✅

**Expected Results**:
- ✅ Vulnerability signals NOW appear
- ✅ Team field shows `team:tee-team`
- ✅ Same CVEs/advisories, but NEW signal instances

**Record**:
- [ ] New signal ID: _________________
- [ ] Team tag present: ☐ Yes
- [ ] Query returns results: ☐ Yes
- [ ] Signal count increased: ☐ Yes
- [ ] Screenshot of new signal with team tag: ☐ Yes

### Step 3.4: Check status of old signal

**Query**:
```
repository:github.com/YOUR_ORG/code-security-team-tag-test status:ALL
```

**Possible outcomes**:
1. Old signal auto-closed/resolved
2. Old signal still present but superseded
3. Both signals present (old without team, new with team)

**Record which scenario occurred**: _________________

---

## Results Summary

### ✅ Test SUCCESS Criteria

- [ ] Phase 1: Signal created without team tag
- [ ] Phase 2: Adding team tag did NOT update existing signal (0 results with team filter)
- [ ] Phase 3: New scan created signal WITH team tag (results now appear with team filter)

### Proof Collected

- [ ] Screenshot: Phase 1 signal (no team tag)
- [ ] Screenshot: Phase 2 unchanged signal (still no team tag)
- [ ] Screenshot: Phase 3 new signal (has team tag)
- [ ] Datadog query results for all three phases

---

## Cleanup (Optional)

```bash
# Delete test repository
cd ..
rm -rf code-security-team-tag-test

# Delete GitHub repo (if desired)
gh repo delete YOUR_ORG/code-security-team-tag-test --yes
```

---

## Timeline

| Phase | Duration | Status |
|-------|----------|--------|
| Setup | ~5 min | ☐ |
| Phase 1 | ~20 min | ☐ |
| Phase 2 | ~10 min | ☐ |
| Phase 3 | ~20 min | ☐ |
| **Total** | **~55 min** | |

---

## Alternative: Use Customer's Repo (Safer)

If you don't want to create a new test repo, you can observe the customer's actual repository:

```bash
# Ask customer to push a trivial commit
# They run:
echo "" >> README.md
git commit -am "Trigger Code Security rescan"
git push origin main
```

Then monitor:
```
repository:github.com/sallinggroup-ai-and-ml/sallie team:team-ai status:Open
```

**Before rescan**: 0 results
**After rescan**: Should show results ✅

---

**Test Created**: 2026-02-18  
**Ticket**: SCRS-1947  
**Hypothesis**: Team tags NOT backfilled, only applied to new scans

