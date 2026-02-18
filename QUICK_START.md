# Quick Start: Execute Team Tag Test

## TL;DR - Copy/Paste Commands

### Step 0: Setup (One-time)
```bash
cd /Users/eoghan.mellott/code-security-team-tag-test

# Create GitHub repo (choose ONE option):
# Option A: GitHub CLI
gh repo create code-security-team-tag-test --public --source=. --remote=origin

# Option B: Manually create on GitHub, then:
# git remote add origin https://github.com/YOUR_ORG/code-security-team-tag-test.git

# Enable Code Security on this repo in Datadog UI
```

---

## Phase 1: Baseline (No Team Tag) → ~20 min

```bash
cd /Users/eoghan.mellott/code-security-team-tag-test
git add package.json index.js README.md
git commit -m "Phase 1: Add vulnerable lodash (no team tag)"
git push -u origin main
```

**Then wait 10-15 min and check Datadog**:
- Go to: Security > Code Security > Vulnerabilities
- Query: `repository:github.com/YOUR_ORG/code-security-team-tag-test status:Open`
- ✅ Should see lodash vulnerabilities
- ✅ Team facet shows "No value"

---

## Phase 2: Add Team Tag → ~10 min

```bash
cd /Users/eoghan.mellott/code-security-team-tag-test

# Update template with your repo
GITHUB_ORG="YOUR_ORG_HERE"  # Replace with your actual org
sed -i '' "s|YOUR_GITHUB_ORG|$GITHUB_ORG|g" entity.datadog.yaml.template
sed -i '' 's|YOUR_REPO_NAME|code-security-team-tag-test|g' entity.datadog.yaml.template
mv entity.datadog.yaml.template entity.datadog.yaml

# Commit team tag config
git add entity.datadog.yaml
git commit -m "Phase 2: Add team tag config (no rescan)"
git push origin main
```

**Wait 5 min, then check Datadog**:
- Go to: Security > Code Security > Repositories > [your repo]
- ✅ Repository should show `team:tee-team`

**CRITICAL: Check if old signal updated**:
- Query: `repository:github.com/YOUR_ORG/code-security-team-tag-test team:tee-team status:Open`
- ✅ Should STILL return 0 results (proves no backfill!)

---

## Phase 3: Trigger Rescan → ~20 min

```bash
cd /Users/eoghan.mellott/code-security-team-tag-test

# Trigger rescan with trivial change
echo "" >> README.md
git add README.md
git commit -m "Phase 3: Trigger rescan with team tag"
git push origin main
```

**Wait 10-15 min, then check Datadog**:
- Query: `repository:github.com/YOUR_ORG/code-security-team-tag-test team:tee-team status:Open`
- ✅ Should NOW return results! (proves forward application)

---

## Success = All Three Checkpoints Pass

1. ✅ Phase 1: Signal without team tag
2. ✅ Phase 2: Team tag added, signal UNCHANGED (0 results)
3. ✅ Phase 3: New scan, signal WITH team tag (results appear)

---

## Cleanup

```bash
cd /Users/eoghan.mellott
rm -rf code-security-team-tag-test

# Delete GitHub repo
gh repo delete YOUR_ORG/code-security-team-tag-test --yes
```

---

**Total Time**: ~50 minutes  
**Files to Track**: TEST_RESULTS.md

