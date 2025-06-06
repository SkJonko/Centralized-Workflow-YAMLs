# NuGet Publish GitHub Actions Workflow Template

This repository provides a reusable GitHub Actions workflow for building, testing, validating, and publishing .NET projects as NuGet packages. It is designed to be used as a template via `workflow_call` in other repositories.

# Features

- **Configurable .NET SDK version** (_default: 9.0.x_)
- **Builds and packs** your .NET project into a NuGet package
- **Runs tests** (_optional, enabled by default_)
- **Validates** the generated NuGet package using [Meziantou NuGet Package Validator](https://www.nuget.org/packages/Meziantou.Framework.NuGetPackageValidation.Tool)
- **Publishes artifacts** for downstream jobs
- **Notifies via Discord** on release (_requires webhook_)
- **Deploys to NuGet.org** on release (_requires API key_)

# Templates

## Workflow

**nuget-publish-template.yaml**

The **nuget-publish-template.yaml** workflow is a reusable GitHub Actions template for automating the build, test, validation, and publication of .NET projects as NuGet packages. Designed to be invoked via workflow_call from other repositories, it streamlines the entire CI/CD process for .NET libraries.

## Inputs and Secrets

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

### Helpers

<details>
    <summary>deploy-nuget.yaml</summary>

---
The **deploy-nuget.yaml** workflow is designed to automate the final step of publishing your .NET package to NuGet.org. It is typically called from a parent workflow (_such as **nuget-publish-template.yaml**_) after the package has been built, tested, and validated.


### Inputs

| Name             | Type   | Required | Default   | Description                      |
|------------------|--------|----------|-----------|----------------------------------|
| `dotnet_version` | string | No       | `9.0.x`   | .NET SDK version to use for publishing the package. |

### Secrets

| Name           | Required | Description                                 |
|----------------|----------|---------------------------------------------|
| `NUGET_APIKEY` | Yes      | API key for publishing to NuGet.org.        |

**Example usage:**
```
  deploy:
    uses: SkJonko/Centralized-Workflow-YAMLs/.github/workflows/helpers/deploy-nuget.yaml@main
    with:
      dotnet_version: ${{ inputs.dotnet_version }}
    secrets:
      NUGET_APIKEY: ${{ secrets.NUGET_APIKEY }}
```

</details>

<details>
    <summary>notify-discord.yaml</summary>

---
The **notify-discord.yaml** workflow is designed to send automated notifications to a specified Discord channel via a webhook. It is typically used to inform your team about important events in your CI/CD pipeline, such as the release of a new NuGet package.

### Inputs

| Name           | Type   | Required | Description                        |
|----------------|--------|----------|------------------------------------|
| `project_name` | string | Yes      | Name of the project/package to include in the Discord notification. |

### Secrets

| Name             | Required | Description                        |
|------------------|----------|------------------------------------|
| `DISCORD_WEBHOOK`| Yes      | Discord webhook URL for sending notifications. |


**Example usage:**
```
  notify_discord:
    uses: SkJonko/Centralized-Workflow-YAMLs/.github/workflows/helpers/notify-discord.yaml@main
    with:
      project_name: ${{ inputs.project_name }}
    secrets:
      DISCORD_WEBHOOK: ${{ secrets.DISCORD_WEBHOOK }}
```
    
</details>


## Workflow Overview

1. **Create NuGet Package**: Checks out code, sets up .NET, builds, and packs the project.
2. **Run Tests**: Optionally runs tests if enabled.
3. **Validate Package**: Validates the NuGet package for best practices.
4. **Notify Discord**: Sends a notification on release events.
5. **Deploy to NuGet.org**: Publishes the package to NuGet.org on release.

## Requirements

- Your repository must provide the required secrets.
- The workflow expects a .NET project compatible with the specified SDK version.

## Customization

You can fork or reference this workflow and adjust steps as needed for your project structure or requirements.
