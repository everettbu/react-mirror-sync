# React Mirror Sync

Automated sync for [greptileai/react-mirror](https://github.com/greptileai/react-mirror) from [facebook/react](https://github.com/facebook/react).

## What it does

Every 30 minutes, this workflow:

1. **Syncs main branch** - Updates `main` to match `facebook/react:main`
2. **Syncs tags** - Pushes all tags from upstream
3. **Syncs PRs** - For every open PR on facebook/react:
   - Creates a corresponding branch on the mirror
   - Creates a corresponding PR with the same title and description
   - Updates branches when upstream PRs are updated
   - Closes mirror PRs when upstream PRs are closed/merged

## Setup

### 1. Create a Personal Access Token (PAT)

Create a PAT with the following permissions:
- `repo` (full control of private repositories)
- `workflow` (update GitHub Action workflows)

### 2. Add the secret

Add the PAT as a repository secret named `MIRROR_PAT`:
1. Go to this repo's Settings → Secrets and variables → Actions
2. Click "New repository secret"
3. Name: `MIRROR_PAT`
4. Value: Your PAT

### 3. Enable the workflow

The workflow runs automatically every 30 minutes. You can also trigger it manually from the Actions tab.

## Manual trigger

Go to Actions → "Sync React Mirror" → "Run workflow"
