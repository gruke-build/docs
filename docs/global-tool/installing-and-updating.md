---
title: Installing & Updating
---


## Latest stable

=== "Install latest release"

    The script this URL points to is publicly [hosted on GitHub at gruke-build/meta](https://github.com/gruke-build/meta/blob/main/install-global-tool.sh).

    === "bash"
        `curl -Ls https://install-nuke.greemdev.net | bash`

        `-L` causes `curl` to follow redirects, and `-s` performs the operation with no output to stdout. `-L` is required, `-s` is just nice.
    === "zsh"
        `curl -Ls https://install-nuke.greemdev.net | zsh`

        `-L` causes `curl` to follow redirects, and `-s` performs the operation with no output to stdout. `-L` is required, `-s` is just nice.
    === "PowerShell"
        `irm https://install-nuke.greemdev.net | iex`

=== "Update to latest release"

    The script this URL points to is publicly [hosted on GitHub at gruke-build/meta](https://github.com/gruke-build/meta/blob/main/update-global-tool.sh).

    === "bash"
        `curl -Ls https://update-nuke.greemdev.net | bash`

        `-L` causes `curl` to follow redirects, and `-s` performs the operation with no output to stdout. `-L` is required, `-s` is just nice.
    === "zsh"
        `curl -Ls https://update-nuke.greemdev.net | zsh`

        `-L` causes `curl` to follow redirects, and `-s` performs the operation with no output to stdout. `-L` is required, `-s` is just nice.
    === "PowerShell"
        `irm https://update-nuke.greemdev.net | iex`

## Latest pre-release

=== "Install latest pre-release"

    The script this URL points to is publicly [hosted on GitHub at gruke-build/meta](https://github.com/gruke-build/meta/blob/main/install-prerelease-global-tool.sh).

    === "bash"
        `curl -Ls https://install-nuke.greemdev.net/alpha | bash`

        `-L` causes `curl` to follow redirects, and `-s` performs the operation with no output to stdout. `-L` is required, `-s` is just nice.
    === "zsh"
        `curl -Ls https://install-nuke.greemdev.net/alpha | zsh`

        `-L` causes `curl` to follow redirects, and `-s` performs the operation with no output to stdout. `-L` is required, `-s` is just nice.
    === "PowerShell"
        `irm https://install-nuke.greemdev.net/alpha | iex`

=== "Update to latest pre-release"

    The script this URL points to is publicly [hosted on GitHub at gruke-build/meta](https://github.com/gruke-build/meta/blob/main/update-prerelease-global-tool.sh).

    === "bash"
        `curl -Ls https://update-nuke.greemdev.net/alpha | bash`

        `-L` causes `curl` to follow redirects, and `-s` performs the operation with no output to stdout. `-L` is required, `-s` is just nice.
    === "zsh"
        `curl -Ls https://update-nuke.greemdev.net/alpha | zsh`

        `-L` causes `curl` to follow redirects, and `-s` performs the operation with no output to stdout. `-L` is required, `-s` is just nice.
    === "PowerShell"
        `irm https://update-nuke.greemdev.net/alpha | iex`


You can easily upgrade from stable to pre-release, however you cannot upgrade from pre-release to stable *unless you are actively using a pre-release for that version, or an earlier version.*

For example, using the update script while using global tool version 10.5.0-alpha.60, will fail to update to stable before 10.5.0 is released. Once it is released, it can be updated to from the pre-release.