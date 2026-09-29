# AegisAI_v2

## 본인 역할 (이정재) — AI 파트
- 정적분석 도구와 AI 판정을 결합한 닫힌 루프 검증 구조 설계: AI가 고친 코드를 정적분석으로 재검사해 통과한 결과만 남기는 파이프라인 방향 담당
- 취약점 수정 모델의 학습 데이터셋 고도화 및 파인튜닝 담당(학습은 외부 GPU 서버에서 진행)

## Start Here

This repository is organized around an agent-first MVP baseline for AegisAI.

- Agents should start with [`AGENTS.md`](./AGENTS.md).
- The canonical execution path lives in [`specs/001-aegisai-mvp-foundation/quickstart.md`](./specs/001-aegisai-mvp-foundation/quickstart.md).
- The background product baseline lives in [`spec 2.2.md`](./spec%202.2.md).
- The active implementation package lives in [`specs/001-aegisai-mvp-foundation/`](./specs/001-aegisai-mvp-foundation/).

If you are starting development work, follow the agent path `AGENTS.md -> quickstart.md ->
feature package` instead of jumping directly to `tasks.md`.

## GitHub Conventions

The repository follows the GitHub workflow convention documented in
[`docs/github-conventions.md`](./docs/github-conventions.md).

- Branches use `feat/<issue-number>-<short-feature>` and the matching `fix/`,
  `refactor/`, `release/`, and `hotfix/` variants
- Commit messages use `<type>: <description>`
- Issue titles and PR titles must match
- `dev` is the default integration branch and `main` remains the release-ready branch

Spec Kit feature docs still live in [`specs/001-aegisai-mvp-foundation/`](./specs/001-aegisai-mvp-foundation/),
so GitHub-style working branches may still need `SPECIFY_FEATURE` when running Spec Kit
helpers.

## CI

GitHub Actions runs the same baseline verification path used locally:

- `corepack pnpm lint`
- `corepack pnpm test`
- `corepack pnpm typecheck`
- `corepack pnpm build`

## CD

Production CD targets a single Oracle Cloud VPS.

- Registry: `ghcr.io`
- Trigger: `main` push or manual `workflow_dispatch`
- Runtime: Docker Compose on the VPS
- Deploy path files: `deploy/oracle/docker-compose.infra.yml`, `deploy/oracle/docker-compose.app.yml`, `deploy/oracle/deploy.sh`, a server-managed `.env`, and the one-time helper `deploy/oracle/bootstrap-infra.sh`
- Automated rollout scope: app stack only (`api`, `web`)
- Manual bootstrap scope: infra stack (`postgres`, `redis`)
- Detailed runbook: [`deploy/oracle/BOOTSTRAP.md`](./deploy/oracle/BOOTSTRAP.md)

Required GitHub secrets for CD:

- `ORACLE_VPS_HOST`
- `ORACLE_VPS_USER`
- `ORACLE_VPS_SSH_PRIVATE_KEY`
- `ORACLE_DEPLOY_PATH`
- `GHCR_USERNAME`
- `GHCR_READ_TOKEN`

The deploy job targets the `production` GitHub environment, so set these as available secrets before running CD. If any are blank or missing, the workflow now fails early with a clear validation error instead of reaching the SSH steps.

For first-time Oracle Cloud setup, follow the runbook in [`deploy/oracle/BOOTSTRAP.md`](./deploy/oracle/BOOTSTRAP.md) rather than reproducing the steps manually from memory.
