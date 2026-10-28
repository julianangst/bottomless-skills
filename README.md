# Bottomless — free Claude skills

6 skills from the [Bottomless catalog](https://botforge-5lo1.onrender.com/en?utm_source=github&utm_campaign=gh-marketplace), free to use.
Each skill has a fixed answer structure and explicit rules against inventing facts.

## Install in Claude Code

```
/plugin marketplace add julianangst/bottomless-skills
/plugin install error-oracle@bottomless
```

## Install in claude.ai

Download the ZIP from [`claude-ai-uploads/`](claude-ai-uploads/) and upload it under
Settings → Capabilities → Skills.

## Skills

| Skill | What it does | Claude Code |
|---|---|---|
| [Error Oracle](https://botforge-5lo1.onrender.com/beispiele/error-oracle-en?utm_source=github&utm_campaign=gh-marketplace) | Paste an error message. Get back which line is to blame and what to do next. | `/plugin install error-oracle@bottomless` |
| [Skill Checker](https://botforge-5lo1.onrender.com/beispiele/skill-pruefer-en?utm_source=github&utm_campaign=gh-marketplace) | You paste a SKILL.md. You get back whether you should install it, before an agent does. | `/plugin install skill-checker@bottomless` |
| [Migration Reviewer](https://botforge-5lo1.onrender.com/beispiele/migrations-pruefer-en?utm_source=github&utm_campaign=gh-marketplace) | You paste your migration. You get back what it locks, for how long, and how to get back if it goes wrong. | `/plugin install migration-checker@bottomless` |
| [Finding Errors in Data Pipelines](https://botforge-5lo1.onrender.com/beispiele/pipeline-diagnose-en?utm_source=github&utm_campaign=gh-marketplace) | You paste your data pipeline's error message. You get back which stage is at fault and what to check next. | `/plugin install pipeline-diagnosis@bottomless` |
| [Query Smith](https://botforge-5lo1.onrender.com/beispiele/query-smith-en?utm_source=github&utm_campaign=gh-marketplace) | Paste your table schema and ask in plain English. Get the matching SQL query. | `/plugin install query-smith@bottomless` |
| [CVE Impact Check](https://botforge-5lo1.onrender.com/beispiele/cve-einordnung-en?utm_source=github&utm_campaign=gh-marketplace) | You paste a CVE report and your stack. You get back whether it affects you — in one sentence, not security jargon. | `/plugin install cve-triage@bottomless` |

## More tools

These six are a sample. The full catalog runs on your own Anthropic key or your own
Claude plan, one-time payment, full source, no account:

- **Paket Agenten- und KI-Entwicklung** — 8 tools, 99 € one-time: https://botforge-5lo1.onrender.com/beispiele/paket-fuer-agenten-und-ki-entwicklung?utm_source=github&utm_campaign=gh-marketplace (page in German)
- **Paket Entwickler: Daten und Infrastruktur** — 9 tools, 99 € one-time: https://botforge-5lo1.onrender.com/beispiele/paket-fuer-entwickler-daten-und-infrastruktur?utm_source=github&utm_campaign=gh-marketplace (page in German)
- **Paket IT-Sicherheit** — 10 tools, 99 € one-time: https://botforge-5lo1.onrender.com/beispiele/paket-fuer-it-sicherheit?utm_source=github&utm_campaign=gh-marketplace (page in German)
- All tools: https://botforge-5lo1.onrender.com/en/werkzeuge?utm_source=github&utm_campaign=gh-marketplace

## Limits

These skills structure and check what you give them. They do not replace a code review,
a security audit or legal advice, and they are not affiliated with Anthropic.

## Provider

Julian Angst, Vienna, Austria · Imprint: https://botforge-5lo1.onrender.com/impressum · Privacy: https://botforge-5lo1.onrender.com/datenschutz

Licence: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/) — free to use and adapt, not for resale.
