# Vibe Stack Builder

Pick your tech stack visually and generate a ready-to-paste prompt for an AI coding assistant (Claude, ChatGPT, Copilot, Cursor, etc.).

Everything lives in a single `index.html` file, so there is nothing to install and nothing to build.

**Live demo:** https://kasamajay.github.io/prompt-generator-app/

## Features

- **Guided stack questions**: system type (Web / Desktop), language, backend and frontend framework (web) or desktop framework (desktop), and database.
- **Smart filtering**: the palette shows only compatible options. Languages are filtered by system type, and frameworks by the chosen language.
- **Drag & drop or click** a technology card to assign it. Hover a card to see a short description.
- **Features & security options** (optional) for the app you're building. Selected options add detailed requirements to the prompt:
  - *Authentication & access:* Sign in with Google, Sign in with GitHub, email + password, magic link, protected pages (web only), roles & permissions, teams & sharing
  - *Security:* security hardening (OWASP baseline), audit log
  - *Project docs:* `docs/decisions/` (ADRs), `docs/diagrams/` (Mermaid), `docs/features/` (specs)
- **Five prompt modes**
  - **Build direct**: asks the AI to scaffold and build the app right away.
  - **Plan first**: asks the AI to produce requirements analysis, architecture, file structure, data model, a roadmap and risks *before* writing any code.
  - **Decision record**: an Architecture Decision Record for a topic you enter, e.g. "Session cookies vs JWT".
  - **Diagram**: Mermaid diagram(s) of a topic, or of the overall architecture.
  - **Feature spec**: user stories, acceptance criteria, UI/API/data changes, security and a test plan for one feature.
- **Fullstack awareness**: if you pick the same framework (e.g. Next.js, Nuxt, SvelteKit) for backend and frontend, the prompt notes that it is used as a fullstack framework.
- **Copy / Regenerate / Reset** controls. The prompt textarea can also be edited by hand before you copy it.
- **Tech Tree view**: an interactive map of Type → Language → Framework, with all supported databases listed below it. Hover a node to highlight its path.

## Getting started

Open `index.html` in any modern browser. You can also serve the folder locally:

```bash
npx serve .
# or
python -m http.server 8000
```

> An internet connection is required for icons, which load from the [Iconify](https://iconify.design) CDN.

## How to use

1. Describe what you're building in **"What are you building?"**
2. Choose **Web** or **Desktop**.
3. For each question, click it and then click or drag a card from the **Technology Palette**.
4. Optionally, toggle **Features & security** options such as Google sign-in, protected pages or teams.
5. Pick a **Prompt mode**. For Decision record, Diagram and Feature spec, also type a topic.
6. Click **Copy prompt** and paste it into your AI assistant.

## Supported technologies

| Category | Options |
|---|---|
| Languages | TypeScript, JavaScript, Python, Java, C#, Go, Rust, PHP, Ruby, C++, Kotlin, Dart |
| Backend (web) | Express, NestJS, Fastify, Hono, Next.js, Nuxt, SvelteKit, Remix, FastAPI, Django, Flask, Spring Boot, .NET/ASP.NET, Gin, Fiber, Axum, Laravel, Rails |
| Frontend (web) | React, Vue, Angular, Svelte, SolidJS, Next.js, Nuxt, SvelteKit, Astro, HTMX |
| Desktop | Electron, Tauri, .NET MAUI, WPF, Avalonia, Qt, JavaFX, Flutter, Compose MP, pywebview, PyQt/PySide, Tkinter, CustomTkinter |
| Databases | PostgreSQL, MySQL, SQLite, MongoDB, Redis, Neon, Supabase, Firestore, Oracle DB, SQL Server, DynamoDB, PlanetScale |

## Customising

All technology data is in the `TECH_DATA` object inside `index.html`. To add a framework, append an entry to the matching list:

```js
{ id:"elysia", name:"Elysia", icon:"logos:bun", languages:["typescript"],
  description:"Bun-first TypeScript web framework with end-to-end type safety." }
```

- `id`: unique within its category. Use lowercase with **no hyphens**.
- `icon`: any [Iconify](https://icon-sets.iconify.design) icon id.
- `languages`: the language ids this framework works with. Languages use `supports:["web","desktop"]` instead.
- `fullstack: true` (optional): adds a "fullstack" badge.

If you add a new **language**, also add it to `WEB_LANG_GROUPS` and/or `DESKTOP_LANG_GROUPS` so it appears in the Tech Tree.

To add a **feature option**, append to `FEATURE_OPTIONS`:

```js
{ id:"twoFactor", group:"auth", name:"Two-factor auth", icon:"ph:device-mobile-bold", auth:true,
  description:"TOTP-based 2FA with recovery codes.",
  lines:[ "Support TOTP two-factor authentication with QR enrolment and one-time recovery codes." ] }
```

- `group` is one of the `FEATURE_GROUPS` ids: `auth`, `security` or `docs`.
- `lines` are the requirement bullets added to the prompt.
- `appliesTo:["web"]` (optional) limits the option to one system type.
- `auth:true` marks a sign-in method, which adds auth-specific deliverables, data-model hints and risks.

## Project structure

```
prompt-generator-app/
├── index.html   # the whole app: styles, markup, data and logic
├── README.md
└── CLAUDE.md    # guidance for Claude Code sessions
```

## License

No license specified yet.
