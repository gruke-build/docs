---
title: Chats & Social Media
---

As a final step of your build automation process, you may want to report errors or announce a new version through different chats and social media channels. NUKE comes with basic support for the most common platforms.

=== "Slack"

    You can send a [Slack](https://slack.com/) messages as follows:

    ```csharp
    // using static Nuke.Common.Tools.Slack.SlackTasks;
    
    [Parameter] [Secret] readonly string SlackWebhook;
    
    Target Send => _ => _
        .Executes(async () =>
        {
            await SendSlackMessageAsync(_ => _
                    .SetText("Hello from NUKE!"),
                SlackWebhook);
        });
    ```

    !!! note
        For more advanced scenarios, check out [SlackAPI](https://github.com/Inumedia/SlackAPI) or [SlackNet](https://github.com/soxtoby/SlackNet).

=== "Microsoft Teams"

    You can send a [Microsoft Teams](https://www.microsoft.com/en/microsoft-teams/group-chat-software) messages as follows:

    ```csharp
    // using static Nuke.Common.Tools.Teams.TeamsTasks;
    
    [Parameter] [Secret] readonly string TeamsWebhook;
    
    Target Send => _ => _
        .Executes(async () =>
        {
            await SendTeamsMessageAsync(_ => _
                    .SetText("Hello from NUKE!"),
                TeamsWebhook)
        });
    ```

=== "Twitter"

    You can send a [Twitter](https://twitter.com/) messages as follows:

    ```csharp
    // using static Nuke.Common.Tools.Twitter.TwitterTasks;

    [Parameter] [Secret] readonly string TwitterConsumerKey;
    [Parameter] [Secret] readonly string TwitterConsumerSecret;
    [Parameter] [Secret] readonly string TwitterAccessToken;
    [Parameter] [Secret] readonly string TwitterAccessTokenSecret;

    Target Send => _ => _
        .Executes(async () =>
        {
            await SendTweetAsync(
                message: "Hello from NUKE",
                TwitterConsumerKey,
                TwitterConsumerSecret,
                TwitterAccessToken,
                TwitterAccessTokenSecret);
        });
    ```

    !!! note
        For more advanced scenarios, check out the [Tweetinvi](https://github.com/linvi/tweetinvi) project.


=== "Gitter"

    You can send a [Gitter](https://gitter.im/) messages as follows:

    ```csharp
    // using static Nuke.Common.Tools.Gitter.GitterTasks;
    
    [Parameter] readonly string GitterRoomId;
    [Parameter] [Secret] readonly string GitterAuthToken;
    
    Target Send => _ => _
        .Executes(() =>
        {
            SendGitterMessage(
                message: "Hello from NUKE",
                GitterRoomId,
                GitterAuthToken);
        });
    ```

    !!! note
        For more advanced scenarios, check out the [gitter-api-pcl](https://github.com/uwp-squad/gitter-api-pcl) project.
