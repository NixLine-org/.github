# Workflow Security Guidelines

## GitHub Token Security

### Problem: Workflow Token Permissions

The `GITHUB_TOKEN` in workflows has powerful permissions that could be abused if a workflow is compromised.

### Mitigation Strategies

#### 1. Use Minimum Required Permissions

**Never use:**
```yaml
permissions: write-all  # NEVER DO THIS
```

**Always specify exact permissions:**
```yaml
permissions:
  contents: read        # Only what you need
  pull-requests: write  # Be specific
```

#### 2. Consider Fine-Grained PATs for Critical Operations

For highly sensitive operations (like org-wide policy sync), consider using fine-grained Personal Access Tokens instead of GITHUB_TOKEN:

```yaml
- name: Policy sync with PAT
  env:
    GH_TOKEN: ${{ secrets.POLICY_SYNC_PAT }}  # Fine-grained PAT
  run: |
    gh api ...
```

**PAT Configuration:**
- Repository access: Selected repositories only
- Permissions: Minimum required
- Expiration: 90 days maximum
- Rotation: Quarterly

#### 3. Workflow Isolation

**Separate workflows by trust level:**

```yaml
# High-trust workflow (can modify code)
name: Policy Sync
permissions:
  contents: write

# Low-trust workflow (read-only)
name: Validation
permissions:
  contents: read
```

#### 4. Fork PR Restrictions

**Always check PR source for auto-approval:**

```yaml
if: |
  github.event.pull_request.head.repo.full_name == github.event.pull_request.base.repo.full_name
```

This prevents fork PRs from being auto-approved.

#### 5. Rate Limiting Protection

**Implement backoff for API calls:**

```yaml
- name: API call with retry
  run: |
    for i in {1..3}; do
      if gh api ...; then
        break
      fi
      sleep $((i * 10))  # Exponential backoff
    done
```

## Workflow Compromise Detection

### Monitoring for Suspicious Activity

Watch for:
1. Unexpected workflow modifications
2. New workflow files added
3. Permission escalations
4. Unusual API call patterns
5. Failed authentication attempts

### Audit Commands

```bash
# Check recent workflow runs
gh run list --limit 20

# Check workflow file changes
git log -p -- .github/workflows/

# List repo collaborators
gh api repos/$OWNER/$REPO/collaborators

# Check branch protection
gh api repos/$OWNER/$REPO/branches/main/protection
```

## Incident Response

### If a Workflow is Compromised

1. **Immediately disable GitHub Actions:**
   ```bash
   gh api -X PUT repos/$OWNER/$REPO/actions/permissions \
     -f enabled=false
   ```

2. **Revoke all tokens:**
   - Rotate all PATs
   - Reset all secrets
   - Regenerate deploy keys

3. **Audit recent activity:**
   ```bash
   gh api repos/$OWNER/$REPO/events --paginate
   ```

4. **Review all recent PRs and commits:**
   ```bash
   git log --oneline --since="7 days ago"
   gh pr list --state all --limit 50
   ```

## Best Practices Checklist

- [ ] All workflows use minimum required permissions
- [ ] No `write-all` permissions anywhere
- [ ] Fork PRs cannot trigger dangerous workflows
- [ ] Auto-approve only works for same-repo PRs
- [ ] Workflows pinned to specific action versions
- [ ] Secrets rotated quarterly
- [ ] Branch protection enabled on main
- [ ] CODEOWNERS enforced for workflows
- [ ] Audit logs monitored
- [ ] Incident response plan documented

## Security Testing

Regularly test your workflows for security issues:

```bash
# Check for overly permissive workflows
grep -r "permissions:" .github/workflows/ | grep -v "#"

# Check for unpinned actions
grep -r "uses:" .github/workflows/ | grep -v "@[a-f0-9]\{40\}"

# Check for hardcoded secrets
grep -r "token\|secret\|password" .github/workflows/
```

## Resource Limits

Protect against resource exhaustion:

```yaml
jobs:
  sync:
    runs-on: ubuntu-latest
    timeout-minutes: 30  # Prevent infinite runs
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 1  # Shallow clone

      - name: Process with limits
        run: |
          ulimit -t 300  # CPU time limit
          ulimit -m 2097152  # Memory limit (2GB)
          # Your processing here
```

## Supply Chain Security

### Action Pinning

**Always pin to commit SHA:**
```yaml
# Good - pinned to specific commit
- uses: actions/checkout@8ade135a41bc03ea155e62e844d188df1ea18608 # v4.1.0

# Bad - can be moved
- uses: actions/checkout@v4
```

### Verify Action Sources

Before using an action:
1. Check the repository stars/watchers
2. Review recent commits
3. Check for security advisories
4. Verify the publisher identity

## Recommendations for Organizations

1. **Create a Security Team**
   - Review all workflow changes
   - Monitor for suspicious activity
   - Manage secrets and tokens
   - Respond to incidents

2. **Implement Defense in Depth**
   - Branch protection
   - CODEOWNERS
   - Required status checks
   - Signed commits
   - Audit logging

3. **Regular Security Reviews**
   - Monthly: Review workflow permissions
   - Quarterly: Rotate secrets
   - Annually: Full security audit

4. **Training**
   - Educate developers on workflow security
   - Document security procedures
   - Practice incident response

## Contact

Report security issues to: [YOUR-SECURITY-EMAIL]