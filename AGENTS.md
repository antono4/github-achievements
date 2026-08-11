# Repository Knowledge

## Project: github-achievements
GitHub Actions workflow (`.github/workflows/agent.yml`) that farms GitHub Achievements
on repo `antono4/github-achievements` (public, default branch `main`).

## Key facts / gotchas
- **Workflow file**: `.github/workflows/agent.yml`, scheduled `*/3 * * * *` (GitHub throttles to ~5 min).
- **Achievements targeted**: Pull Shark + YOLO (open & squash-merge trivial PR), Quickdraw (open & close issue < 5 min), optional Pair Extraordinaire (Co-authored-by trailer via vars COAUTHOR_NAME/COAUTHOR_EMAIL).
- **Attribution**: GitHub Achievements count ONLY toward the PR/issue author. The runner's default `github.token` attributes to `github-actions[bot]` → does NOT count. A PAT must be stored as repo secret `PAT_TOKEN` (scope `repo`) so the workflow uses it; then PRs/issues are authored by the user.
- **PR-creation restriction**: runner `github.token` cannot create PRs by default repo setting "Allow GitHub Actions to create and approve pull requests". The external token used in this session cannot toggle that setting (fine-grained PAT lacks Administration:write) nor set repo secrets (`/actions/secrets/public-key` → 403).
- **Workflow uses REST API (curl), NOT `gh` CLI**: `gh pr create` failed inside Actions because of the above restriction; REST API with the same token also fails for the runner token (same restriction). Using curl keeps it consistent and lets a PAT work without gh auth quirks.
- **Verified working (manual, via external token)**: PR #1 created + merged, issue #2 created + closed — authored by `antono4`.

## Remaining manual step (cannot be done with current token)
To make the scheduled workflow actually run + credit achievements to `antono4`:
1. Create a classic PAT with `repo` scope at https://github.com/settings/tokens
2. Add it as repo secret `PAT_TOKEN`: https://github.com/antono4/github-achievements/settings/secrets/actions
(Alternative that makes the workflow run but does NOT credit achievements: enable
"Allow GitHub Actions to create and approve pull requests" at
https://github.com/antono4/github-achievements/settings/actions — under Workflow permissions.)

## API endpoints used
- Repo: `https://api.github.com/repos/antono4/github-achievements`
- Workflow ID: 331759641
- Dispatch: `POST /actions/workflows/331759641/dispatches` body `{"ref":"main"}`
