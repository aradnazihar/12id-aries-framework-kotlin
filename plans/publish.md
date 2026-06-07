# Publish 12id-aries-framework-kotlin to GitHub Packages

## Context
This is a fork of `hyperledger/aries-framework-kotlin` with custom changes. The publishing config currently points to the upstream Hyperledger repo. We need to re-point everything to publish under `io.github.nazihar` on the `aradnazihar/12id-aries-framework-kotlin` GitHub Packages registry.

## Changes Required

### 1. Update `ariesframework/build.gradle` (publishing config)
**File:** `ariesframework/build.gradle`

- Change `groupId` from `org.hyperledger` → `io.github.nazihar`
- Change `artifactId` from `aries-framework-kotlin` → `12id-aries-framework-kotlin` (or keep as-is if preferred)
- Change GitHub Packages URL from `https://maven.pkg.github.com/hyperledger/aries-framework-kotlin` → `https://maven.pkg.github.com/aradnazihar/12id-aries-framework-kotlin`
- Change default version from `2.4.1-fix` → `2.4.1-1`

### 2. Update `.github/workflows/release.yml` (CI publishing)
**File:** `.github/workflows/release.yml`

- No structural changes needed — the workflow already uses `GITHUB_TOKEN` (auto-provided) and `VERSION` from git tag. It will work as-is with the updated `build.gradle`.
- Optionally add `permissions: packages: write` for clarity (GitHub Actions needs this for GitHub Packages).

### 3. Update `settings.gradle` (project name, optional)
**File:** `settings.gradle`

- Change `rootProject.name` from `aries-framework-kotlin` → `12id-aries-framework-kotlin`

## How Consumers Will Use It

After publishing, consumers add to their `settings.gradle`:
```groovy
maven {
    url "https://maven.pkg.github.com/aradnazihar/12id-aries-framework-kotlin"
    credentials {
        username = "GITHUB_USERNAME"
        password = "GITHUB_TOKEN"  // needs read:packages scope
    }
}
```

And in their `build.gradle`:
```groovy
implementation 'io.github.nazihar:12id-aries-framework-kotlin:2.4.1-1'
```

> **Note:** GitHub Packages requires authentication even for reading public packages. Consumers need a GitHub PAT with `read:packages` scope.

## How to Publish

### First time (manual):
1. Create a GitHub Personal Access Token (PAT) with `write:packages` scope
2. Set in `local.properties`:
   ```
   githubUsername=aradnazihar
   githubToken=ghp_YOUR_TOKEN
   ```
3. Run: `./gradlew publishAllPublicationsToGithubRepository`

### Via CI (automated, recommended):
1. Push a git tag: `git tag v2.4.1-1 && git push origin v2.4.1-1`
2. The `release.yml` workflow triggers automatically, builds & publishes

## Verification
1. After publishing, visit `https://github.com/aradnazihar/12id-aries-framework-kotlin/packages` to see the package
2. Test consuming it from another project using the dependency coordinates above
