---
title: NuGet Trusted Publishing
---

For years, NuGet has offered just an API key for publishing packages via automation. It was always prone to being forgotten about since they've always enforced expiration dates, too.

Forget about all that! [Users of GitLab and GitHub can now skip the API key, and simply use an OIDC token exchange.](https://aka.ms/nuget/trusted-publishing) It's more secure, and easier to manage, because there's nothing to manage!

Users of GitHub and GRUKE are able to use the NuGet Trusted Publishing functionality directly in their GRUKE projects, *without* using the `NuGet/login` action!

??? note "In regards to GitLab support"

    Support is desired for GitLab, but is much trickier, and will require manual workflow file changes or the use of the workflow generator with a specific flag set. This is due to the fact that [GitLab's OIDC system is part of the pipeline file syntax itself](https://docs.gitlab.com/ci/secrets/id_token_authentication/).

Simply create a Trusted Publishing policy for your repository, on a workflow file that your GRUKE project runs in.
Once you have done so, do one of the following, depending on your setup:

=== "GitHub Actions workflow generator users"
    ```csharp title="Build.cs"
    [GitHubActions(
        ...
        WritePermissions = [ GitHubActionsPermissions.IdToken])]
    class Build : NukeBuild { /* ... */ }
    ```

    Then rerun the generator.
=== "Raw workflow authors"
    ```yml title="myworkflow.yml"
    permissions:
        id-token: write
    ```

Once that's done, you can invoke [`GetNuGetApiKeyWithOpenId`](https://nuke.greemdev.net/docfx/api/Nuke.Common.CI.GitHubActions.GitHubActions.html?q=GitHubActions#Nuke_Common_CI_GitHubActions_GitHubActions_GetNuGetApiKeyWithOpenId_System_String_System_String_System_String_System_Boolean_) or [`GetNuGetApiKeyWithOpenIdAsync`](https://nuke.greemdev.net/docfx/api/Nuke.Common.CI.GitHubActions.GitHubActions.html?q=GitHubActions#Nuke_Common_CI_GitHubActions_GitHubActions_GetNuGetApiKeyWithOpenIdAsync_System_String_System_String_System_String_System_Boolean_) on the [`GitHubActions`](https://nuke.greemdev.net/docfx/api/Nuke.Common.CI.GitHubActions.GitHubActions.html) host instance.

Simply pass the return value to where you were using a NuGet API key before, and as long as the package patterns & names match, and the information you put in on NuGet is accurate, you will have Trusted Publishing working!