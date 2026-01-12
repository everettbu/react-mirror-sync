# Mirror Sync

Automated sync for mirroring GitHub repositories to `greptileai/*-mirror` repos.

## What it does

Every 30 minutes, this workflow syncs configured repositories in parallel:

1. **Syncs all branches** - Force-pushes all branches from upstream
2. **Syncs tags** - Pushes all tags from upstream
3. **Syncs labels** - Copies labels from upstream repo
4. **Syncs PRs** - For every open PR on upstream:
   - Creates a corresponding branch on the mirror
   - Creates a corresponding PR with the same title and description
   - Updates branches when upstream PRs are updated
   - Closes mirror PRs when upstream PRs are closed/merged

## Adding a new repo

1. Edit `repos.yaml` and add a new entry:

```yaml
repos:
  - name: react
    upstream: facebook/react
    mirror: greptileai/react-mirror
    excluded_prs: [32222]

  - name: nextjs          # Add your repo
    upstream: vercel/next.js
    mirror: greptileai/nextjs-mirror
    excluded_prs: []
```

2. Create the mirror repository on GitHub (e.g., `greptileai/nextjs-mirror`)

3. Ensure your `MIRROR_PAT` has access to the new mirror repo

4. Commit and push - the workflow will start syncing on the next run

## Configuration

### repos.yaml fields

| Field | Description |
|-------|-------------|
| `name` | Short identifier for logging |
| `upstream` | Source repository (e.g., `facebook/react`) |
| `mirror` | Destination repository (e.g., `greptileai/react-mirror`) |
| `excluded_prs` | List of PR numbers to skip (e.g., branch name collisions) |

## Setup

### 1. Create a Personal Access Token (PAT)

Create a PAT with the following permissions:
- `repo` (full control of private repositories)
- `workflow` (update GitHub Action workflows)

The PAT must have access to all mirror repositories under `greptileai/`.

### 2. Add the secret

Add the PAT as a repository secret named `MIRROR_PAT`:
1. Go to this repo's Settings > Secrets and variables > Actions
2. Click "New repository secret"
3. Name: `MIRROR_PAT`
4. Value: Your PAT

### 3. Enable the workflow

The workflow runs automatically every 30 minutes. You can also trigger it manually from the Actions tab.

## Manual trigger

Go to Actions > "Sync Mirror Repos" > "Run workflow"

## Architecture

```
repos.yaml                    # Central config defining all repo pairs
  |
sync.yml (matrix strategy)    # GitHub Actions runs jobs in parallel
  |
sync_mirror.py (parameterized)  # Python script accepts repo config as args
```

- Jobs run in parallel (up to 5 concurrent) with `fail-fast: false`
- One repo failure doesn't affect others
- Each sync job is independent (no shared state)
