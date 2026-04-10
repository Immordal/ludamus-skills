---
name: ludamus
description: >
  Manage RPG game sessions in the Ludamus event app (zagrajmy.net). Use this skill whenever
  the user wants to browse sessions, sign up for a game, drop out, propose a session, or
  write a session pitch. Triggers on: "what sessions are available", "show me the games",
  "sign me up for", "drop out of", "I want to propose a session", "help me write a pitch",
  "zgłoś sesję", "zapisz mnie na", "na co jestem zapisany", "co powinienem poprowadzić".
user-invocable: true
disable-model-invocation: false
allowed-tools: WebFetch, Read, Glob
---

# Ludamus — Session Guide

You help users manage RPG sessions at zagrajmy.net — a Django server-rendered site.
All interactions are plain HTTP GET/POST requests.

## Environments

| | URL |
|---|---|
| Production | `https://zagrajmy.net` |
| Event subdomain | `https://<event>.zagrajmy.net` |
| Local dev | `http://localhost:8000` |

Default to production unless the user says "locally" or "localhost".

## Step 0 — Identify the event

If the user hasn't specified an event, GET `<base>/` and look for upcoming events.
If there's exactly one, use it. If multiple, list them and ask. Extract the slug from
URLs like `/chronology/event/<slug>/`.

## CSRF tokens

Before every POST, GET the target page and extract the CSRF token from the form:
```html
<input type="hidden" name="csrfmiddlewaretoken" value="...">
```
Include it in every POST body as `csrfmiddlewaretoken=<token>`.

---

## Browse sessions

GET `<base>/chronology/event/<slug>/`

Parse `<article>` cards. For each, collect title, host, spots available, and status.

```
Sessions at <Event Name>

1. <Title>
   Host: <name> · <spots>

2. <Title>
   Host: <name> · FULL (N waiting)

[N sessions total]
```

---

## Sign up for a session

1. GET `<base>/chronology/session/<session_id>/enrollment/` — read current status and extract CSRF token.
2. Confirm with user: "Sign you up for **<Title>**? [yes/no]"
3. POST to the same URL:
   ```
   csrfmiddlewaretoken=<token>&enrollment_myself=enroll
   ```
   Use `enrollment_myself=waitlist` if the session is full.

---

## Drop out of a session

1. GET `<base>/chronology/session/<session_id>/enrollment/` — confirm enrollment and extract CSRF token.
2. Confirm: "Drop out of **<Title>**? [yes/no]"
3. POST to the same URL:
   ```
   csrfmiddlewaretoken=<token>&enrollment_myself=cancel
   ```

---

## Propose a session

1. GET `<base>/chronology/event/<slug>/session/propose/` — extract CSRF token and available categories/time slots.
2. Collect from user: title, description, presenter name, max players, time slot, min age (optional).
3. Confirm with user before submitting.
4. Walk through the multi-step wizard via sequential POSTs — each step returns the next step's HTML (HTMX, include `HX-Request: true`):
   - Step 1: `csrfmiddlewaretoken=<token>&category=<id>`
   - Step 2: `csrfmiddlewaretoken=<token>&time_slots=<id>`
   - Step 3: `csrfmiddlewaretoken=<token>&title=<title>&description=<desc>&participants_limit=<n>&display_name=<name>&min_age=<age>`
   - Step 4: final review — POST to submit.

---

## Write a pitch

Help the user write a session pitch before proposing it.

1. If the user has a game in mind, ask what it is. Otherwise use `Glob` to search `~/Documents`, `~/Downloads`, `~/RPG` for PDFs and suggest options.
2. If a PDF is found, use `Read` to extract: system, tone, scenario hooks, player count, content warnings.
3. Ask only what the file didn't answer (scenario, tone, warnings, player count).
4. Write a draft pitch (100–200 words):
   - Hook first — start with a situation, not a system name
   - End with a question or stakes
   - List content warnings honestly
5. Iterate until the user is happy, then hand off to "Propose a session".

---

## Error handling

| Response | Say |
|---|---|
| Redirect to login / "Login Required" | "You need to be logged in. Log in at zagrajmy.net first, then try again." |
| HTTP 403 | "You don't have permission to do that." |
| HTTP 404 | "That page doesn't exist." |
| Form validation errors | Read the error text, rephrase naturally. |
| Connection refused (localhost) | "The local server isn't running — start it with `mise run start`." |
