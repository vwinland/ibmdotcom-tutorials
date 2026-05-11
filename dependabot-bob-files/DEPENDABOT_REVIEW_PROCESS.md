# Dependabot Review Process

## Overview

This repository uses an automated review process for Dependabot PRs to balance security with efficiency when the main repository requires two approving reviewers.

## Review Requirements by Update Type

### ✅ Auto-Approved (Patch & Minor Updates)

**Patch Updates** (`1.2.3` → `1.2.4`)
- **Reviews Required:** 1 (automated)
- **Action:** Auto-approved by workflow
- **Merge:** Manual after tests pass
- **Examples:** Bug fixes, security patches

**Minor Updates** (`1.2.0` → `1.3.0`)
- **Reviews Required:** 1 (automated)
- **Action:** Auto-approved by workflow
- **Merge:** Manual after tests pass
- **Examples:** New features, backward-compatible changes

### ⚠️ Manual Review Required (Major Updates)

**Major Updates** (`1.0.0` → `2.0.0`)
- **Reviews Required:** 2 (manual)
- **Action:** Requires human review
- **Merge:** After thorough testing
- **Examples:** Breaking changes, API changes

## How It Works

### Automated Workflow

The `.github/workflows/dependabot-auto-approve.yml` workflow:

1. **Triggers** on every Dependabot PR
2. **Analyzes** the update type (patch/minor/major)
3. **Auto-approves** patch and minor updates
4. **Labels** PRs by update type
5. **Comments** on major updates requiring manual review

### Review Process Flow

```
Dependabot creates PR
    ↓
Workflow analyzes update type
    ↓
┌─────────────────┬──────────────────┐
│ Patch/Minor     │ Major            │
│ Auto-approved   │ Needs review     │
│ 1 review ✅     │ 2 reviews needed │
└─────────────────┴──────────────────┘
    ↓                    ↓
Tests pass          Manual review
    ↓                    ↓
Manual merge        2 approvals → merge
```

## For Reviewers

### Quick Review Checklist

**For Auto-Approved PRs (Patch/Minor):**
- [ ] Check that tests pass
- [ ] Review changelog/release notes
- [ ] Verify no breaking changes mentioned
- [ ] Merge if everything looks good

**For Major Updates:**
- [ ] Read full changelog
- [ ] Check for breaking changes
- [ ] Test locally if possible
- [ ] Discuss with team if needed
- [ ] Get second approval
- [ ] Merge after thorough review

### Priority Levels

**🔴 Critical (Merge ASAP):**
- Security vulnerabilities (CVE fixes)
- Critical bug fixes
- Label: `security`, `critical`

**🟡 Normal (Merge This Week):**
- Patch updates
- Minor updates
- Regular dependency updates

**🟢 Low (Review When Convenient):**
- Major updates
- Non-critical dependencies
- Development dependencies

## Labels

Dependabot PRs are automatically labeled:

- `dependencies` - All Dependabot PRs
- `python` - Python package updates
- `javascript` - npm package updates
- `tutorial` - Tutorial-specific updates
- `dependabot-version-update:semver-patch` - Patch updates
- `dependabot-version-update:semver-minor` - Minor updates
- `dependabot-version-update:semver-major` - Major updates

## Batch Review Sessions

### Weekly Review (Recommended)

**Schedule:** Every Monday, 10:00 AM (30-60 minutes)

**Agenda:**
1. Review all open Dependabot PRs
2. Quick approve patch updates (already auto-approved)
3. Discuss minor updates if needed
4. Deep dive on major updates
5. Batch merge approved PRs

**Participants:** 2+ team members for major updates

## Dependabot Commands

You can interact with Dependabot by commenting on PRs:

```bash
@dependabot rebase          # Rebase the PR
@dependabot recreate        # Recreate the PR from scratch
@dependabot merge           # Merge after checks pass
@dependabot squash and merge # Squash and merge
@dependabot cancel merge    # Cancel auto-merge
@dependabot close           # Close the PR
@dependabot reopen          # Reopen a closed PR
@dependabot ignore this dependency        # Ignore all future updates
@dependabot ignore this major version     # Ignore this major version
@dependabot ignore this minor version     # Ignore this minor version
```

## Handling Two-Reviewer Requirement

### When Merging to Main Repository

If the main repository requires two approving reviewers:

**Option 1: Branch Protection Exception**
- Configure branch protection to require only 1 review for Dependabot PRs
- Keep 2 reviews for human-authored PRs

**Option 2: Dedicated Review Team**
- Create `@dependabot-reviewers` team
- Assign 3-4 members
- Rotate review duties

**Option 3: Batch Approval**
- Schedule weekly review sessions
- Two team members review together
- Approve multiple PRs in one session

## Troubleshooting

### PR Not Auto-Approved

**Check:**
1. Is it a patch or minor update? (Major updates need manual review)
2. Did the workflow run? (Check Actions tab)
3. Are permissions correct? (Workflow needs `pull-requests: write`)

### Too Many PRs

**Solutions:**
1. Reduce `open-pull-requests-limit` in `dependabot.yml`
2. Increase review frequency
3. Use `@dependabot ignore` for non-critical dependencies

### Tests Failing

**Actions:**
1. Review test failures
2. Check if update introduces breaking changes
3. Update code if needed
4. Comment `@dependabot recreate` to retry

## Security Considerations

### Why Auto-Approve?

**Benefits:**
- ✅ Faster security patch deployment
- ✅ Reduced review burden
- ✅ Maintains security posture
- ✅ Industry best practice

**Safeguards:**
- ✅ Only patch/minor updates auto-approved
- ✅ Tests must pass before merge
- ✅ Major updates require manual review
- ✅ All changes are transparent (PRs)

### Risk Assessment

**Low Risk (Auto-Approve):**
- Patch updates (bug fixes only)
- Security patches
- Dependencies with good test coverage

**Medium Risk (Quick Review):**
- Minor updates (new features)
- Non-critical dependencies
- Development dependencies

**High Risk (Full Review):**
- Major updates (breaking changes)
- Core production dependencies
- Database/security libraries

## Metrics & Monitoring

### Track These Numbers

**Weekly:**
- Open Dependabot PRs
- Pending updates
- Average merge time
- Failed tests

**Monthly:**
- Total updates merged
- Security vulnerabilities fixed
- Time saved vs. manual process

### Success Criteria

**Good:**
- ✅ <10 open Dependabot PRs
- ✅ <48 hour average merge time
- ✅ 0 critical vulnerabilities
- ✅ 90%+ dependencies up to date

**Needs Improvement:**
- ⚠️ >20 open Dependabot PRs
- ⚠️ >1 week average merge time
- ⚠️ Critical vulnerabilities present
- ⚠️ <70% dependencies up to date

## Resources

### Documentation
- [Dependabot Documentation](https://docs.github.com/en/code-security/dependabot)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Semantic Versioning](https://semver.org/)

### Internal Docs
- [SECURITY.md](SECURITY.md) - Security policy
- [CONTRIBUTING.md](CONTRIBUTING.md) - Contribution guidelines
- [EXECUTIVE_TECHNICAL_GUIDE.md](EXECUTIVE_TECHNICAL_GUIDE.md) - Technical overview

---

**Questions?** Open an issue or contact the security team.

**Last Updated:** May 7, 2026
