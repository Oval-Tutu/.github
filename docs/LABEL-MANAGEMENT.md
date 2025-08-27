# Label Management for Oval Tutu Organization

This document explains how labels are managed across all repositories in the Oval Tutu organization.

## 🏷️ How It Works

### Source of Truth
The **Oval-Tutu/.github** repository serves as the authoritative source for all organization labels. Labels in this repository are **manually maintained** and should be carefully curated.

### Automatic Synchronization
The [Label Sync Workflow](../.github/workflows/sync-labels.yml) automatically ensures that all other repositories in the organization have the same labels as the `.github` repository.

## 📋 Managing Labels

### Adding New Labels
1. Go to the [Oval-Tutu/.github repository labels page](https://github.com/Oval-Tutu/.github/labels)
2. Click **"New label"**
3. Configure the label name, color, and description
4. The sync workflow will automatically apply this label to all other repositories

### Modifying Existing Labels
1. Go to the [labels page](https://github.com/Oval-Tutu/.github/labels)
2. Click the **edit button** (pencil icon) next to the label
3. Make your changes
4. The sync workflow will automatically update this label across all repositories

### Deleting Labels
1. Go to the [labels page](https://github.com/Oval-Tutu/.github/labels)
2. Click the **delete button** next to the label
3. **Note**: The sync workflow only creates/updates labels, it doesn't delete them from other repositories
4. If you need to remove a label organization-wide, you'll need to do it manually or create a separate cleanup script

## 🔄 Sync Triggers

The label sync workflow runs automatically when:

- **Label changes**: When any label is added, modified, or applied to issues in the `.github` repository
- **Manual trigger**: Can be triggered manually from the Actions tab
- **Weekly schedule**: Every Monday at 2 AM UTC to catch any drift

## 📊 Monitoring Sync Status

### Viewing Sync Results
1. Go to [Actions tab](https://github.com/Oval-Tutu/.github/actions)
2. Look for **"Sync Organization Labels"** workflows
3. Click on a run to see detailed logs and summary

### What Gets Synced
- ✅ **Included**: All active, non-fork repositories in the organization
- ❌ **Excluded**: 
  - `.github` repository (source of truth)
  - Archived repositories
  - Fork repositories
  - Private repositories (depending on permissions)

## 🛠️ Troubleshooting

### Sync Failures
If the sync workflow fails:
1. Check the workflow logs for error messages
2. Ensure the `GITHUB_TOKEN` has proper permissions
3. Verify that target repositories exist and are accessible
4. Run the workflow manually to retry

### Missing Labels on New Repositories
When creating new repositories:
1. The labels will be synced automatically within 24 hours (weekly schedule)
2. Or trigger the workflow manually for immediate sync
3. Or add any label to the `.github` repository to trigger an automatic sync

### Manual Sync Command
If needed, you can manually sync labels using GitHub CLI:

```bash
# Get labels from source repository
gh label list --repo Oval-Tutu/.github --json name,color,description > labels.json

# Apply to target repository
cat labels.json | jq -r '.[] | @base64' | while IFS= read -r label; do
  data=$(echo $label | base64 -d)
  name=$(echo $data | jq -r '.name')
  color=$(echo $data | jq -r '.color')
  desc=$(echo $data | jq -r '.description // ""')
  gh label create "$name" --color "$color" --description "$desc" --repo Oval-Tutu/TARGET-REPO --force
done
```

## 📝 Best Practices

1. **Think before adding**: Labels in `.github` will appear on ALL repositories
2. **Use clear names**: Labels should be self-explanatory
3. **Consistent colors**: Use similar colors for related label types
4. **Meaningful descriptions**: Help contributors understand when to use each label
5. **Regular cleanup**: Periodically review and remove unused labels

## 🎯 Recommended Label Categories

Consider organizing labels into these categories:

- **Type**: `bug`, `enhancement`, `documentation`
- **Priority**: `priority-high`, `priority-medium`, `priority-low`
- **Status**: `needs-triage`, `needs-discussion`, `wontfix`
- **Area**: `ui-ux`, `performance`, `gameplay`, `audio`
- **Difficulty**: `good-first-issue`, `help-wanted`
- **Game-specific**: `game-breaking`, `balance`, `content`

---

💡 **Remember**: Changes to labels in the `.github` repository affect the entire organization. Use this power responsibly!