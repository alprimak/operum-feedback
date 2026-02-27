# Release Management

> Semantic versioning, CI-driven releases, and hotfix processes for AI-managed projects.

## Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{{REPO}}` | GitHub repository (owner/name) | `acme/my-app` |

## Template

---begin template---

## Release Management

### Semantic Versioning (SemVer)

Use semantic versioning for all releases: `MAJOR.MINOR.PATCH`

| Change Type | Version Bump | Example | When to Use |
|-------------|--------------|---------|-------------|
| Breaking changes | MAJOR | 1.0.0 → 2.0.0 | API changes, removed features |
| New features | MINOR | 1.0.0 → 1.1.0 | Backwards-compatible functionality |
| Bug fixes | PATCH | 1.0.0 → 1.0.1 | Backwards-compatible bug fixes |

**Pre-release versions:** `1.0.0-alpha.1`, `1.0.0-beta.1`, `1.0.0-rc.1`

### Golden Rule: Version BEFORE Tag

Always update the version in code files BEFORE creating a git tag. If you tag `v2.0.0` but `package.json` says `1.9.0`, artifacts will be misnamed.

**Common version files by ecosystem:**

| Ecosystem | File |
|-----------|------|
| Node.js | `package.json` |
| Rust | `Cargo.toml` |
| Python | `pyproject.toml` |
| Go | Tag-based (check for `version.go`) |
| .NET | `*.csproj` |

### Standard Release Process

```bash
# 1. Ensure main is up to date
git checkout main && git pull

# 2. Update version in ALL version files

# 3. Update CHANGELOG.md (if maintained)

# 4. Commit version bump
git add -A && git commit -m "chore(release): bump version to X.Y.Z"

# 5. Push to main
git push origin main

# 6. Create annotated tag
git tag -a vX.Y.Z -m "Release vX.Y.Z"

# 7. Push tag (triggers CI release workflow)
git push origin vX.Y.Z
```

### CI-Driven Releases

Let CI handle release creation when possible.

**If project has CI release workflow:**
- Do NOT use `gh release create` manually (conflicts with CI)
- Just push the tag — CI handles everything

**If NO CI workflow exists:**
- Manual `gh release create` is acceptable
- Consider adding automated releases

### Changelog Format

Follow [Keep a Changelog](https://keepachangelog.com/):

```markdown
## [Unreleased]

## [1.2.0] - 2024-01-15
### Added
- New feature X

### Fixed
- Bug in Z
```

**Categories:** Added, Changed, Deprecated, Removed, Fixed, Security

### Fixing a Failed Release

```bash
# Delete release and tag
gh release delete vX.Y.Z --yes
git tag -d vX.Y.Z && git push origin :refs/tags/vX.Y.Z

# Fix the issue, recommit, recreate tag
git tag -a vX.Y.Z -m "Release vX.Y.Z"
git push origin vX.Y.Z
```

### Hotfix Process

For urgent fixes to released versions:

```bash
git checkout -b hotfix/issue-{NUMBER} vX.Y.Z
# Make fix, bump patch version, tag
git tag -a vX.Y.(Z+1) -m "Hotfix: description"
git push origin vX.Y.(Z+1)
# Merge fix back to main
git checkout main && git merge hotfix/issue-{NUMBER}
```

### Release Checklist

- [ ] All tests passing on main
- [ ] Version bumped in all relevant files
- [ ] Versions consistent across files
- [ ] CHANGELOG updated
- [ ] No blocking bugs for this release
- [ ] Breaking changes documented

---end template---

## Customization Guide

- **Skip CI releases for small projects** — manual `gh release create` is fine when you're starting out. Add CI automation when releases become frequent.
- **Changelog is optional but recommended** — at minimum, write good release notes on GitHub Releases.
- **Trunk-based development** (release from `main`) is recommended for most projects. Git Flow adds complexity that's rarely worth it for small teams.
- **Let the PM agent own release coordination** — the PM decides when to cut a release, the Engineer executes the process.
