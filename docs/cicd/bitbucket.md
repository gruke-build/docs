---
title: Bitbucket
---

Running on [Bitbucket](https://bitbucket.org/) will use the standard theming for your build log output.

!!! info
    Please refer to the official [Bitbucket documentation](https://confluence.atlassian.com/bitbucketserver/) for questions not covered here.

## Environment Variables

You can access [predefined environment variables](https://support.atlassian.com/bitbucket-cloud/docs/variables-and-secrets/) by using the `Bitbucket` class:

```csharp
Bitbucket Bitbucket => Bitbucket.Instance;

Target Print => _ => _
    .Executes(() =>
    {
        Log.Information("Branch = {Branch}", Bitbucket.Branch);
        Log.Information("Commit = {Commit}", Bitbucket.Commit);
    });
```

A full reference of available variables and their documentation can be found [here](https://gruke.readthedocs.io/docfx/api/Nuke.Common.CI.Bitbucket.Bitbucket.html).
