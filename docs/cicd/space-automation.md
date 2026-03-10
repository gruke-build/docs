---
title: Space Automation
---

!!! danger "JetBrains SpaceCode and Space has been discontinued."
    [View the blog post here.](https://blog.jetbrains.com/space/2024/11/27/discontinuation-of-the-spacecode-private-preview/)

Running on [JetBrains Space](https://www.jetbrains.com/space/) will use the standard theming for your build log output:

![Space Automation Log Output](space-automation-light.webp#gh-light-mode-only)
![Space Automation Log Output](space-automation-dark.webp#gh-dark-mode-only)

!!! info
    Please refer to the official [Space Automation documentation](https://www.jetbrains.com/help/space/getting-started.html) for questions not covered here.


## Environment Variables

You can access [predefined environment variables](https://www.jetbrains.com/help/space/automation-environment-variables.html) by using the `SpaceAutomation` class:

```csharp
SpaceAutomation SpaceAutomation => SpaceAutomation.Instance;

Target Print => _ => _
    .Executes(() =>
    {
        Log.Information("Branch = {Branch}", SpaceAutomation.GitBranch);
        Log.Information("Commit = {Commit}", SpaceAutomation.GitRevision);
    });
```

A full reference of available variables and their documentation can be found [here](https://gruke.readthedocs.io/docfx/api/Nuke.Common.CI.SpaceAutomation.SpaceAutomation.html).