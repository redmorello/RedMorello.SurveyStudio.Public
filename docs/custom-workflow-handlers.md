# Custom workflow handlers

Survey Studio's Workflows feature runs one or more actions automatically after a form
submission completes — sending a webhook, emailing a notification, alerting on a score
threshold. You can add your own action types from your own project, referencing
`RedMorello.SurveyStudio` as a package reference, without submitting any code into this
package. Once registered, your handler appears in every form's Workflows panel exactly
like a built-in one — no frontend code required.

## What a workflow handler is

A workflow handler is a class implementing `RedMorello.SurveyStudio.Services.Workflows.Handlers.IWorkflowHandler`:

- `Alias`, `Name`, `Description` — identity and labels shown in the Workflows panel's
  type picker.
- `SettingsSchema` — describes the settings fields your handler needs. This drives the
  settings form in the backoffice automatically; you never write frontend code for it.
- `ExecuteAsync` — runs your action for one submission.
- `ValidateSettings` — validates a workflow's settings before it's saved.

## A worked example: posting to Slack

```csharp
using System.Text.Json;
using RedMorello.SurveyStudio.Models.Workflows;
using RedMorello.SurveyStudio.Services.Workflows.Engine;
using RedMorello.SurveyStudio.Services.Workflows.Handlers;

namespace Acme.SurveyStudioExtensions;

public class SlackNotificationHandler(IHttpClientFactory httpClientFactory) : IWorkflowHandler
{
    // Prefix with your own package/vendor name — see "Alias naming" below.
    public string Alias => "acme.slackNotify";
    public string Name => "Post to Slack";
    public string Description => "Posts a message to a Slack channel via an incoming webhook.";

    public IReadOnlyList<HandlerSettingField> SettingsSchema =>
    [
        new HandlerSettingField("webhookUrl", "Slack webhook URL", "text", Required: true),
        new HandlerSettingField("message", "Message template", "textarea", Required: true,
            Help: "Supports {{field:x}}, {{form:x}}, and {{score:x}} tokens."),
        new HandlerSettingField("mentionChannel", "Mention @channel", "boolean"),
    ];

    public IReadOnlyList<string> ValidateSettings(JsonElement settings)
    {
        var errors = new List<string>();
        if (!settings.TryGetProperty("webhookUrl", out var url) || url.GetString() is not { Length: > 0 })
            errors.Add("Slack webhook URL is required.");
        if (!settings.TryGetProperty("message", out var msg) || msg.GetString() is not { Length: > 0 })
            errors.Add("Message template is required.");
        return errors;
    }

    public async Task<WorkflowResult> ExecuteAsync(WorkflowContext ctx, CancellationToken ct = default)
    {
        var webhookUrl = ctx.GetSetting("webhookUrl");
        var text = ctx.ExpandTokens(ctx.GetSetting("message"));
        if (ctx.GetBoolSetting("mentionChannel"))
            text = "@channel " + text;

        var client = httpClientFactory.CreateClient();
        var response = await client.PostAsJsonAsync(webhookUrl, new { text }, ct);

        return response.IsSuccessStatusCode
            ? new WorkflowResult(true, "Posted to Slack.")
            : new WorkflowResult(false, $"Slack returned {(int)response.StatusCode}.");
    }
}
```

## Registering it

Register your handler from your own `IComposer` — Umbraco discovers it automatically
at startup (the same mechanism that bootstraps Survey Studio itself), so nothing else
needs to know it exists:

```csharp
using Umbraco.Cms.Core.Composing;
using Umbraco.Cms.Core.DependencyInjection;
using RedMorello.SurveyStudio.Extensions;

namespace Acme.SurveyStudioExtensions;

public class AcmeWorkflowHandlersComposer : IComposer
{
    public void Compose(IUmbracoBuilder builder)
    {
        builder.AddSurveyStudioWorkflowHandler<SlackNotificationHandler>();
    }
}
```

`AddSurveyStudioWorkflowHandler<T>()` is sugar over
`builder.Services.AddScoped<IWorkflowHandler, T>()` — use the raw call instead if you
prefer, they're equivalent. Either way, your handler is picked up the moment your
project (or the NuGet package you ship it in) is referenced by an Umbraco site running
Survey Studio.

## Alias naming

`Alias` must be unique across every installed package. Prefix yours with your own
package or vendor name (`"acme.slackNotify"`, not `"slackNotify"`) to avoid colliding
with Survey Studio's built-ins (`webhook`, `emailNotification`, `scoreThresholdAlert`)
or another third party's handler.

If two registered handlers do end up sharing an alias, the site does **not** crash: one
handler wins deterministically, and Survey Studio logs an `Error`-level message at
startup naming both colliding handler types — check your logs if a workflow using your
alias isn't behaving the way you expect.

## Settings-schema limitation

`HandlerSettingField.Type` must be one of `text`, `textarea`, `email`, `boolean`,
`select`, or `number` — these are the only field types the Workflows panel currently
renders. There is no way to register a fully custom settings widget (a rich picker, a
code editor, etc.) yet.

## Lifecycle notes

If a workflow references your handler's alias and your assembly is later removed
(uninstalled, or the reference dropped), nothing crashes:

- Creating or updating a workflow with an unknown alias returns a clean validation
  error (`"Unknown handler alias '...'."`), not an exception.
- Running workflows on a submission skips any workflow whose alias no longer resolves
  to a registered handler, logging a warning, and continues running the rest.
