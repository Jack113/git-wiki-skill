# git-wiki

A Claude skill that turns any git repo into a developer handover wiki. It reads the code to answer 12 key questions (setup, architecture, auth, data, APIs, deployment and more) and only asks you when it's unsure.

**The goal:** if the original developer disappeared tomorrow, could someone else understand, run, change, test and deploy the project?

## What it does

1. **Reads the repo**: docs, config, code, migrations, routes, CI/CD and git history.
2. **Answers 12 core questions**, each with source files and a confidence score.
3. **Asks you** only when it's less than 70% sure, showing its best guess.
4. **Suggests extra pages** that fit your project (Docker, Background Jobs, Integrations...) and asks which ones you want.
5. **Writes a `wiki/` folder** that works as-is in GitHub, GitLab or Azure DevOps wikis.
6. **Scores the handover**: understand, run, modify, test, deploy.

## The 12 core pages

| # | Page | Answers |
|---|---|---|
| 1 | Overview | What is it, who is it for, what's in scope |
| 2 | Architecture | Components and how they connect |
| 3 | Tech Stack | Languages, database, hosting, versions |
| 4 | Local Setup | Prerequisites, env vars, how to run it |
| 5 | Repo Structure | What's in each folder, where new code goes |
| 6 | Auth | Login, roles, permissions, first admin user |
| 7 | Data Model | Entities, relationships, rules, migrations |
| 8 | APIs | Endpoints, inputs, outputs, errors |
| 9 | Workflows | Main tasks, states, edge cases |
| 10 | Contributing | Branches, conventions, PRs, checks |
| 11 | Testing and Deployment | Tests, CI/CD, hosting, troubleshooting |
| 12 | Before You Change | Fragile areas, tech debt, decisions, roadmap |

## Install

**Claude Code (recommended):**
```
/plugin marketplace add Jack113/git-wiki-skill
/plugin install git-wiki@jack113-skills
```
Update later with `claude plugin update git-wiki@jack113-skills`.

**Claude app (claude.ai / desktop):** download `git-wiki.zip` from this repo, then upload it under Settings → Capabilities → Skills.

## Use

Open or attach a repo, then say:

- `Build a wiki for this repo` – full AI mode
- `Build a wiki template for this repo` – blank pages for a human to fill in
- `Update the wiki` – refreshes only the sections that are out of date

## Contributing

Ideas for better questions or extra page types are welcome. Open an issue or PR.

## License

MIT
