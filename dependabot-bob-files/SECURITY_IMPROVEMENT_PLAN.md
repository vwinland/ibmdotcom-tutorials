# Security Improvement Plan for ibmdotcom-tutorials

**Repository:** https://github.com/vwinland/ibmdotcom-tutorials  
**Analysis Date:** May 4, 2026  
**Status:** Recommendations for Implementation

---

## Executive Summary

This repository currently lacks automated dependency management and security monitoring. With 60+ tutorials containing Python, JavaScript, and other dependencies across multiple `requirements.txt`, `pyproject.toml`, and `package.json` files, implementing Dependabot and security scanning is critical for maintaining tutorial quality and security.

---

## Current Security Posture

### ✅ Existing Security Measures

1. **Secret Scanning Baseline**
   - File: `.secrets.baseline`
   - Tool: detect-secrets (v1.5.0)
   - Configured plugins: AWS, Azure, GitHub tokens, IBM Cloud, JWT, private keys, etc.
   - Status: Active and properly configured

2. **Developer Certificate of Origin (DCO)**
   - File: `.github/dco.yml`
   - Purpose: Ensures contributors sign off on commits
   - Status: Active

3. **Git Ignore Configuration**
   - File: `.gitignore`
   - Excludes: `.env`, `.venv`, `__pycache__`, credentials, build artifacts
   - Status: Properly configured

### ❌ Missing Security Components

1. **No Dependabot Configuration**
   - No `.github/dependabot.yml` file
   - No automated dependency updates
   - No security vulnerability alerts for dependencies

2. **No Security Policy**
   - No `SECURITY.md` file
   - No vulnerability disclosure process
   - GitHub Security tab shows: "No security policy detected"

3. **No GitHub Actions Workflows**
   - No CI/CD pipelines
   - No automated security scanning
   - No dependency review on pull requests

4. **No Published Security Advisories**
   - GitHub Security tab shows: "There aren't any published security advisories"

---

## Dependency Landscape Analysis

### Python Dependencies

**Requirements.txt Files (20+ locations):**
- `tutorials/03-multi-agent-systems/a2a_tutorial/beeai-a2a-client/requirements.txt`
- `tutorials/03-multi-agent-systems/a2a_tutorial/beeai-a2a-server/requirements.txt`
- `tutorials/03-multi-agent-systems/bee-ai-multi-agent-contract-management/requirements.txt`
- `tutorials/03-multi-agent-systems/chatdev_watsonx_tutorial_/requirements.txt`
- `tutorials/ai-agent-security/requirements.txt`
- And many more across tutorial directories

**Pyproject.toml Files (Modern Python Packaging):**
- `tutorials/03-multi-agent-systems/acp_tutorial/pyproject.toml`
- `tutorials/03-multi-agent-systems/multiagent-collab-cs-call-center-analysis/pyproject.toml`
- `tutorials/03-multi-agent-systems/my_retail_advisor/pyproject.toml`
- `tutorials/03-multi-agent-systems/acp_tutorial/crewai_agent_server/pyproject.toml`

**Key Dependencies Identified:**
- `beeai-framework` (>=0.1.36,<0.2.0)
- `pydantic` (>=2.10,<3.0.0)
- `crewai[tools]` (>=0.80.0,<1.0.0)
- `acp-sdk` (==1.0.1)
- Various AI/ML libraries across tutorials

### JavaScript/Node.js Dependencies

**Package.json Files:**
- `tutorials/13-full-stack-applications/silly_story_time/package.json`
- Full-stack applications with React, Chakra UI, Emotion, and other frontend dependencies

### Special Considerations

**ChatDev Tutorial:**
- Uses exact version pins in `requirements.txt`
- Documented reason: Compatibility requirements
- Location: `tutorials/03-multi-agent-systems/chatdev_watsonx_tutorial_/`

---

## Recommended Implementation Plan

### Phase 1: Dependabot Configuration (Priority: HIGH)

**Objective:** Enable automated dependency updates and security alerts

**Action Items:**

1. **Create `.github/dependabot.yml`**

```yaml
version: 2
updates:
  # Python pip dependencies (requirements.txt)
  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "weekly"
      day: "monday"
    open-pull-requests-limit: 5
    groups:
      python-dependencies:
        patterns:
          - "*"
    labels:
      - "dependencies"
      - "python"
    reviewers:
      - "vwinland"

  # Python pip dependencies in tutorials
  - package-ecosystem: "pip"
    directory: "/tutorials/03-multi-agent-systems/a2a_tutorial/beeai-a2a-client"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 3
    labels:
      - "dependencies"
      - "python"
      - "tutorial"

  - package-ecosystem: "pip"
    directory: "/tutorials/03-multi-agent-systems/a2a_tutorial/beeai-a2a-server"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 3
    labels:
      - "dependencies"
      - "python"
      - "tutorial"

  - package-ecosystem: "pip"
    directory: "/tutorials/03-multi-agent-systems/bee-ai-multi-agent-contract-management"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 3
    labels:
      - "dependencies"
      - "python"
      - "tutorial"

  - package-ecosystem: "pip"
    directory: "/tutorials/ai-agent-security"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 3
    labels:
      - "dependencies"
      - "python"
      - "tutorial"

  # Python projects with pyproject.toml
  - package-ecosystem: "pip"
    directory: "/tutorials/03-multi-agent-systems/acp_tutorial"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 3
    labels:
      - "dependencies"
      - "python"
      - "tutorial"

  - package-ecosystem: "pip"
    directory: "/tutorials/03-multi-agent-systems/multiagent-collab-cs-call-center-analysis"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 3
    labels:
      - "dependencies"
      - "python"
      - "tutorial"

  - package-ecosystem: "pip"
    directory: "/tutorials/03-multi-agent-systems/my_retail_advisor"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 3
    labels:
      - "dependencies"
      - "python"
      - "tutorial"

  - package-ecosystem: "pip"
    directory: "/tutorials/03-multi-agent-systems/acp_tutorial/crewai_agent_server"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 3
    labels:
      - "dependencies"
      - "python"
      - "tutorial"

  # JavaScript/npm dependencies
  - package-ecosystem: "npm"
    directory: "/tutorials/13-full-stack-applications/silly_story_time"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 5
    groups:
      npm-dependencies:
        patterns:
          - "*"
    labels:
      - "dependencies"
      - "javascript"
      - "tutorial"

  # GitHub Actions (when added)
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
    labels:
      - "dependencies"
      - "github-actions"
```

**Configuration Notes:**
- Weekly schedule balances security with maintenance overhead
- Grouped updates reduce PR noise
- Separate configurations per tutorial directory for targeted updates
- PR limits prevent overwhelming maintainers
- Labels help with triage and automation

**Special Handling for ChatDev:**
- Consider excluding or using `ignore` directive for exact version pins
- Add comment in dependabot.yml explaining why certain dependencies are pinned

2. **Enable Dependabot in GitHub Settings**

Navigate to: `https://github.com/vwinland/ibmdotcom-tutorials/settings/security_analysis`

Enable:
- ✅ Dependabot alerts
- ✅ Dependabot security updates
- ✅ Dependabot version updates
- ✅ Dependency graph

### Phase 2: Security Policy (Priority: HIGH)

**Objective:** Establish clear vulnerability reporting process

**Action Items:**

1. **Create `SECURITY.md` in repository root**

```markdown
# Security Policy

## Supported Versions

We actively maintain and provide security updates for the following:

| Version | Supported          |
| ------- | ------------------ |
| Latest  | :white_check_mark: |
| < Latest| :x:                |

**Note:** This repository contains educational tutorials. Each tutorial may use different versions of dependencies. We recommend always using the latest version of tutorial code.

## Reporting a Vulnerability

We take the security of our tutorials and code examples seriously. If you discover a security vulnerability, please follow these steps:

### 1. **Do Not** Open a Public Issue

Please do not report security vulnerabilities through public GitHub issues.

### 2. Report Privately

Send a detailed report to: **[your-security-email@example.com]**

Include:
- Description of the vulnerability
- Steps to reproduce
- Affected tutorial(s) or file(s)
- Potential impact
- Suggested fix (if available)

### 3. Response Timeline

- **Initial Response:** Within 48 hours
- **Status Update:** Within 5 business days
- **Fix Timeline:** Varies by severity
  - Critical: 7 days
  - High: 14 days
  - Medium: 30 days
  - Low: 90 days

### 4. Disclosure Policy

- We will acknowledge your report within 48 hours
- We will provide regular updates on our progress
- We will notify you when the vulnerability is fixed
- We will credit you in the security advisory (unless you prefer to remain anonymous)

## Security Best Practices for Tutorial Users

When using these tutorials:

1. **Keep Dependencies Updated**
   - Regularly update Python packages: `pip install --upgrade -r requirements.txt`
   - Update Node.js packages: `npm update`

2. **Use Virtual Environments**
   - Python: Use `venv` or `conda` environments
   - Node.js: Use project-specific `node_modules`

3. **Protect Credentials**
   - Never commit API keys or credentials
   - Use `.env` files (already in `.gitignore`)
   - Follow tutorial-specific security guidance

4. **Review Dependencies**
   - Check for known vulnerabilities before installation
   - Use tools like `pip-audit` or `npm audit`

5. **Production Deployments**
   - These are educational tutorials, not production-ready code
   - Perform security review before production use
   - Add appropriate authentication, authorization, and input validation

## Security Features in This Repository

- **Secret Scanning:** Configured via `.secrets.baseline`
- **Dependabot:** Automated dependency updates (when enabled)
- **DCO:** Developer Certificate of Origin for contributions

## Contact

For security concerns: **[your-security-email@example.com]**  
For general questions: Open a GitHub Discussion

---

**Last Updated:** May 2026
```

2. **Update Repository Settings**
   - Add security email to GitHub repository settings
   - Enable private vulnerability reporting

### Phase 3: GitHub Actions Workflows (Priority: MEDIUM)

**Objective:** Automate security scanning and testing

**Action Items:**

1. **Create `.github/workflows/dependency-review.yml`**

```yaml
name: Dependency Review

on:
  pull_request:
    branches: [ main ]

permissions:
  contents: read
  pull-requests: write

jobs:
  dependency-review:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Dependency Review
        uses: actions/dependency-review-action@v4
        with:
          fail-on-severity: moderate
          comment-summary-in-pr: always
```

2. **Create `.github/workflows/codeql-analysis.yml`**

```yaml
name: CodeQL Security Analysis

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '0 0 * * 1'  # Weekly on Monday

permissions:
  actions: read
  contents: read
  security-events: write

jobs:
  analyze:
    name: Analyze
    runs-on: ubuntu-latest

    strategy:
      fail-fast: false
      matrix:
        language: [ 'python', 'javascript' ]

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Initialize CodeQL
        uses: github/codeql-action/init@v3
        with:
          languages: ${{ matrix.language }}

      - name: Autobuild
        uses: github/codeql-action/autobuild@v3

      - name: Perform CodeQL Analysis
        uses: github/codeql-action/analyze@v3
```

3. **Create `.github/workflows/python-security.yml`**

```yaml
name: Python Security Scan

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '0 0 * * 1'  # Weekly

jobs:
  security-scan:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ['3.10', '3.11', '3.12', '3.13']

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}

      - name: Install security tools
        run: |
          pip install pip-audit safety bandit

      - name: Run pip-audit
        run: |
          find . -name "requirements.txt" -type f | while read req; do
            echo "Auditing $req"
            pip-audit -r "$req" || true
          done

      - name: Run Safety check
        run: |
          find . -name "requirements.txt" -type f | while read req; do
            echo "Safety check for $req"
            safety check -r "$req" || true
          done

      - name: Run Bandit security linter
        run: |
          bandit -r tutorials/ -ll -f json -o bandit-report.json || true

      - name: Upload Bandit results
        uses: actions/upload-artifact@v4
        if: always()
        with:
          name: bandit-results-${{ matrix.python-version }}
          path: bandit-report.json
```

4. **Create `.github/workflows/secret-scan.yml`**

```yaml
name: Secret Scanning

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  secret-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install detect-secrets
        run: pip install detect-secrets

      - name: Run detect-secrets scan
        run: |
          detect-secrets scan --baseline .secrets.baseline

      - name: Verify baseline
        run: |
          detect-secrets audit .secrets.baseline
```

### Phase 4: Repository Settings Configuration (Priority: MEDIUM)

**Objective:** Enable GitHub's built-in security features

**Action Items:**

1. **Navigate to Repository Settings**
   - URL: `https://github.com/vwinland/ibmdotcom-tutorials/settings`

2. **Security & Analysis Section**
   - Enable: Dependency graph
   - Enable: Dependabot alerts
   - Enable: Dependabot security updates
   - Enable: Dependabot version updates
   - Enable: Code scanning (CodeQL)
   - Enable: Secret scanning
   - Enable: Secret scanning push protection

3. **Branch Protection Rules**
   - Require pull request reviews
   - Require status checks (when workflows are added)
   - Require conversation resolution
   - Include administrators

4. **Vulnerability Reporting**
   - Enable private vulnerability reporting
   - Add security contact email

### Phase 5: Documentation Updates (Priority: LOW)

**Objective:** Document security practices for contributors

**Action Items:**

1. **Update `CONTRIBUTING.md`**

Add section on dependency management:

```markdown
## Dependency Management

### Adding Dependencies

When adding new dependencies to tutorials:

1. **Use Version Ranges** (unless exact version required)
   - Good: `package>=1.0.0,<2.0.0`
   - Avoid: `package==1.0.0` (unless necessary)

2. **Document Version Requirements**
   - If exact versions needed, explain why in comments
   - Example: ChatDev tutorial requires specific versions for compatibility

3. **Test with Multiple Python Versions**
   - Test with Python 3.10, 3.11, 3.12, 3.13
   - Use `requires-python` in pyproject.toml

4. **Security Considerations**
   - Run `pip-audit` before committing
   - Check for known vulnerabilities
   - Update dependencies regularly

### Reviewing Dependabot PRs

When Dependabot creates a PR:

1. **Review the changelog** for breaking changes
2. **Test the tutorial** with updated dependencies
3. **Check for compatibility** with tutorial code
4. **Merge promptly** if tests pass
5. **Document issues** if updates break functionality

### Dependency Update Schedule

- **Security updates:** Immediate review and merge
- **Version updates:** Weekly review on Mondays
- **Major version updates:** Careful review and testing required
```

2. **Update `README.md`**

Add security badge and information:

```markdown
## Security

[![Security Policy](https://img.shields.io/badge/security-policy-blue)](SECURITY.md)

We take security seriously. See our [Security Policy](SECURITY.md) for:
- Vulnerability reporting process
- Supported versions
- Security best practices

### Keeping Dependencies Updated

This repository uses Dependabot for automated dependency updates. Dependencies are reviewed weekly.

To check for vulnerabilities in your local environment:

```bash
# Python
pip install pip-audit
pip-audit -r requirements.txt

# Node.js
npm audit
```
```

---

## Implementation Timeline

### Week 1: Critical Security Setup
- [ ] Create and merge `.github/dependabot.yml`
- [ ] Create and merge `SECURITY.md`
- [ ] Enable Dependabot in repository settings
- [ ] Enable secret scanning and push protection

### Week 2: Automated Scanning
- [ ] Create and test GitHub Actions workflows
- [ ] Enable CodeQL scanning
- [ ] Configure branch protection rules
- [ ] Set up security contact email

### Week 3: Documentation & Process
- [ ] Update `CONTRIBUTING.md` with dependency guidelines
- [ ] Update `README.md` with security information
- [ ] Create internal process for reviewing Dependabot PRs
- [ ] Train team on security workflow

### Week 4: Monitoring & Refinement
- [ ] Review first week of Dependabot PRs
- [ ] Adjust PR limits and schedules as needed
- [ ] Address any security alerts
- [ ] Document lessons learned

---

## Ongoing Maintenance

### Weekly Tasks
- Review and merge Dependabot PRs
- Check security alerts dashboard
- Monitor GitHub Actions workflow results

### Monthly Tasks
- Review security policy effectiveness
- Update dependency management guidelines
- Audit tutorial dependencies for outdated packages

### Quarterly Tasks
- Security audit of all tutorials
- Review and update security workflows
- Update security documentation
- Team security training

---

## Success Metrics

### Key Performance Indicators

1. **Dependency Freshness**
   - Target: 90% of dependencies within 2 minor versions of latest
   - Measure: GitHub dependency graph

2. **Security Alert Response Time**
   - Target: Critical alerts addressed within 7 days
   - Measure: GitHub security alerts dashboard

3. **Dependabot PR Merge Rate**
   - Target: 80% of non-breaking PRs merged within 14 days
   - Measure: GitHub PR metrics

4. **Tutorial Functionality**
   - Target: 100% of tutorials working after dependency updates
   - Measure: Manual testing and user feedback

---

## Risk Assessment

### High Risk Areas

1. **ChatDev Tutorial**
   - Uses exact version pins
   - May break with dependency updates
   - Mitigation: Exclude from Dependabot or use ignore directives

2. **Multiple Dependency Files**
   - 20+ separate requirements.txt files
   - Potential for inconsistency
   - Mitigation: Grouped Dependabot updates, clear documentation

3. **Tutorial Breakage**
   - Dependency updates may break tutorial code
   - Users may encounter issues
   - Mitigation: Test before merging, clear versioning in tutorials

### Medium Risk Areas

1. **Maintenance Overhead**
   - Weekly Dependabot PRs require review
   - Mitigation: Grouped updates, clear review process

2. **False Positives**
   - Security scanners may flag non-issues
   - Mitigation: Tune scanner configurations, document exceptions

---

## Additional Recommendations

### Consider for Future Implementation

1. **Automated Testing**
   - Add pytest for Python tutorials
   - Add Jest for JavaScript tutorials
   - Run tests on Dependabot PRs

2. **Dependency Consolidation**
   - Consider shared requirements file for common dependencies
   - Use dependency groups in pyproject.toml

3. **Version Pinning Strategy**
   - Document when to use exact pins vs ranges
   - Create tutorial template with best practices

4. **Security Training**
   - Regular security awareness for contributors
   - Document common security pitfalls in tutorials

5. **Automated Dependency Updates**
   - Consider auto-merge for patch updates
   - Use GitHub Actions to auto-approve low-risk updates

---

## Resources

### GitHub Documentation
- [Dependabot Configuration](https://docs.github.com/en/code-security/dependabot/dependabot-version-updates/configuration-options-for-the-dependabot.yml-file)
- [Security Advisories](https://docs.github.com/en/code-security/security-advisories)
- [CodeQL](https://docs.github.com/en/code-security/code-scanning/automatically-scanning-your-code-for-vulnerabilities-and-errors/about-code-scanning-with-codeql)

### Security Tools
- [pip-audit](https://github.com/pypa/pip-audit) - Python dependency auditing
- [Safety](https://github.com/pyupio/safety) - Python dependency security checker
- [Bandit](https://github.com/PyCQA/bandit) - Python security linter
- [npm audit](https://docs.npmjs.com/cli/v8/commands/npm-audit) - Node.js security auditing

### Best Practices
- [OWASP Dependency Check](https://owasp.org/www-project-dependency-check/)
- [Python Security Best Practices](https://python.readthedocs.io/en/stable/library/security_warnings.html)
- [Node.js Security Best Practices](https://nodejs.org/en/docs/guides/security/)

---

## Conclusion

Implementing this security improvement plan will:

1. **Automate dependency updates** via Dependabot
2. **Establish clear security processes** via SECURITY.md
3. **Enable continuous security monitoring** via GitHub Actions
4. **Reduce security vulnerabilities** in tutorial code
5. **Improve maintainability** of 60+ tutorials

**Estimated Implementation Time:** 4 weeks  
**Ongoing Maintenance:** 2-4 hours per week  
**Risk Level:** Low (primarily additive changes)

**Next Steps:**
1. Review and approve this plan
2. Begin Phase 1 implementation (Dependabot)
3. Schedule weekly security review meetings
4. Assign security champion for the repository

---

**Document Version:** 1.0  
**Last Updated:** May 4, 2026  
**Author:** Security Analysis Team  
**Status:** Ready for Implementation
