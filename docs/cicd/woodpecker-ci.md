---
title: Woodpecker CI
---

Running on [Woodpecker CI](https://woodpecker-ci.org/) will use the standard theming for your build log output.

!!! info
    Please refer to the official [Woodpecker documentation](https://woodpecker-ci.org/docs/intro) for questions not covered here.

## Environment Variables

You can access [predefined environment variables](https://woodpecker-ci.org/docs/usage/environment#built-in-environment-variables) by using the `WoodpeckerCI` class:

```csharp
WoodpeckerCI WoodpeckerCI => WoodpeckerCI.Instance;

Target Print => _ => _
    .Executes(() =>
    {
        Log.Information("Branch = {Branch}", WoodpeckerCI.CommitRef);
        Log.Information("Commit = {Commit}", WoodpeckerCI.CommitSha);
    });
```

A full reference of available variables and their documentation can be found [here](https://nuke.greemdev.net/docfx/api/Nuke.Common.CI.WoodpeckerCI.WoodpeckerCI.html).

## Configuration Generation

You can generate [workflow files](https://woodpecker-ci.org/docs/usage/workflows) from your existing target definitions by adding the `WoodpeckerCI` attribute. For instance, you can run the `Compile` target on every push to the default branch with the latest .NET SDK image:

```csharp title="Build.cs"
[WoodpeckerCI(
    name: "ci",
    OnlyOnBranches = [ default ],
    Triggers = [ WoodpeckerCIEvent.Push ],
    InvokedTargets = [ nameof(Compile) ])]
class Build : NukeBuild { /* ... */ }
```

??? note "Generated output"

    ```kotlin title=".woodpecker/ci.yml"
    when:
      branch: ${CI_REPO_DEFAULT_BRANCH}
      event: push
    
    steps:
      - name: 'Run: Test'
        image: mcr.microsoft.com/dotnet/sdk:10.0.401
        commands:
          - './build.sh Test'
    ```

Woodpecker automatically configures a default clone step if it is not explicitly defined. The default clone is a shallow clone. If you need the full Git history, i.e. for GitVersion, you can specify `MinimalFetch = false` in the attribute:

```csharp title="Build.cs" hl_lines="6"
[WoodpeckerCI(
    name: "ci",
    OnlyOnBranches = [ default ],
    Triggers = [ WoodpeckerCIEvent.Push ],
    InvokedTargets = [ nameof(Compile) ],
    MinimalFetch = false)]
class Build : NukeBuild { /* ... */ }
```

??? note "Generated output"

    ```yml title=".woodpecker/ci.yml" hl_lines="4 5 6 7 8 9 10"
    when:
      branch: ${CI_REPO_DEFAULT_BRANCH}
      event: push
    clone:
      - name: 'Checkout Code'
        image: woodpeckerci/plugin-git
        settings:
          depth: 0
          partial: false
          tags: true
    
    steps:
      - name: 'Run: Test'
        image: mcr.microsoft.com/dotnet/sdk:10.0.401
        commands:
          - './build.sh Test'
    ```

Using `default` (or `null`) as a value for `OnlyOnBranches` will inject `${CI_REPO_DEFAULT_BRANCH}`. This allows you to write `OnlyOnBranches = [ default ]` and have it literally mean "Only on the default branch."

### Limitations

WoodpeckerCI has no native artifacts functionality. You are expected to use a plugin, and connect your own storage such as an S3 bucket.
As a result, this functionality is not present in the workflow generator. If you want to use one of those artifact plugins, you will need to either modify the generated output, or write the workflow file yourself.

Alternatively, you can use any of the `ICreate<Platform>Release` component interfaces from special component packages:

  - [`ICreateGitLabRelease`](https://nuke.greemdev.net/docfx/api/Nuke.Components.ICreateGitLabRelease.html) from [GreemDev.Nuke.Components.GitLab](https://www.nuget.org/packages/GreemDev.Nuke.Components.GitLab)
  - [`ICreateGitHubRelease`](https://nuke.greemdev.net/docfx/api/Nuke.Components.ICreateGitHubRelease.html) from [GreemDev.Nuke.Components.GitHub](https://www.nuget.org/packages/GreemDev.Nuke.Components.GitHub)
  - [`ICreateForgejoRelease`](https://nuke.greemdev.net/docfx/api/Nuke.Components.ICreateForgejoRelease.html) from [GreemDev.Nuke.Components.Forgejo](https://www.nuget.org/packages/GreemDev.Nuke.Components.Forgejo)