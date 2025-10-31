# Security Policy

## Supported Versions

We actively maintain and provide security updates for the following versions:

| Version | Supported          |
| ------- | ------------------ |
| stable  | [+] Yes            |
| unstable| [~] Best effort    |
| < stable| [-] No             |

**stable**: Latest tagged stable release - fully supported with security patches
**unstable**: Development version - best effort support, use at your own risk
**older**: Previous stable versions - not supported, please upgrade

## Reporting a Vulnerability

**Do NOT create a public GitHub issue for security vulnerabilities.**

Instead, please report security vulnerabilities responsibly:

### For Critical Vulnerabilities

Email: [security@nixline.org](mailto:security@nixline.org)

Include:
- Description of the vulnerability
- Steps to reproduce
- Potential impact
- Suggested fix (if known)

### Response Timeline

- **Acknowledgment**: Within 24 hours
- **Initial Assessment**: Within 48 hours
- **Status Update**: Weekly until resolved
- **Fix Release**: Target 7-14 days for critical issues

### What We Consider Security Issues

**Critical (Immediate Response)**:
- Remote code execution vulnerabilities
- Privilege escalation in CI workflows
- Supply chain attacks (compromised dependencies)
- Secrets exposure or leakage
- Authentication bypass

**High Priority**:
- Denial of service vulnerabilities
- Information disclosure
- Cross-site scripting in generated files
- Unsafe defaults that expose sensitive data

**Medium Priority**:
- Dependency vulnerabilities with patches available
- Hardening opportunities
- Insecure configurations

### What We Don't Consider Security Issues

- Bug reports without security implications
- Feature requests
- Performance issues
- Compatibility problems
- Issues requiring physical access to user systems

## Security Best Practices for Users

### Baseline Repository Security

When forking NixLine for your organization:

1. **Repository Access Control**
   - Limit admin access to 2-3 security team members
   - Use teams for granular permissions
   - Never add outside collaborators to baseline repos

2. **Branch Protection**
   - Enable comprehensive CI validation (primary security control)
   - Require status checks for all branches
   - Allow automation bypass only for validated workflows
   - Use CODEOWNERS for human review gates

3. **Dependency Management**
   - Pin nixpkgs to specific commit hashes
   - Regularly update dependencies via automated workflows
   - Monitor for security advisories

4. **Secrets Management**
   - Never commit secrets to baseline repositories
   - Use GitHub repository secrets for CI tokens
   - Parameterize all organization-specific values

### Consumer Repository Security

1. **Direct Consumption Pattern**
   - Validate baseline source before consumption
   - Use specific tags (not `main` branch)
   - Monitor baseline repository for changes

2. **Template-Based Pattern**
   - Keep flake.lock updated
   - Review baseline updates before applying
   - Test policy changes in staging environments

## Security Architecture

### Trust Model

NixLine follows a hierarchical trust model:

```
Organization Security Team
    ↓
Baseline Repository (Critical Trust Point)
    ↓
Reusable Workflows (.github repo)
    ↓
Consumer Repositories
```

### Supply Chain Security

- **Nixpkgs Pinning**: All Nix dependencies pinned to specific commits
- **Action Pinning**: GitHub Actions pinned to specific SHA commits
- **Workflow Validation**: All reusable workflows validated before promotion
- **Audit Trail**: All changes tracked with commit signatures

### Incident Response

In case of a security incident:

1. **Immediate**: Disable affected workflows/repositories
2. **Assessment**: Determine scope and impact
3. **Communication**: Notify affected organizations within 24 hours
4. **Remediation**: Develop and test fix
5. **Deployment**: Coordinate fix rollout across organizations
6. **Post-Incident**: Conduct review and improve processes

## Responsible Disclosure

We are committed to working with security researchers and the community to verify and respond to legitimate security vulnerabilities. We will:

- Respond promptly to security reports
- Keep reporters informed about our progress
- Give credit to reporters (unless they prefer anonymity)
- Not pursue legal action against researchers following responsible disclosure

## Security Updates

Security updates are announced through:

- GitHub Security Advisories
- Release notes with `[SECURITY]` prefix
- Email notifications to baseline repository watchers
- Updates to this security policy

## Contact

- **Security Team**: [security@nixline.org](mailto:security@nixline.org)
- **General Questions**: [support@nixline.org](mailto:support@nixline.org)
- **Project Issues**: [GitHub Issues](https://github.com/NixLine-org/nixline-baseline/issues)

---

*This security policy applies to all repositories in the NixLine-org organization.*