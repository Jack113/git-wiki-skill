---
name: git-wiki
description: Build a developer handover wiki for any git repo. Answers a 12-section checklist by reading the code, suggests extra pages that fit the project (e.g. Docker) and asks before adding them, and asks the user only when confidence is below 70%.
---

# Git Wiki

Goal: answer one question for any repo:
**"If the original developer disappeared tomorrow, could another developer understand, run, modify, test, and deploy this project using the repository and wiki?"**

Two modes:
- **AI mode** (default): read the repo, infer answers, ask only where unsure.
- **Template mode** (user says "template" or "I'll fill it in"): produce the same pages with each question as a heading and a hint of where to look, answers left blank.

## Step 1: Scan the repo

Read before writing. Check, in this order:

1. `README*`, `CONTRIBUTING*`, `CHANGELOG*`, `docs/`, existing wiki, `adr/` or `decisions/` folders
2. Manifests and versions: `package.json`, `*.csproj`, `*.sln`, `pyproject.toml`, `requirements*.txt`, `go.mod`, `Cargo.toml`, `pom.xml`, `Gemfile`, `.nvmrc`, `.tool-versions`, `global.json`
3. Config and env: `.env.example`, `appsettings*.json`, `config/`, secrets references
4. Folder tree (2-3 levels deep) and entry points (`main`, `Program`, `index`, `app`, `server`)
5. Data: models/entities, ORM config, `migrations/`, seed scripts, SQL files
6. Interfaces: routes, controllers, OpenAPI/Swagger files, GraphQL schemas, queues
7. Auth: middleware, guards, policies, role enums, identity config, admin seeding
8. Tests: test folders, test config, coverage settings
9. CI/CD and hosting: `.github/workflows/`, `azure-pipelines.yml`, `.gitlab-ci.yml`, `Jenkinsfile`, IaC folders
10. Conventions: linter/formatter config, `CODEOWNERS`, PR templates, commit message style
11. History: `git log --oneline -50`, `git branch -a`, tags, most-changed files (to spot fragile areas)
12. Debt signals: `TODO`, `FIXME`, `HACK`, `@deprecated`, skipped tests

For large repos, sample representative files instead of reading everything.

## Step 2: Suggest extra pages

The 12 core pages always get written. On top of that, look for things in this project that deserve their own page. Examples:

| If the repo has | Suggest a page |
|---|---|
| `Dockerfile`, `docker-compose*`, `devcontainer.json` | Docker |
| `k8s/`, `helm/`, `terraform/`, `*.bicep` | Infrastructure |
| Queues, workers, cron, hosted services | Background Jobs |
| Feature flag library or config | Feature Flags |
| Payment, email, SMS or other external APIs | Integrations |
| Logging/monitoring setup (Serilog, Sentry, App Insights...) | Monitoring and Logs |
| `i18n/`, resource files | Translations |
| Mobile app, CLI, or second frontend | One page per app |
| Anything else unusual or complex | A page named after it |

This table is a starting point, not a limit. Suggest whatever fits the project.

Rules:
- **Always ask before adding.** List the suggested pages with one line on why each fits, and let the user pick (use AskUserQuestion with multiSelect when available).
- Also ask if they want any page that wasn't suggested.
- Only write pages the user chose. If the user isn't present, skip extra pages and list them in `_Open-Questions.md`.
- Write each extra page in the same style as the core pages: questions as headings, sources, confidence flags.

## Step 3: Answer the checklist

For each question record: the answer, the **evidence** (file paths), and a **confidence** score (0-100%).

1. **What is this project?** Problem solved · audience · main features · in/out of scope
2. **How does it work?** High-level architecture · main components/services · how they communicate · normal app flow
3. **What technologies does it use?** Languages/frameworks · database · hosting/cloud · key third-party libraries/services · required versions
4. **How do I run it locally?** Prerequisites · clone and configure · required env vars · database setup · start command · how to tell it's working
5. **How is the repo organised?** What each major folder holds · where new code goes · where models, services, APIs, UI, tests, migrations live
6. **Authentication and authorization?** Login method · roles/permissions · which areas need which permissions · how the first admin is created
7. **Data model?** Key entities · relationships · business rules · how migrations are handled
8. **APIs/interfaces?** Endpoints · inputs · outputs · auth · common error responses
9. **Important workflows?** How users complete main tasks · what happens behind the scenes · states an item moves through · edge cases
10. **Develop and contribute?** Branch strategy · coding conventions · commit/PR structure · checks before merge · how to add a feature
11. **Testing and deployment?** Running tests · test types · what happens on push · how migrations deploy · where it's hosted · troubleshooting failed deploys
12. **Before changing something?** Fragile areas · dependency impact · known limitations · tech debt · past decisions and why · planned features

### Confidence guide
- **90-100%**: stated directly in code, config or docs
- **70-89%**: strongly implied by code (e.g. a role enum plus `[Authorize]` attributes)
- **Below 70%**: guessed, conflicting or missing → **ask**

Code usually can't tell you: audience, scope, planned features, why decisions were made, branch strategy (if history is unclear), production hosting details, real secret values.

## Step 4: Ask only where unsure

If any answer is below 70% confidence:
- Group all such questions into **one batch** (use AskUserQuestion when available, up to 4 at a time; otherwise a short numbered list).
- Show your best guess for each so the user can confirm or correct it.
- Don't ask about anything at 70% or above.
- If the user can't answer or isn't present, keep the best guess and mark it **⚠️ Needs confirmation**.
- Never invent secrets, URLs or credentials. Write `<ask team>` instead.

## Step 5: Write the wiki

Default location: `wiki/` in the repo root (or `docs/wiki/` if `docs/` exists). Ask before overwriting existing docs.

```
wiki/
  Home.md                   # summary, quick start, links to all pages, handover score
  01-Overview.md
  02-Architecture.md        # Mermaid diagram of components
  03-Tech-Stack.md          # table: tech | version | purpose
  04-Local-Setup.md         # copy-paste commands, env var table
  05-Repo-Structure.md      # annotated folder tree
  06-Auth.md                # roles → permissions table
  07-Data-Model.md          # Mermaid ER diagram
  08-APIs.md                # table: method | path | auth | purpose
  09-Workflows.md           # Mermaid state/sequence diagrams
  10-Contributing.md
  11-Testing-and-Deployment.md
  12-Before-You-Change.md   # fragile areas, debt, decisions, roadmap
  _Open-Questions.md        # everything still marked Needs confirmation
```

Extra pages the user chose (e.g. `Docker.md`) go in the same folder and are linked from `Home.md`.

Page format:
- Each checklist question gets its own heading.
- Plain language, short sentences, no filler.
- End each answer with `Source: path/to/file`.
- Mark low-confidence items `⚠️ Needs confirmation`.
- File names follow GitHub/GitLab/Azure DevOps wiki conventions, so the folder can be pushed to a repo wiki as-is.

## Step 6: Handover check

In `Home.md`, score the five abilities (**understand, run, modify, test, deploy**) as ✅ covered, ⚠️ partial or ❌ missing, with one line on what would close each gap. Finish by telling the user the score and the top 3 gaps.

## Updating an existing wiki

If the wiki already exists, compare it with the code changed since the wiki was last edited (`git log`), update only stale sections, and list what changed.
