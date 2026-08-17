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
| `android-release.yaml`          | Builds signed APK and AAB files for .NET MAUI Android applications with release versioning. |

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
      project_path: './MyProject.sln'
      project_name: 'MyProject'
      need_run_test: true
    secrets:
      NUGET_APIKEY: ${{ secrets.NUGET_APIKEY }}
      DISCORD_WEBHOOK: ${{ secrets.DISCORD_WEBHOOK }}
```

> ✅ Note: Replace @main with a tag or commit SHA for version pinning (recommended for stability).

---

**android-release.yaml**

The **android-release.yaml** workflow is a reusable GitHub Actions template for building and publishing signed Android release packages (.apk and .aab) for .NET MAUI applications. It handles keystore management, signing configuration, and artifact generation.

### Inputs

| Name             | Type    | Required | Default                  | Description                                      |
|------------------|---------|----------|--------------------------|--------------------------------------------------|
| `project_path`   | string  | Yes      | `src/myApp/myApp.csproj` | Path to the MAUI project file.                   |
| `version_name`   | string  | Yes      | `1.0.0`                  | Application version name (e.g., 1.0.0).         |
| `version_code`   | string  | Yes      | `1`                      | Application version code (numeric increment).    |
| `release_title`  | string  | Yes      | `MyApp Release`          | Title/name of the release.                       |
| `dotnet_version` | string  | No       | `10.0.x`                 | .NET SDK version to use for build and publish.   |

### Secrets

| Name                | Required | Description                                        |
|---------------------|----------|----------------------------------------------------|
| `KEY_ALIAS`         | Yes      | Alias of the key in the keystore.                  |
| `KEY_PASSWORD`      | Yes      | Password for the private key.                      |
| `KEYSTORE_PASSWORD` | Yes      | Password for the keystore file.                    |
| `KEYSTORE_BASE64`   | Yes      | Base64-encoded keystore file for signing APK/AAB. |

### Usage Example

In your repository, create or update a workflow file like `.github/workflows/android-release.yml`:

```yaml
name: Build Android Release

on:
  workflow_dispatch:
  push:
    tags:
      - 'android-v*.*.*'

jobs:
  build-android:
    uses: SkJonko/Centralized-Workflow-YAMLs/.github/workflows/android-release.yaml@main
    with:
      project_path: './src/MyApp/MyApp.csproj'
      version_name: '1.0.0'
      version_code: '1'
      release_title: 'MyApp Android Release v1.0.0'
      dotnet_version: '10.0.x'
    secrets:
      KEY_ALIAS: ${{ secrets.ANDROID_KEY_ALIAS }}
      KEY_PASSWORD: ${{ secrets.ANDROID_KEY_PASSWORD }}
      KEYSTORE_PASSWORD: ${{ secrets.ANDROID_KEYSTORE_PASSWORD }}
      KEYSTORE_BASE64: ${{ secrets.ANDROID_KEYSTORE_BASE64 }}
```

> ✅ Note: Ensure your Android signing credentials are securely stored as repository secrets. The KEYSTORE_BASE64 should be generated by encoding your .keystore file in base64 format.

# 🎯 Benefits

- Reusable, DRY GitHub Actions
- Fast setup across multiple repos
- Maintenance in one place
- Supports secure and stable versioning

# 📚 Resources

- [GitHub Reusable Workflows Documentation](https://docs.github.com/en/actions/sharing-automations/reusing-workflows)
- [Including README in NuGet Packages](https://learn.microsoft.com/en-us/nuget/nuget-org/package-readme-on-nuget-org)
