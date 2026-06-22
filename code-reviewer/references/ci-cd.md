## CI/CD Review Checklist

Applies to: `.github/workflows/*.yml`, `.gitlab-ci.yml`, `Dockerfile`, `docker-compose.yml`, Kubernetes manifests, Terraform/CloudFormation.

### Security 🔒
- [ ] 🔴 Hardcoded secrets or credentials — API keys, passwords, tokens in plain text; should use secrets manager or CI secret variables
- [ ] 🔴 Unpinned third-party actions — `uses: some-action@main` can be hijacked; pin to a commit SHA: `uses: some-action@abc1234`
- [ ] 🔴 Docker image running as root — no `USER` directive in Dockerfile; add a non-root user
- [ ] 🔴 Vulnerable or untagged base images — `FROM ubuntu:latest` pulls an unknown version; pin to a specific digest or tag
- [ ] 🟠 Unvetted third-party actions or orbs with broad permissions
- [ ] 🟠 Overly permissive CI job permissions — `permissions: write-all` when only `contents: read` is needed
- [ ] 🟠 Sensitive data exposed in logs — `echo $SECRET` or unmasked environment variables printed during steps
- [ ] 🟠 Missing image vulnerability scanning before push (e.g. Trivy, Snyk)

### Performance ⚡
- [ ] 🟠 Missing build/dependency caching — re-downloading `node_modules` or pip packages on every run
- [ ] 🟡 Sequential steps that could run in parallel — independent jobs not split across parallel runners
- [ ] 🟡 Oversized Docker layers — `COPY . .` before installing dependencies invalidates cache on every code change; copy dependency files first
- [ ] 🔵 Redundant build steps — building the same artifact twice across jobs without sharing it via artifacts/cache

### Reliability 🛡️
- [ ] 🔴 No rollback strategy on deployment failure
- [ ] 🟠 Missing health checks before marking a deployment successful — deploying without verifying the new version is actually serving traffic
- [ ] 🟠 Missing failure conditions — steps that can silently fail without failing the job (e.g. a script that exits 0 on error)
- [ ] 🟡 No timeout on long-running jobs — a hanging step will block the runner until the global timeout fires
- [ ] 🟡 Non-idempotent steps — running the pipeline twice produces different results or side-effects

### Tests 🧪
- [ ] 🟠 No test stage before deployment — code deployed to any environment without a passing test run gating it
- [ ] 🟠 Missing smoke tests post-deploy — no step that verifies the deployed service is actually healthy after rollout
- [ ] 🟡 Tests running in the deploy job instead of a separate stage — a test failure mid-deploy leaves the environment in an unknown state
- [ ] 🔵 No branch policy enforcing tests pass before merge — tests exist but aren't required status checks

### Best Practices 📋
- [ ] 🟠 Unpinned dependency versions — `pip install requests` with no version; use a lockfile or pin explicitly
- [ ] 🟡 Missing environment separation — the same job deploying to prod and staging with a boolean flag instead of separate jobs/environments
- [ ] 🔵 Step and job names that don't describe what they do — `- run: ./script.sh` with no `name:` field
