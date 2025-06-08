# 📦 Centralized Workflow YAMLs

A centralized repo of reusable [GitHub Actions](https://docs.github.com/actions) workflows designed for:

- ✅ .NET NuGet publishing  
- 🔄 CI/CD pipelines  
- 📁 Central management of GitHub Actions logic across repositories

---

## 🛠️ Available Workflows

| Workflow Name                | Description                                               |
|-----------------------------|-----------------------------------------------------------|
| `nuget-publish-template.yaml` | Builds, tests, packs, and publishes .NET NuGet packages   |
| `deploy-nuget.yaml`             | Deploys pre-built NuGet packages to nuget.org; meant to be called by other workflows. |
| `notify-discord.yaml`           | Sends a notification to a Discord channel with your desired message. |
| `run-tests.yaml`                | Discovers test projects, runs unit tests with code coverage, and uploads a coverage report. Supports skipping via input. |
| `validate-nuget.yaml`           | Validates NuGet package metadata and structure using Meziantou's NuGet validation tool. |

---
**nuget-publish-template.yaml**

The **nuget-publish-template.yaml** workflow is a reusable GitHub Actions template for automating the build, test, validation, and publication of .NET projects as NuGet packages. Designed to be invoked via workflow_call from other repositories, it streamlines the entire CI/CD process for .NET libraries.


### Inputs

| Name             | Type    | Required | Default     | Description                                      |
|------------------|---------|----------|-------------|--------------------------------------------------|
| `project_name`   | string  | Yes      |             | Name of the project/package.                     |
| `project_path`   | string  | No       | `./`        | Path to the project file or directory.           |
| `dotnet_version` | string  | No       | `9.0.x`     | .NET SDK version to use for build and publish.   |
| `need_run_test`       | boolean | No       | `true`      | Whether to run tests before packaging.           |

### Secrets

| Name             | Required | Description                                 |
|------------------|----------|---------------------------------------------|
| `NUGET_APIKEY`   | Yes      | API key for publishing to NuGet.org.        |
| `DISCORD_WEBHOOK`| Yes      | Discord webhook URL for notifications.      |

---

## ⬇️ How to Use

In your repository, create or update a workflow file like `.github/workflows/publish.yml`:

```yaml
name: Publish NuGet Package

on:
  workflow_dispatch:
  push:
    tags:
      - 'v*.*.*'

jobs:
  publish:
    uses: SkJonko/Centralized-Workflow-YAMLs/.github/workflows/nuget-publish-template.yml@main
    with:
      solution: 'MyProject.sln'
      need_run_test: true
    secrets:
      NUGET_APIKEY: ${{ secrets.NUGET_APIKEY }}
      DISCORD_WEBHOOK: ${{ secrets.DISCORD_WEBHOOK }}
```

> ✅ Note: Replace @main with a tag or commit SHA for version pinning (recommended for stability).


# 🎯 Benefits

- Reusable, DRY GitHub Actions
- Fast setup across multiple repos
- Maintenance in one place
- Supports secure and stable versioning

# 📚 Resources

- [GitHub Reusable Workflows Documentation](https://docs.github.com/en/actions/sharing-automations/reusing-workflows)
- [Including README in NuGet Packages](https://learn.microsoft.com/en-us/nuget/nuget-org/package-readme-on-nuget-org)
