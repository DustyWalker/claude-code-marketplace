---
description: Safe, staged deployment with quality gates and rollback capability
allowed-tools: Bash(*), Read(*), Write(*)
---

Execute a safe, staged deployment to $1 environment using the deployment-engineer agent.

Pre-deployment checklist:
1. Verify git status is clean
2. Run full test suite
3. Run security scan (npm audit)
4. Build production artifacts
5. Verify environment variables are set

Deployment steps:
1. Deploy to staging first (if not already staging)
2. Run smoke tests
3. Monitor for 5 minutes
4. If successful, ask for production approval
5. Deploy to production with blue-green strategy
6. Monitor error rates and latency
7. Generate deployment report

IMPORTANT: Request explicit confirmation before deploying to production.

Rollback plan: If any issues detected, automatically rollback to previous version.
