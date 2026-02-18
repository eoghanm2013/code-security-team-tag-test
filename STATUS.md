# Test Setup Status

## ✅ Test Environment Ready!

**Location**: `/Users/eoghan.mellott/code-security-team-tag-test`

---

## What's Ready

### Test Application
- ✅ `package.json` - Node.js app with vulnerable lodash@4.17.20
- ✅ `index.js` - Simple test file
- ✅ `README.md` - Test documentation
- ✅ `.git/` - Git repository initialized

### Test Configuration
- ✅ `entity.datadog.yaml.template` - Team tag config (ready for Phase 2)

### Execution Guides
- ✅ `QUICK_START.md` - **START HERE** - Copy/paste commands
- ✅ `TEST_EXECUTION_GUIDE.md` - Detailed step-by-step instructions
- ✅ `TEST_RESULTS.md` - Results tracking template

---

## What You Need to Do

### 1. Create GitHub Repository

**Option A: Using GitHub CLI**
```bash
cd /Users/eoghan.mellott/code-security-team-tag-test
gh repo create code-security-team-tag-test --public --source=. --remote=origin
```

**Option B: Manual**
1. Go to https://github.com/new
2. Create repo: `code-security-team-tag-test`
3. Then run:
```bash
cd /Users/eoghan.mellott/code-security-team-tag-test
git remote add origin https://github.com/YOUR_ORG/code-security-team-tag-test.git
```

### 2. Enable Code Security

1. Connect repo to Datadog Code Security
2. Go to: Security > Code Security > Settings
3. Enable scanning for your new repository

### 3. Execute Test

Open `QUICK_START.md` and follow the 3 phases!

---

## Expected Timeline

| Phase | Time | What Happens |
|-------|------|--------------|
| Phase 1 | 20 min | Scan without team tag |
| Phase 2 | 10 min | Add team tag (no backfill) |
| Phase 3 | 20 min | Rescan with team tag |
| **Total** | **~50 min** | |

---

## Success Criteria

The test proves the hypothesis if:

1. ✅ Phase 1: Vulnerabilities detected WITHOUT team tag
2. ✅ Phase 2: Team tag added but signals UNCHANGED
3. ✅ Phase 3: New scan creates signals WITH team tag

---

## Quick Commands Reference

**Phase 1**:
```bash
git add package.json index.js README.md
git commit -m "Phase 1: Add vulnerable lodash (no team tag)"
git push -u origin main
```

**Phase 2**:
```bash
# Edit entity.datadog.yaml.template first!
git add entity.datadog.yaml
git commit -m "Phase 2: Add team tag config"
git push origin main
```

**Phase 3**:
```bash
echo "" >> README.md
git add README.md
git commit -m "Phase 3: Trigger rescan"
git push origin main
```

---

## Alternative: Quick Test with Customer Repo

Instead of creating a new repo, you can test with the customer's actual repository:

1. Ask them to push: `echo "" >> README.md; git commit -am "Rescan"; git push`
2. Wait 15 minutes
3. Check: `repository:github.com/sallinggroup-ai-and-ml/sallie team:team-ai status:Open`
4. Should change from 0 results → has results ✅

---

**Status**: Ready to execute  
**Next Step**: Open `QUICK_START.md`  
**Ticket**: SCRS-1947

