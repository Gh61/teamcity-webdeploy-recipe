# TeamCity Web Deploy Recipe

A TeamCity recipe (formerly meta-runner) for deploying ASP.NET Core applications to IIS using [msdeploy](https://www.iis.net/downloads/microsoft/web-deploy) (Web Deploy). Designed as a drop-in replacement for the classic .NET Framework WebDeploy behavior.

## Features

- Deploys the output of `dotnet publish` to an IIS site via msdeploy
- Skips deletion of the `App_Data` folder and its contents during deployment — same behavior as .NET Framework WebDeploy (optional, enabled by default)
- Optionally sets write permissions on `App_Data` for the IIS application pool identity via the msdeploy `setAcl` provider
- Puts the site offline during deployment using the `AppOffline` rule
- Works with self-signed certificates (`-allowUntrusted`)

## Files

| File | Description |
|---|---|
| `webdeploy-meta-runner.xml` | Legacy XML format (meta-runner), compatible with all TeamCity versions |
| `webdeploy-recipe.yml` | Modern YAML format, compatible with TeamCity 2025.03+ |

## Parameters

| Parameter | Default | Description |
|---|---|---|
| Source path | `%teamcity.build.checkoutDir%\publish` | Path to the `dotnet publish` output |
| IIS site name | _(required)_ | Name of the IIS site, e.g. `MySite` or `Default Web Site/MyApp` |
| Web Deploy URL | _(required)_ | URL of the Web Deploy endpoint, e.g. `https://server:8172/msdeploy.axd` |
| Username | _(required)_ | Web Deploy username |
| Password | _(required)_ | Web Deploy password (use a secret parameter) |
| Skip App_Data | `true` | Prevents deletion of `App_Data` and its contents on the destination server |
| Set App_Data permissions | `false` | Sets write permissions on `App_Data` for the IIS application pool identity |

## Requirements

- msdeploy (Web Deploy) must be installed on the TeamCity build agent
- The build agent requirement `system.msdeploy` is checked automatically (XML version only)
- The Web Deploy user must have the `setAcl` provider enabled in IIS Management Service Delegation (only required when **Set App_Data permissions** is enabled)

## Installation

### XML (meta-runner)

1. In TeamCity, go to **Project Settings → Recipes**
2. Click **Upload recipe** and select `webdeploy-meta-runner.xml`

### YAML

1. In TeamCity, go to **Project Settings → Recipes**
2. Click **Upload private recipe** and select `webdeploy-recipe.yml`

Or install directly from [JetBrains Marketplace](https://plugins.jetbrains.com/teamcity_recipe).

## Background

In classic ASP.NET (.NET Framework), WebDeploy automatically skipped the `App_Data` folder during deployment and set write permissions for the application pool identity. This behavior was built into `Microsoft.Web.Publishing.targets`.

In .NET Core, this does not happen automatically. This recipe replicates the original behavior, making it straightforward to deploy .NET Core applications to IIS without losing data stored in `App_Data`.

## License

GPL-3.0
