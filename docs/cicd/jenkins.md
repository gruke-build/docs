---
title: Jenkins
---

Running on [Jenkins](https://www.jenkins.io/) will use the standard theming for your build log output.

!!! info
    Please refer to the official [Jenkins documentation](https://www.jenkins.io/doc/) for questions not covered here.

## Environment Variables

You can access [predefined environment variables](https://wiki.jenkins.io/display/JENKINS/Building+a+software+project#Buildingasoftwareproject-belowJenkinsSetEnvironmentVariables) by using the `Jenkins` class:

```csharp
Jenkins Jenkins => Jenkins.Instance;

Target Print => _ => _
    .Executes(() =>
    {
        Log.Information("Branch = {Branch}", Jenkins.GitBranch);
        Log.Information("Commit = {Commit}", Jenkins.GitCommit);
    });
```

A full reference of available variables and their documentation can be found [here](https://gruke-build.github.io/src/api/Nuke.Common.CI.Jenkins.Jenkins).
