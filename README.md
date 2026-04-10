# ludamus-skills

A [Claude Code skill](https://docs.anthropic.com/en/docs/claude-code/skills) for managing RPG game sessions on [zagrajmy.net](https://zagrajmy.net) (Ludamus), a Django-based convention event app.

Since Ludamus has no REST API yet, the skill operates via direct HTTP requests — scraping CSRF tokens from rendered forms and posting form data. Once an API exists, the skill will switch to JSON calls; user-facing behavior stays the same.

## Install

```sh
npx skills add https://github.com/Immordal/ludamus-skills
```

## What it can do

| Capability | Example prompt |
|---|---|
| Browse sessions | "What sessions are available at O2F?" |
| Sign up / join waitlist | "Sign me up for the Call of Cthulhu session" |
| Drop out | "Cancel my spot in the afternoon slot" |
| Write a session pitch | "Help me write a pitch for running Delta Green" |
| Propose a session | "I want to propose a session for O2F" |
| Manage encounters | "Create a side game meetup for Saturday evening" |
| Edit profile | "Show my profile" / "Update my connected users" |

When writing pitches, the skill can read local PDF game books to pull in scenario hooks, tone, player count, and content warnings automatically.

## Supported environments

| Environment | Base URL |
|---|---|
| Production | `https://zagrajmy.net` |
| Event subdomain | `https://<event>.zagrajmy.net` |
| Local dev | `http://localhost:8000` |

## Authentication

The app uses Auth0. If you're not logged in, the skill will prompt you to log in via the browser before performing any write action.

## Notes

- Works on any event hosted on zagrajmy.net — just tell the skill the event name or paste a URL.
- Direct HTTP calls will be replaced with JSON API calls once an API is available; user-facing behavior will stay the same.

## License

MIT
