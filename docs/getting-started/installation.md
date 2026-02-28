---
title: Installation
---

Before you can set up a build project, you need to install NUKE's dedicated [.NET global tool](https://docs.microsoft.com/en-us/dotnet/core/tools/global-tools):

```powershell
# terminal-command
dotnet tool install GreemDev.Nuke.GlobalTool --global
```

From now on, you can use the global tool to:

- [Set up new builds](setup.md)
- [Run existing builds](execution.md)
- [Leverage shell completion](../global-tool/shell-completion.md)
- [Add tool & library packages](../global-tool/packages.md)
- [Navigate around root directories](../global-tool/navigation.md)
- [Convert CAKE build scripts](../global-tool/cake.md)
- [Manage secrets in parameter files](../global-tool/secrets.md)

:::note
If you're running on Linux-based systems, it's worth checking if the `gruke` global tool is available. This can be verified with `where gruke`. If the global tool is not found, you have to manually add `$HOME/.dotnet/tools` to your terminal configuration:

<Tabs>
  <TabItem value="zsh" label="Zsh" default>

```powershell
# terminal-command
echo 'export PATH=$HOME/.dotnet/tools:$PATH' >> ~/.zshrc
```

  </TabItem>
</Tabs>
:::

:::info
While theoretically, you could use NUKE by only adding its main NuGet package, we highly recommend using the dedicated global tool to set up new builds. This ensures that your repository will run consistently in different environments and that your build implementation is always properly formatted.
:::
