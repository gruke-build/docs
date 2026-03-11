# Frequently Asked Questions

On this page you can find a collection FAQ questions from [NUKE's FAQ](https://nuke.build/faq/), 
with small modifications specific to this fork.

## General

??? question "Why is it called GRUKE/NUKE?"
    The name "GRUKE" is a portmanteau of the name of the maintainer of this fork, [GreemDev](https://github.com/GreemDev/), and NUKE.

    The name "NUKE" originates from a combination of the words New and Make.

    GNU Make can be considered the godfather of build automation tools that is based on the commonly known makefile.

    NU is a play on the word *New*, similar as in *Nu-Metal*. 
    It also breaks the common naming pattern to be [AKEless](https://www.youtube.com/watch?v=_sZT0CpJ6Vo&t=216s); 
    as in "working without ache/pain" - since it doesn't come with the scripting heritage most developers expect from MAKE tools. 
    Instead, it fully integrates with IDEs and other existing tooling, and makes build automation a breeze.

??? question "Can I use GRUKE in places other than .NET?"
    Absolutely!

    We've heard plenty of stories where developers choose NUKE to kick off and extend their JavaScript/TypeScript or Gradle builds.

    Although pulling in the .NET SDK can be seen as an unnecessary dependency for the project, the gained comfort quickly outweighs this penalty.

    Ultimately, it's up to you to decide if the tradeoff is worth it. The answer is yes, however.

??? question "How does GRUKE compare to [MSBuild](https://learn.microsoft.com/en-gb/visualstudio/msbuild/msbuild?view=visualstudio)?"
    While [MSBuild](https://learn.microsoft.com/en-gb/visualstudio/msbuild/msbuild?view=visualstudio) is 
    a powerful build system and definitely worth exploring for some use-cases,
    it falls rather short when it comes to general build automation.

    The XML format makes it very verbose even for medium complex logic. 
    Writing custom tasks in C# is a possible but not practical due to the maintenance costs and cumbersome debugging.

    However, GRUKE doesn't replace MSBuild. Using the 
    [`DotNetTasks`](https://nuke.greemdev.net/docfx/api/Nuke.Common.Tools.DotNet.DotNetTasks.html) and
    [`MSBuildTasks`](https://nuke.greemdev.net/docfx/api/Nuke.Common.Tools.MSBuild.MSBuildTasks.html),
    it is still used to compile solutions and projects.

    Our rule of thumb: when something is closely related to the compilation process and involved files, 
    you should use MSBuild. When it's not, like packaging the output files, you could use GRUKE.

??? question "How does GRUKE compare to [YAML](https://yaml.org/)?"
    Using YAML can be absolutely fine in many scenarios, 
    particularly when you're sure that your build remains simple long-term.
    Once it gets more complex though, you will quickly realize that common control flow elements 
    like if-statements or foreach-loops have no home in YAML files. 

    In addition, you will fight some other [YAML issues](https://noyaml.com/), like repetitive formatting errors, inability for local troubleshooting, and missing refactoring tools.

    NUKE doesn't replace YAML files though. Your respective CI/CD service still needs a YAML file (or similar configuration files), to invoke your NUKE build.

??? question "How does GRUKE compare to other build tools?"
    We encourage everyone to try and evaluate any of the other build automation options that exist in the .NET ecosystem. Some unique NUKE features from our point of view are:

    - Targets (build steps) are declared as properties
    - Common build automation helper functions included
    - Consistent fluent API for all the major CLI tools
    - Declarative build parameters (input values)
    - Build sharing and build composition
    - Generation of CI/CD configuration files (YAML, etc.)
    - Native integration into IDEs with additional extensions

    You can also read comparative articles by 
    [Dennis Doomen](https://www.continuousimprover.com/2020/03/reasons-for-adopting-nuke.html) or
    [Laurent Kempé](https://laurentkempe.com/2022/02/02/automate-your-dotnet-project-builds-with-nuke-a-cross-platform-build-automation-solution/).

## Build Structure

??? question "Do I really need the `build.cmd`, `build.ps1`, and `build.sh` files?"
    Not necessarily. It's perfectly fine to remove them and call dotnet run on the build project. However, there are two situations when these files are really required:

    1. They ensure the availability of the .NET SDK according to the global.json file.
        - If there is no local installation, it will be downloaded to the temporary directory.
        - This greatly simplifies execution on CI/CD environments and for anyone trying to contribute to your project.
    2. They allow the Visual Studio and Visual Studio Code extensions to execute the build.

??? question "Do I really need the `.nuke` directory and contained files?"
    The `.nuke` directory allows to infer the root directory (i.e., `NukeBuild.RootDirectory`). 
    Alternatively, you can invoke the build with the `--root` flag to indicate that the working directory should be used,
    or `--root <path>` to pass another directory.

    The `build.schema.json` file is generated on each build execution. 
    If you have configured [shell completion](https://nuke.greemdev.net/release/global-tool/shell-completion/), this file is used to provide completion results. 
    If the file is not present, the first completion attempt (i.e., when hitting `TAB`) needs to compile your build project. Therefore, you only need to commit this file if you prefer immediate completion behavior.

## Build Execution

??? question "Does every project contributor need to install the global tool?"
    No. The global tool is only required to [set up a build](https://nuke.greemdev.net/release/getting-started/setup/). Contributors can use the following commands to execute the build:

    === ".NET CLI"
        ```
        dotnet run -- [args]
        ```

    === "Windows"
        ```
        .\build.cmd [args]
        ```

    === "Linux"
        ```
        ./build.sh [args]
        ```

??? question "Why does my tool invocation log errors, although it's successful?"
    Whether a tool invocation is treated as an error depends solely on its exit code. Only non-zero exit codes will fail the build. 
    Some tools are notoriously logging to the error stream while the outcome is still successful.
    Most commonly, this includes Git and Docker. You can overwrite the tool-specific logger in your `Build` constructor and
    delegate to any of the [logging methods](https://nuke.greemdev.net/release/fundamentals/logging/):

    === "Git"
        ```csharp title="Build.cs"
        GitTasks.GitLogger = (type, text) => Log.Debug(text);
        ```

    === "Docker"
        ```csharp title="Build.cs"
        DockerTasks.DockerLogger = (type, text) => Log.Debug(text);
        ```

??? question "Why does my build output show hieroglyphs?"
    You're most likely running the build in a terminal that isn't set to UTF-8 output. We can't give any more help here,
    but recommend to check on a suitable [StackExchange](https://stackexchange.com/sites) platform or ask our community on Discord:

    [![Discord](https://img.shields.io/discord/405806471578648588?style=for-the-badge&color=5B65EA)](https://discord.gg/H8bcFr2)


## Public API

??? question "Why was the public API broken in a minor release?"
    Major versions are released alongside major .NET SDK releases. 
    This is to reflect that our releases are compatible with a specific .NET SDK version. 
    As a consequence, breaking changes may and will happen during minor releases for various reasons. 
    Most often, it is to improve an API, fix legacy mistakes, or update a tool task to a new version.

    If you can no longer find an API, proceed as follows:

    - Check the changelog
    - Try to fuzzy search in your IDE
    - Reach out to the community on [Discord](https://discord.gg/H8bcFr2)

    Please remember that this is an open-source project.

??? question "Can you provide migration guides?"
    Most likely not, unless it is fundamental and would affect all users. 
    It is in the interest of the project to dedicate our available time to implementing cool new features.

    Please remember that this is an open-source project.

## Misc

??? question "How can I restore packages from a private NuGet feed?"
    If you need to restore a private NuGet package as part of the build project compilation (as in, it's a dependency of your build project), 
    you have to provide the feed password in your build environment and adapt the bootstrapping scripts as follows:

    === "PowerShell"
        ```powershell title="build.ps1" hl_lines="7 8 16 17 18"
        ###########################################################################
        # CONFIGURATION
        ###########################################################################

        ...

        $env:FEED_SOURCE = "https://nuget.pkg.github.com/nuke-build/index.json"
        $env:FEED_USERNAME = "nuke-bot"

        ###########################################################################
        # EXECUTION
        ###########################################################################

        ...

        if (Test-Path env:FEED_PASSWORD) {
            ExecSafe { & $env:DOTNET_EXE nuget add source $env:FEED_SOURCE --username $env:FEED_USERNAME --password $env:FEED_PASSWORD }
        }

        Write-Output "Microsoft (R) .NET Core SDK version $(& $env:DOTNET_EXE --version)"

        ExecSafe { & $env:DOTNET_EXE build $BuildProjectFile /nodeReuse:false /p:UseSharedCompilation=false -nologo -clp:NoSummary --verbosity quiet }
        ExecSafe { & $env:DOTNET_EXE run --project $BuildProjectFile --no-build -- $BuildArguments }
        ```

    === "Bash"
        ```bash title="build.sh" hl_lines="7 8 16 17 18"
        ###########################################################################
        # CONFIGURATION
        ###########################################################################

        ...

        export FEED_SOURCE="https://nuget.pkg.github.com/nuke-build/index.json"
        export FEED_USERNAME="nuke-bot"

        ###########################################################################
        # EXECUTION
        ###########################################################################

        ...

        if [[ ! -z ${FEED_PASSWORD+x} && "$FEED_PASSWORD" != "" ]]; then
            "$DOTNET_EXE" nuget add source "$FEED_SOURCE" --username "$FEED_USERNAME" --password "$FEED_PASSWORD" --store-password-in-clear-text
        fi

        echo "Microsoft (R) .NET SDK version $("$DOTNET_EXE" --version)"

        "$DOTNET_EXE" build "$BUILD_PROJECT_FILE" /nodeReuse:false /p:UseSharedCompilation=false -nologo -clp:NoSummary --verbosity quiet
        "$DOTNET_EXE" run --project "$BUILD_PROJECT_FILE" --no-build -- "$@"
        ```

    !!! info
        If your packages are not required for the build project, it is a better idea to add the source from within the build project.