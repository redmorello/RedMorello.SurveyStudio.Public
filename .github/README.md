<picture>
  <source media="(prefers-color-scheme: dark)" srcset="../logos/lockups/surveystudio-lockup-on-red.png">
  <img alt="Survey Studio" src="../logos/lockups/surveystudio-lockup-transparent.png" width="420">
</picture>

[![Downloads](https://img.shields.io/nuget/dt/RedMorello.SurveyStudio?color=cc9900)](https://www.nuget.org/packages/RedMorello.SurveyStudio/)
[![NuGet](https://img.shields.io/nuget/vpre/RedMorello.SurveyStudio?color=0273B3)](https://www.nuget.org/packages/RedMorello.SurveyStudio)
[![GitHub license](https://img.shields.io/github/license/redmorello/RedMorello.SurveyStudio.Public?color=8AB803)](../LICENSE.txt)

Survey Studio adds a full forms and survey builder to the Umbraco backoffice — no external SaaS, no separate SurveyJS license, no iframe embed. Editors build, publish, and review surveys entirely inside the CMS they already use.

## Features

- **Forms & form definitions** — build multi-page surveys, quizzes, and data-capture forms, with versioned definitions so a form can evolve without breaking past submissions.
- **Custom question types & data lists** — define reusable question types and shared option lists (e.g. countries, departments) once, use them across forms.
- **Themes** — apply consistent styling across a form or a whole site's worth of forms.
- **Conditional workflows** — a visual rules engine drives branching logic and conditional behaviour based on answers given.
- **Folders & templates** — organize forms into folders and start new forms from reusable templates.
- **Submissions** — review, filter, and manage submitted responses directly in the backoffice.

## Requirements

- Umbraco CMS 17.4.2+ or 18.x
- .NET 10

## Versioning

Survey Studio's major version tracks the lowest Umbraco major it supports: **17.x** releases support Umbraco 17 and 18. A new major (e.g. 18.0.0) is released only when support for the oldest Umbraco major is dropped.

## Installation

Add the package to an existing Umbraco website from NuGet:

```
dotnet add package RedMorello.SurveyStudio
```

Run the site and add the SurveyStudio section to your User account, then open the **Survey Studio** section in the Umbraco backoffice - no further configuration is required to get started. Add the SurveyStudio Admin user group to your account to enable changing the settings in the backoffice.

Sample forms can be seeded automatically on first run via the `SurveyStudio:SeedSampleData` / `SurveyStudio:SeedSampleForms` configuration options.

While installed, Survey Studio sends a notification roughly once a week (site domain, package/Umbraco versions, license tier) so RedMorello knows where the package is installed - see [LICENSE.txt](../LICENSE.txt) for details. Disable via `SurveyStudio:Telemetry:Enabled: false`.

## reCAPTCHA

Public forms can be protected with Google reCAPTCHA v2 (the "I'm not a robot" checkbox). Create a v2 Checkbox key pair in the [reCAPTCHA admin console](https://www.google.com/recaptcha/admin), with your site's domain(s) added, then set both keys in the `SurveyStudio` section of `appsettings.json`:

```json
"SurveyStudio": {
  "ReCaptcha": {
    "SiteKey": "your-site-key",
    "SecretKey": "your-secret-key"
  }
}
```

Then turn on **Requires reCAPTCHA** in the settings of each form that should use it. The widget is shown above the form, and the response is verified server-side when the first submission is saved. The site key is sent to the browser; the secret key never is - consider keeping it out of source control (e.g. in user secrets or an environment variable such as `SurveyStudio__ReCaptcha__SecretKey`).

reCAPTCHA applies to anonymous public forms only; it isn't checked on forms restricted to logged-in members. If a form requires reCAPTCHA but `SecretKey` isn't set, every submission to that form is rejected, so a missing key can't silently switch the protection off.

## License

Survey Studio is licensed commercial software — see [LICENSE.txt](../LICENSE.txt). A free trial tier is available; visit the Survey Studio dashboard in the backoffice after installing for license status and upgrade options.

To apply a paid license, place the `.lic` file provided by RedMorello at `umbraco/Licenses/SurveyStudio/license.lic`, relative to the site's content root (alongside `appsettings.json`, not `wwwroot`). This path can be changed via the `SurveyStudio:Licensing:LicenseFilePath` configuration option.

## Support

This repository doesn't accept external code contributions — see [CONTRIBUTING.md](CONTRIBUTING.md). Bug reports and feature requests are welcome via [Issues](https://github.com/redmorello/RedMorello.SurveyStudio.Public/issues).

## Acknowledgments

Survey Studio's form designer and rendering engine are built on [SurveyJS](https://surveyjs.io), commercially licensed by RedMorello — see [THIRD-PARTY-NOTICES.md](../THIRD-PARTY-NOTICES.md).
