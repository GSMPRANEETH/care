# Upstream Sync Workflow

## Overview

This repository is configured to automatically stay in sync with its upstream repository (`ohcnetwork/care`) using a GitHub Actions workflow.

## How It Works

The sync workflow (`.github/workflows/sync-upstream.yml`) automatically:

1. **Runs on a schedule** - Every hour, the workflow checks for new commits in the upstream repository
2. **Syncs multiple branches** - By default, it syncs the `develop`, `staging`, and `production` branches
3. **Handles conflicts gracefully** - If a merge conflict is detected, the workflow will:
   - Abort the merge
   - Log a warning
   - Keep the current state intact
   - Require manual intervention

## Manual Triggering

You can manually trigger the sync workflow at any time:

1. Go to the **Actions** tab in your GitHub repository
2. Select the **"Sync with Upstream"** workflow
3. Click **"Run workflow"**
4. Optionally specify which branches to sync (comma-separated)
5. Click the green **"Run workflow"** button

### Example: Sync specific branches

When manually triggering, you can specify branches in the input field:
- `develop` - Only sync the develop branch
- `develop,staging` - Sync both develop and staging branches
- `develop,staging,production` - Sync all three branches (default)

## What Happens During Sync

For each branch:

1. **Fetch upstream** - Gets the latest commits from `ohcnetwork/care`
2. **Check if update needed** - Compares local and upstream commits
3. **Merge changes** - If updates exist, merges them into the local branch
4. **Push to origin** - Pushes the updated branch to your fork
5. **Handle conflicts** - If conflicts occur, aborts and logs a warning

## Monitoring

### View Sync Results

- Check the **Actions** tab to see workflow runs
- Each run provides a summary of:
  - Which branches were processed
  - Success/failure status for each branch
  - Warnings if manual intervention is needed

### When Manual Intervention is Needed

If you see a warning about merge conflicts:

1. Manually sync the affected branch:
   ```bash
   git checkout <branch-name>
   git fetch upstream
   git merge upstream/<branch-name>
   # Resolve conflicts
   git add .
   git commit
   git push origin <branch-name>
   ```

2. The automatic sync will resume on the next scheduled run

## Configuration

### Change Sync Frequency

Edit the cron schedule in `.github/workflows/sync-upstream.yml`:

```yaml
schedule:
  - cron: '0 * * * *'  # Every hour
```

Common cron schedules:
- `0 * * * *` - Every hour
- `*/30 * * * *` - Every 30 minutes  
- `0 */6 * * *` - Every 6 hours
- `0 0 * * *` - Daily at midnight

### Change Default Branches

Edit the default branches in `.github/workflows/sync-upstream.yml`:

```yaml
default: 'develop,staging,production'
```

## Upstream Repository

This fork is synchronized with: **https://github.com/ohcnetwork/care**

## Disabling Auto-Sync

To disable automatic syncing:

1. Go to the **Actions** tab
2. Select the **"Sync with Upstream"** workflow
3. Click the **"..."** menu (top right)
4. Select **"Disable workflow"**

You can still manually trigger syncs when needed.

## Troubleshooting

### Workflow Not Running

- Check if the workflow is enabled in the Actions tab
- Verify repository permissions allow GitHub Actions to run
- Check the workflow file syntax is valid

### Persistent Merge Conflicts

If a branch consistently has conflicts:

1. Consider creating a separate branch for your custom changes
2. Rebase your changes on top of upstream instead of merging
3. Manually resolve conflicts and push

### Authentication Issues

The workflow uses `GITHUB_TOKEN` which is automatically provided by GitHub Actions. No additional setup is needed.

## Best Practices

1. **Avoid modifying synced branches directly** - Make changes in feature branches instead
2. **Monitor sync results** - Regularly check the Actions tab for warnings
3. **Keep documentation in sync** - Update this file if you modify the workflow
4. **Test changes** - Always test after a sync to ensure nothing breaks

## Related Documentation

- [GitHub Repository Structure](../docs/development/github-repo/index.rst)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Managing Forks](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks)
