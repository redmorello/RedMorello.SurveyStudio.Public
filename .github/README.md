# Survey Studio for Umbraco

[![Downloads](https://img.shields.io/nuget/dt/RedMorello.SurveyStudio?color=cc9900)](https://www.nuget.org/packages/RedMorello.SurveyStudio/)
[![NuGet](https://img.shields.io/nuget/vpre/RedMorello.SurveyStudio?color=0273B3)](https://www.nuget.org/packages/RedMorello.SurveyStudio)
[![GitHub license](https://img.shields.io/github/license/redmorello/RedMorello.SurveyStudio?color=8AB803)](../LICENSE.txt)

Survey Studio adds a full forms and survey builder to the Umbraco backoffice — no external SaaS, no separate SurveyJS license, no iframe embed. Editors build, publish, and review surveys entirely inside the CMS they already use.

## Features

- **Forms & form definitions** — build multi-page surveys, quizzes, and data-capture forms, with versioned definitions so a form can evolve without breaking past submissions.
- **Custom question types & data lists** — define reusable question types and shared option lists (e.g. countries, departments) once, use them across forms.
- **Themes** — apply consistent styling across a form or a whole site's worth of forms.
- **Conditional workflows** — a visual rules engine drives branching logic and conditional behaviour based on answers given.
- **Scoring** — automatically score responses for quizzes and assessments.
- **Folders & templates** — organize forms into folders and start new forms from reusable templates.
- **Submissions** — review, filter, and manage submitted responses directly in the backoffice.
- **Multi-tenant ready** — a tenant-aware data layer suits agencies and multi-site installs running many surveys from one Umbraco instance.

## Requirements

- Umbraco CMS 17+
- .NET 10

## Installation

Add the package to an existing Umbraco website from NuGet:

```
dotnet add package RedMorello.SurveyStudio
```

Run the site and add the SurveyStudio section to your User account, then open the **Survey Studio** section in the Umbraco backoffice - no further configuration is required to get started. Add the SurveyStudio Admin user group to your account to enable changing the settings in the backoffice.

Sample forms can be seeded automatically on first run via the `SurveyStudio:SeedSampleData` / `SurveyStudio:SeedSampleForms` configuration options.

On first install, Survey Studio sends a one-time notification (site domain, package/Umbraco versions, license tier) so RedMorello knows where the package is installed - see [LICENSE.txt](../LICENSE.txt) for details. Disable via `Surveys:Telemetry:Enabled: false`.

## License

Survey Studio is licensed commercial software — see [LICENSE.txt](../LICENSE.txt). A free trial tier is available; visit the Survey Studio dashboard in the backoffice after installing for license status and upgrade options.

## Support

This repository doesn't accept external code contributions — see [CONTRIBUTING.md](CONTRIBUTING.md). Bug reports and feature requests are welcome via [Issues](https://github.com/redmorello/RedMorello.SurveyStudio.Public/issues).

## Acknowledgments

Survey Studio's form designer and rendering engine are built on [SurveyJS](https://surveyjs.io), commercially licensed by RedMorello — see [THIRD-PARTY-NOTICES.md](../THIRD-PARTY-NOTICES.md).
