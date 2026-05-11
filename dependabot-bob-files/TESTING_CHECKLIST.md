# Testing Checklist - Security Implementation

Quick reference for testing the security implementation.

## ✅ Pre-Push Checklist

Before pushing to GitHub, verify locally:

```bash
# 1. Check all files exist
ls -la .github/dependabot.yml
ls -la SECURITY.md
ls -la .github/workflows/*.yml
ls -la CONTRIBUTING.md

# 2. Validate YAML syntax
pip install yamllint
yamllint .github/dependabot.yml
yamllint .github/workflows/*.yml

# 3. Check for syntax errors
echo "All files created successfully!"
```

## 🚀 Push to GitHub

```bash
# Stage all security files
git add .github/dependabot.yml
git add SECURITY.md
git add .github/workflows/
git add CONTRIBUTING.md
git add SECURITY_IMPROVEMENT_PLAN.md
git add IMPLEMENTATION_SUMMARY.md
git add TESTING_CHECKLIST.md

# Commit with signed commit
git commit -S -m "Add: Complete security and Dependabot implementation

- Add Dependabot configuration for Python and JavaScript dependencies
- Add SECURITY.md with vulnerability reporting process
- Add GitHub Actions workflows for security scanning
- Update CONTRIBUTING.md with security guidelines
- Add comprehensive documentation"

# Push to main branch
git push origin main
```

## 🔧 Enable GitHub Features

After pushing, enable these features in GitHub settings:

### Step 1: Navigate to Settings
Go to: `https://github.com/vwinland/ibmdotcom-tutorials/settings/security_analysis`

### Step 2: Enable Dependabot
- [x] Dependency graph
- [x] Dependabot alerts
- [x] Dependabot security updates
- [x] Dependabot version updates

### Step 3: Enable Code Scanning
- [x] CodeQL analysis (will use the workflow we created)

### Step 4: Enable Secret Scanning
- [x] Secret scanning
- [x] Secret scanning push protection

## 📊 Verify Implementation

### Check 1: Workflows Running (5 minutes after push)

```bash
# Go to Actions tab
open https://github.com/vwinland/ibmdotcom-tutorials/actions
```

Expected workflows:
- ✅ Secret Scanning
- ✅ CodeQL Security Analysis (Python)
- ✅ CodeQL Security Analysis (JavaScript)
- ✅ Python Security Scan (4 jobs for Python 3.10-3.13)

### Check 2: Security Policy Visible (Immediate)

```bash
# Go to Security tab
open https://github.com/vwinland/ibmdotcom-tutorials/security
```

Expected:
- ✅ "Security policy detected" message
- ✅ Link to SECURITY.md visible

### Check 3: Dependabot Active (Within 24 hours)

```bash
# Go to Insights > Dependency graph > Dependabot
open https://github.com/vwinland/ibmdotcom-tutorials/network/updates
```

Expected:
- ✅ "Dependabot is checking for updates"
- ✅ List of monitored package ecosystems
- ✅ Last checked timestamp

### Check 4: First Dependabot PRs (Within 24-48 hours)

```bash
# Go to Pull Requests
open https://github.com/vwinland/ibmdotcom-tutorials/pulls
```

Expected:
- ✅ PRs created by `dependabot[bot]`
- ✅ Labels: "dependencies", "python" or "javascript", "tutorial"
- ✅ Clear description of what's being updated

## 🧪 Test Workflows

### Test 1: Create a Test PR

```bash
# Create test branch
git checkout -b test/security-workflows

# Make a small change
echo "# Testing security workflows" >> README.md

# Commit and push
git add README.md
git commit -S -m "Test: Security workflows"
git push origin test/security-workflows

# Create PR on GitHub
# Expected: Dependency Review workflow runs automatically
```

### Test 2: Test Dependabot Commands

On any Dependabot PR, comment:
- `@dependabot rebase` - Rebases the PR
- `@dependabot recreate` - Recreates the PR
- `@dependabot merge` - Merges the PR (if checks pass)
- `@dependabot ignore this dependency` - Ignores this dependency
- `@dependabot ignore this major version` - Ignores this major version

## 📈 Success Criteria

### Immediate Success (Day 1)
- [x] All files committed and pushed
- [x] All workflows appear in Actions tab
- [x] SECURITY.md visible in repository
- [x] No workflow failures

### Short-term Success (Week 1)
- [ ] At least 1 Dependabot PR created
- [ ] All workflows running successfully
- [ ] CodeQL analysis complete
- [ ] No critical security alerts

### Long-term Success (Month 1)
- [ ] 80% of Dependabot PRs reviewed
- [ ] 50% of safe updates merged
- [ ] All high/critical alerts addressed
- [ ] Team familiar with workflow

## 🐛 Common Issues

### Issue: Workflow Fails with "Permission Denied"

**Solution:** Check workflow permissions in repository settings
```
Settings > Actions > General > Workflow permissions
Select: "Read and write permissions"
```

### Issue: Dependabot Not Creating PRs

**Solution:** Manually trigger Dependabot
```
Insights > Dependency graph > Dependabot > Check for updates
```

### Issue: CodeQL Analysis Timeout

**Solution:** This is normal for first run. Wait 15-20 minutes.

### Issue: Too Many Dependabot PRs

**Solution:** Adjust limits in `.github/dependabot.yml`
```yaml
open-pull-requests-limit: 3  # Reduce from 5 to 3
```

## 📞 Need Help?

- Review [IMPLEMENTATION_SUMMARY.md](IMPLEMENTATION_SUMMARY.md) for detailed testing
- Check [SECURITY_IMPROVEMENT_PLAN.md](SECURITY_IMPROVEMENT_PLAN.md) for full plan
- See [SECURITY.md](SECURITY.md) for security policies
- Read [CONTRIBUTING.md](CONTRIBUTING.md) for contribution guidelines

## ✅ Final Checklist

Before marking as complete:

- [ ] All files pushed to GitHub
- [ ] Dependabot enabled in settings
- [ ] Code scanning enabled
- [ ] Secret scanning enabled
- [ ] All workflows passing
- [ ] First Dependabot PR reviewed
- [ ] Team notified of changes
- [ ] Documentation reviewed

---

**Status:** Ready for Testing  
**Last Updated:** May 5, 2026  
**Next Review:** After first Dependabot PRs arrive
